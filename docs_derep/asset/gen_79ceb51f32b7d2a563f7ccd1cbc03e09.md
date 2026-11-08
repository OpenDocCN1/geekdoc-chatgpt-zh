# 企业问答系统生产落地全流程

本文把一套基于 Flask 与 Transformers 的内部问答服务，从代码拉取到线上验证的完整路径拆开说明。核心思路是先在服务器上准备运行环境，再用轻量 API 承接上下文和问题调用本地大模型，最后借助 Docker 与 Nginx 做容器化与反向代理，并辅以向量检索与配置解耦提升可维护性。

先把源码和 Python 依赖拉到生产机，创建统一的工作目录。
[PRE0]bash

# 在生产服务器上,克隆代码仓库并安装依赖

git clone https://github.com/your-repo/enterprise-qa-system.git

cd enterprise-qa-system

# 安装Python依赖

pip install -r requirements.txt

最小可运行的服务骨架如下：加载分词器与 GPT-2 中等模型，构造带背景信息的提示词，暴露 /query 接口接收 JSON 并返回生成结果。
[PRE1]python

from flask import Flask, request, jsonify

import torch

# 初始化Flask应用

app=Flask(__name__)

# 加载模型和分词器（确保在服务器环境中可以加载模型）

from transformers import AutoTokenizer, AutoModelForCausalLM

model_name="gpt2-medium"

tokenizer=AutoTokenizer.from_pretrained(model_name)

model=AutoModelForCausalLM.from_pretrained(model_name)

device="cuda" if torch.cuda.is_available() else "cpu"

model.to(device)

# 定义生成函数

def generate_answer(context, question):

    prompt=f"企业问答系统: \n\n背景信息: {context}\n\n问题: {question}\n\n回答: "

    inputs=tokenizer(prompt, return_tensors="pt").to(device)

    with torch.no_grad():

        outputs=model.generate(inputs["input_ids"], max_length=150)

    return tokenizer.decode(outputs[0], skip_special_tokens=True)

# 定义API端点：用于接收查询并返回回答

@app.route('/query', methods=['POST'])

def query():

    data=request.json

    context=data.get("context")

    question=data.get("question")

    if not context or not question:

        return jsonify({"error": "context and question are required"}), 400

    answer=generate_answer(context, question)

    return jsonify({"answer": answer})

# 运行Flask应用

if __name__=="__main__":

    app.run(host="0.0.0.0", port=5000)

为了保证环境一致，使用 Docker 打包。镜像基于 Python 3.8，复制代码、安装依赖、预下载模型并以 app.py 启动。
[PRE2]dockerfile

# 使用Python基础镜像

FROM python:3.8

# 设置工作目录

WORKDIR /app

# 复制项目文件

COPY . .

# 安装项目依赖

RUN pip install -r requirements.txt

# 下载模型

RUN python -c "from transformers import AutoTokenizer, AutoModelForCausalLM; \
AutoTokenizer.from_pretrained('gpt2-medium'); \
AutoModelForCausalLM.from_pretrained('gpt2-medium')"

# 启动Flask应用

CMD ["python", "app.py"]

构建镜像命令：
[PRE3]bash

# 构建镜像

docker build -t enterprise-qa-system .

对外暴露统一域名时用 Nginx 做反向代理，把 80 端口转发到本机 5000。
[PRE4]nginx

server {

    listen 80;

    server_name qa.yourcompany.com;

    location / {

        proxy_pass http://127.0.0.1:5000;

        proxy_set_header Host $host;

        proxy_set_header X-Real-IP $remote_addr;

        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_set_header X-Forwarded-Proto $scheme;

    }

}

上线后可用简单的 POST 请求做冒烟验证。
[PRE5]python

import requests

# 测试请求

url = "http://qa.yourcompany.com/query"

data = {

    "context": "公司规定所有员工每年可以享受带薪年假。",

    "question": "公司的带薪年假政策是什么？"

}

response = requests.post(url, json=data)

print("API返回结果:", response.json())

预期返回示例：
[PRE6]text

>> API返回结果： {'answer': '公司的带薪年假政策是每年员工享有10天的带薪年假。'}

项目整体目录示意：
[PRE7]text

# 项目目录结构

# enterprise_qa_system/

配置集中管理模型名、设备与采样参数，便于后续调整。
[PRE8]python

# 配置文件, 定义模型参数和其他配置

MODEL_NAME = "gpt2-medium"

DEVICE = "cuda" if torch.cuda.is_available() else "cpu"

