模型加速与服务化实战笔记

先把推理效率提上来，再把模型包装成可调用的在线服务，这套组合在实际项目里很常见。下面用一个 BERT 基础模型演示如何导出 ONNX、做校验与推理，以及用动态量化换取速度提升，最后把 DistilBERT 情感分类模型用 FastAPI 暴露成 REST 接口并容器化。

导出与验证环节把模型转成 ONNX 格式，输入名设定为 input_ids、attention_mask、token_type_ids，输出名为 logits，opset 采用 12，动态轴对 batch_size 开放，便于不同批量推理。导出完成后用 onnx.checker.check_model 做结构校验，通过后再用 ONNX Runtime 加载同一文件进行前向，借助 to_numpy 把 PyTorch 张量转成 NumPy 喂给会话，得到与原模型相近的 logits。

量化部分使用 PyTorch 的动态量化接口 quantize_dynamic，目标只覆盖 torch.nn.Linear，量化位宽设为 INT8。量化后模型仍回到设备上推理，对比可以看到数值几乎一致，推理耗时从 0.0241 秒降到 0.0153 秒，实测加速约 1.58 倍。

[PRE0]

当前设备: cuda

加载预训练模型和分词器: bert-base-uncased

输入文本: This is an example text for inference acceleration.

导出模型为 ONNX 格式: bert_base_uncased.onnx

验证 ONNX 模型...

ONNX 模型验证通过

使用 ONNX Runtime 进行推理...

ONNX 推理输出: [[ 0.5379177 -0.305843 ]]

进行动态量化...

使用量化模型进行推理...

量化模型输出: tensor([[ 0.5361, -0.3051]], device='cuda:0')

原始模型推理时间: 0.0241 秒

量化模型推理时间: 0.0153 秒

加速比: 1.58 倍

服务化时首选 FastAPI，依赖安装很直接。

[PRE1]

pip install fastapi uvicorn transformers torch

首个示例把 DistilBERT 微调版 distilbert-base-uncased-finetuned-sst-2-english 挂到 GPU 上，定义 TextRequest 与 TextResponse 模型，/analyze 接口校验输入非空后做分词、截断与填充，推理得到 logits 再经 softmax 转化为概率，标签映射为 negative 与 positive，返回情感与置信度。服务标题与描述也一并配置，启动方式通过 uvicorn 完成。

[PRE2]python

from fastapi import FastAPI, HTTPException

from pydantic import BaseModel

from transformers import AutoTokenizer, AutoModelForSequenceClassification

import torch

# 检查 CUDA 设备

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

print(f"当前设备: {device}")

# 加载预训练模型和分词器

model_name = "distilbert-base-uncased-finetuned-sst-2-english"

tokenizer = AutoTokenizer.from_pretrained(model_name)

model = AutoModelForSequenceClassification.from_pretrained(

    model_name).to(device)

print(f"已加载模型: {model_name}")

# 定义 FastAPI 应用

app = FastAPI(title="情感分析 API",

    description="基于 FastAPI 的 RESTful 接口实现情感分析", version="1.0")

# 定义请求体

class TextRequest(BaseModel):

    text: str

# 定义响应体

class TextResponse(BaseModel):

    sentiment: str

    confidence: float

# 定义 API

@app.post("/analyze", response_model=TextResponse)

async def analyze_sentiment(request: TextRequest):

    # 获取输入文本

    input_text = request.text

    if not input_text:

        raise HTTPException(status_code=400, detail="输入文本不能为空")

    # 文本编码

    inputs = tokenizer(input_text, return_tensors="pt", truncation=True,

        padding=True, max_length=128).to(device)

    # 模型推理

    with torch.no_grad():

        outputs = model(**inputs)

        logits = outputs.logits

        probabilities = torch.nn.functional.softmax(logits, dim=-1)

    # 解析结果

    labels = ["negative", "positive"]

    predicted_index = torch.argmax(probabilities, dim=1).item()

    sentiment = labels[predicted_index]

    confidence = probabilities[0][predicted_index].item()

    return TextResponse(sentiment=sentiment, confidence=confidence)

# 启动服务

# 使用以下命令运行服务: uvicorn script_name:app --reload

if __name__ == "__main__":

    import uvicorn

    uvicorn.run(app, host="0.0.0.0", port=8000)

本地热更新启动命令如下，文档自动生成在对应地址。

[PRE3]

uvicorn script_name:app --reload

[PRE4]

http://127.0.0.1:8000/docs

请求体示例与返回示例保持一致，方便前端对接。

[PRE5]json

{
    "text": "I absolutely love this product! It's fantastic."
}

[PRE6]json

{
    "sentiment": "positive",
    "confidence": 0.987
}

运行日志显示模型已在 cuda 加载，Uvicorn 成功监听 0.0.0.0:8000，并记录一次成功的 POST 请求。

[PRE7]

当前设备: cuda

已加载模型: distilbert-base-uncased-finetuned-sst-2-english

INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)

INFO:     Started reloader process [12345] using statreload

INFO:     Started server process [12346]

INFO:     Waiting for application startup.

INFO:     Application startup complete.

INFO:     127.0.0.1:60000 - "POST /analyze HTTP/1.1" 200 OK

为了简化部署，还提供了另一版精简实现，接口路径改为 /predict，逻辑等价。

[PRE8]

pip install fastapi uvicorn transformers torch

[PRE9]python

from fastapi import FastAPI, HTTPException

from pydantic import BaseModel

from transformers import AutoTokenizer, AutoModelForSequenceClassification

import torch

# 加载模型和分词器

model_name = "distilbert-base-uncased-finetuned-sst-2-english"

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

tokenizer = AutoTokenizer.from_pretrained(model_name)

model = AutoModelForSequenceClassification.from_pretrained(

    model_name).to(device)

# 创建 FastAPI 实例

app = FastAPI()

# 请求体

class TextRequest(BaseModel):

    text: str

# 响应体

class TextResponse(BaseModel):

    sentiment: str

    confidence: float

@app.post("/predict", response_model=TextResponse)

async def predict(request: TextRequest):

    text = request.text

    if not text:

        raise HTTPException(status_code=400, detail="输入文本不能为空")

    # 文本预处理

    inputs = tokenizer(text, return_tensors="pt", truncation=True,

        padding=True, max_length=128).to(device)

    # 模型推理

    with torch.no_grad():

        outputs = model(**inputs)

        probabilities = torch.nn.functional.softmax(outputs.logits, dim=-1)

        label = torch.argmax(probabilities, dim=1).item()

        confidence = probabilities[0][label].item()

    sentiment = "positive" if label == 1 else "negative"

    return TextResponse(sentiment=sentiment, confidence=confidence)

# 运行服务

if __name__ == "__main__":

    import uvicorn

    uvicorn.run(app, host="0.0.0.0", port=8000)

直接执行脚本即可拉起服务。

[PRE10]

python app.py

容器化采用 python:3.9-slim 镜像，先复制 requirements 再复制源码，最后通过 uvicorn 启动。

[PRE11]dockerfile

# 基础镜像

FROM python:3.9-slim

# 设置工作目录

WORKDIR /app

# 安装依赖

COPY requirements.txt .

RUN pip install -r requirements.txt

# 复制代码

COPY . .

# 启动服务

CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]

依赖清单包含核心四件套。

[PRE12]

fastapi

uvicorn

torch

transformers