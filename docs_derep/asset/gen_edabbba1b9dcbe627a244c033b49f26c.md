# MarianMT 英中翻译：从批量文档到划词即时

想快速把英文文档转成中文，同时又希望在网页上能划词即时查看？下面这套方案用 Helsinki-NLP/opus-mt-en-zh 这个 MarianMT 模型完成了两件事：本地脚本实现段落级批量翻译，并通过 FastAPI 暴露单句与整篇异步翻译接口，前端用鼠标选词就能拿到结果。整个流程覆盖文档解析、模型加载、逐段翻译、结果落盘，以及后端服务与前端交互，运行日志也清晰可查。

以下保留原始实现细节，便于直接复用和对比。

    print(f"文档包含段落数: {len(paragraphs)}")

    print(f"文档总词数: {word_count}")

    return paragraphs

# 加载翻译模型

def load_translation_model():

    """

    加载 MarianMT 模型和分词器

    返回:

        translator:翻译模型管道

    """

    model_name="Helsinki-NLP/opus-mt-en-zh" # 英文到中文翻译模型

    tokenizer=MarianTokenizer.from_pretrained(model_name)

    model=MarianMTModel.from_pretrained(model_name)

    translator=pipeline("translation", model=model, tokenizer=tokenizer)

    print("翻译模型加载成功")

    return translator

# 文档翻译

def translate_paragraphs(paragraphs, translator):

    """

    对段落进行翻译

    参数:

        paragraphs:段落列表

        translator:翻译模型管道

    返回:

        translations:翻译结果列表

    """

    translations=[]

[PRE0]python

for i, para in enumerate(paragraphs):

    result=translator(para, max_length=512)

    translations.append(result[0]['translation_text'])

    print(f"段落 {i+1} 翻译完成")

return translations

# 保存翻译结果

def save_translations(output_path, translations):

    """

    保存翻译结果到文件

    参数:

        output_path:结果保存路径

        translations:翻译结果列表

    """

    with open(output_path,'w', encoding='utf-8') as file:

        file.write('\n\n'.join(translations))

    print(f"翻译结果已保存至: {output_path}")

# 主流程

if __name__ == "__main__":

    # 输入文件路径

    input_file="sample_document.txt"

    output_file="sample_document_translated.txt"

    try:

        # 文档解析

        paragraphs=analyze_document(input_file)

        # 加载翻译模型

        translator=load_translation_model()

        # 翻译文档段落

        translations=translate_paragraphs(paragraphs, translator)

        # 保存翻译结果

        save_translations(output_file, translations)

        # 输出部分翻译结果

        print("\n示例翻译结果:")

        for i, translated in enumerate(translations[:3]):

            print(f"段落 {i+1} 翻译: {translated}")

    except Exception as e:

        print(f"发生错误: {str(e)}")

[PRE1]

文档包含段落数: 5

文档总词数: 380

翻译模型加载成功

段落 1 翻译完成

段落 2 翻译完成

段落 3 翻译完成

段落 4 翻译完成

段落 5 翻译完成

翻译结果已保存至: sample_document_translated.txt

示例翻译结果:

段落 1 翻译: 人工智能的快速发展正在改变各个行业的运作方式。

段落 2 翻译: 自然语言处理技术在跨语言通信中发挥了至关重要的作用。

段落 3 翻译: 大型语言模型通过大量数据训练,在翻译任务中表现出色。

[PRE2]python

# 后端实现:翻译服务

from fastapi import FastAPI, Request

from transformers import MarianMTModel, MarianTokenizer

# 初始化 FastAPI 应用

app=FastAPI()

# 加载翻译模型

model_name="Helsinki-NLP/opus-mt-en-zh"

tokenizer=MarianTokenizer.from_pretrained(model_name)

model=MarianMTModel.from_pretrained(model_name)

@app.post("/translate/")