GENERATION_CONFIG = {

    "max_length": 150,

    "temperature": 0.6,

    "top_k": 40,

    "top_p": 0.85,

    "num_return_sequences": 1

}

生成模块抽离后使用统一配置调用模型：
[PRE9]python

import torch

from transformers import AutoTokenizer, AutoModelForCausalLM

from config import MODEL_NAME, DEVICE, GENERATION_CONFIG

# 加载生成模型和分词器

tokenizer = AutoTokenizer.from_pretrained(MODEL_NAME)

model = AutoModelForCausalLM.from_pretrained(MODEL_NAME).to(DEVICE)

def generate_answer(context, question):

    """

    生成回答函数, 接收上下文和问题, 返回生成的回答

    """

    prompt = f"企业问答系统: \n\n背景信息: \n{context}\n\n问题: {question}\n\n回答: "

    inputs = tokenizer(prompt, return_tensors="pt").to(DEVICE)

    with torch.no_grad():

        outputs = model.generate(

            inputs["input_ids"],

            max_length=GENERATION_CONFIG["max_length"],

            temperature=GENERATION_CONFIG["temperature"],

            top_k=GENERATION_CONFIG["top_k"],

            top_p=GENERATION_CONFIG["top_p"],

            num_return_sequences=GENERATION_CONFIG["num_return_sequences"],

            do_sample=True

        )

    return tokenizer.decode(outputs[0], skip_special_tokens=True)

检索层采用 FAISS 构建向量索引，支持快速相似匹配。
[PRE10]python

import faiss

import numpy as np

# 构建和管理向量数据库

class VectorDatabase:

    def __init__(self, dim=768, nlist=100):

        """

        初始化向量数据库，设置索引结构

        """

        self.index = faiss.IndexFlatL2(dim)

        self.index_ivf = faiss.IndexIVFFlat(self.index, dim, nlist)

        self.index_ivf.train(np.random.rand(10000, dim).astype("float32")) # 模拟训练

        self.index_ivf.add(np.random.rand(1000, dim).astype("float32")) # 添加数据

    def search(self, query_vector, k=3):

        """

        检索最相似的向量

        """

        distances, indices = self.index_ivf.search(query_vector, k)

        return distances, indices

预处理工具把原始文本转为模型可用的嵌入表示。
[PRE11]python

from transformers import AutoTokenizer

import numpy as np

import torch

from config import MODEL_NAME, DEVICE

tokenizer = AutoTokenizer.from_pretrained(MODEL_NAME)

def preprocess_data(texts):

    """

    对文本数据进行预处理，并生成嵌入向量

    """

    embeddings = []

    for text in texts:

        inputs = tokenizer(text, return_tensors="pt", truncation=True).to(DEVICE)

        embedding = inputs["input_ids"].cpu().detach().numpy()

        embeddings.append(embedding)

    return np.array(embeddings)

最终集成服务在接收查询时先检索相关向量，再调用生成模型产出答案。
[PRE12]python

from flask import Flask, request, jsonify

import torch

from model import generate_answer

from database import VectorDatabase

from utils import preprocess_data

app = Flask(__name__)

# 初始化向量数据库

vector_db = VectorDatabase()

@app.route('/query', methods=['POST'])

def query():

    """

    API端点，接收查询请求并返回回答

    """

    data = request.json

    context = data.get("context")

    question = data.get("question")

    if not context or not question:

        return jsonify({"error": "context and question are required"}), 400

    # 检索最相似的向量

    embeddings = preprocess_data([context])

    distances, indices = vector_db.search(embeddings, k=3)

    print("检索结果:", distances, indices)

    # 调用生成模型

    answer = generate_answer(context, question)

    return jsonify({"answer": answer})

if __name__ == "__main__":

    app.run(host="0.0.0.0", port=5000)

核心依赖清单：
[PRE13]text

torch

transformers

faiss-cpu

flask

numpy

自动化测试脚本可批量校验不同场景的关键字命中情况。
[PRE14]python

# test_app.py

import requests

import json

# 定义测试数据

test_data = [

    {

        "context": "公司政策规定，所有员工每年可以享受10天的带薪年假。",

        "question": "公司的带薪年假政策是什么？",

        "expected_keywords": ["带薪年假", "10天"]

    },

    {

        "context": "员工在公司入职满一年后可以获得额外的年终奖金。",

        "question": "公司对年终奖金是如何规定的的？",

        "expected_keywords": ["年终奖金", "满一年"]

    },

    {

        "context": "公司支持员工每周三在家办公。",

        "question": "公司是否允许远程工作？",

        "expected_keywords": ["在家办公", "远程工作"]

    },

    {