async def translate_text(request: Request):

    """

    翻译 API,用于接收前端传递的文本并返回翻译结果

    """

    data=await request.json()

    text_to_translate=data.get("text", "")

    if not text_to_translate:

        return {"error": "No text provided"}

    # 翻译文本

    translated=model.generate(** tokenizer(text_to_translate, return_tensors="pt", padding=True))

    translation=tokenizer.batch_decode(translated, skip_special_tokens=True)[0]

    return {"translation": translation}

[PRE3]html

<!DOCTYPE html>

<html lang="en">

<head>

    <meta charset="UTF-8">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>划词翻译</title>

    <style>

        body {

            font-family: Arial, sans-serif;

            margin: 20px;

        }

        #output {

            margin-top: 20px;

            padding: 10px;

            border: 1px solid #ccc;

            background-color: #f9f9f9;

        }

    </style>

</head>

<body>

    <h1>划词翻译工具</h1>

    <p>选择下面的文本,自动获取翻译结果。</p>

    <p id="text">

        Artificial intelligence is transforming industries by automating processes and enabling data-driven insights.

    </p>

    <div id="output">翻译结果将显示在这里</div>

    <script>

        async function getTranslation(selectedText) {

            const response=await fetch("http://127.0.0.1:8000/translate/", {

                method: "POST",

                headers: {

                    "Content-Type": "application/json"

                },

                body: JSON.stringify({ text: selectedText })

            });

            const data=await response.json();

            document.getElementById("output").innerText=data.translation || "翻译失败";

        }

        document.getElementById("text").addEventListener("mouseup", () => {

            const selection=window.getSelection().toString();

            if (selection) {

                getTranslation(selection);

            }

        });

    </script>

</body>

</html>

[PRE4]

翻译结果将显示在这里

翻译完成:人工智能正在通过自动化流程和数据驱动洞察变革行业。

[PRE5]python

# 导入必要库

from fastapi import FastAPI, Request, BackgroundTasks

from transformers import MarianMTModel, MarianTokenizer

import os

import re

# 初始化 FastAPI 应用

app = FastAPI()

# 加载翻译模型

model_name = "Helsinki-NLP/opus-mt-en-zh"

tokenizer = MarianTokenizer.from_pretrained(model_name)

model = MarianMTModel.from_pretrained(model_name)

@app.post("/translate/")

async def translate_text(request: Request):

    """

    翻译 API,接收前端文本并返回翻译结果

    """

    data = await request.json()

    text_to_translate = data.get("text", "")

    if not text_to_translate:

        return {"error": "No text provided"}

    # 翻译文本

    translated = model.generate(**tokenizer(text_to_translate,

                                            return_tensors="pt", padding=True))

[PRE6]python

translation=tokenizer.batch_decode(translated,

                                 skip_special_tokens=True)[0]

return {"translation": translation}

@app.post("/bulk_translate/")

async def bulk_translate(file_path: str, background_tasks: BackgroundTasks):

    """

    批量翻译API,接收文档路径并异步翻译整篇文档

    """

    if not os.path.exists(file_path):

        return {"error": "File not found"}

    def process_file():

        with open(file_path, 'r', encoding='utf-8') as file:

            content=file.read()

        # 提取段落

        paragraphs=[

    para.strip() for para in content.split('\n') if para.strip()]

        translations=[]

        for para in paragraphs:

            translated=model.generate(

                    ** tokenizer(para, return_tensors="pt", padding=True))

            translation =tokenizer.batch_decode(

                    translated, skip_special_tokens=True)[0]

            translations.append(translation)

        # 保存翻译结果

        output_path=file_path.replace('.txt', '_translated.txt')

        with open(output_path, 'w', encoding='utf-8') as file:

            file.write('\n\n'.join(translations))

    background_tasks.add_task(process_file)

    return {"status": "Translation in progress"}

# 启动主函数

if __name__ == "__main__":

    import uvicorn

    uvicorn.run(app, host="127.0.0.1", port=8000)

[PRE7]json

{

    "status": "Translation in progress"
}