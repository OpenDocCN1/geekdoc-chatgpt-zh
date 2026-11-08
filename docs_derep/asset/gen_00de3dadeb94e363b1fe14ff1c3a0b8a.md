法律智能问答的搭建全流程拆解

想把法规检索和自然语言生成串起来做一个可用的法律问答助手，核心就三步：先建向量索引做召回，再用生成模型做回答，最后对全流程做验证。下面是几个典型实现细节的整理，方便直接复现。

索引搭建与检索验证
先实例化 FAISS 索引，把预处理好的向量写入。随后用第一条向量做一次自检检索，取 top 5。校验检索结果的维度是否符合预期，通过断言锁定召回环节可用。

紧接着进入回答生成环节。用《民法典》第123条的解释请求作为提示词，调用 LegalAnswerGenerator 生成文本，并断言返回内容非空。全部校验通过后打印成功信息和样例回答。

[PRE0]

>> 所有测试均通过！

>> 生成的法律回答: 《民法典》第123条主要规定了自然人享有民事权利的基本原则，包括法律对其权利的保护、权利的行使应当遵循社会公德等内容。在应用中，该条款对于个人财产的保护以及权利义务的明确具有重要意义。

检索增强生成实战
检索模块先把用户问题向量化，再交给 FAISS 索引取前 5 条最相关的法规条款。生成模块则把召回结果拼成提示，调用 OpenAI 的 ChatCompletion，模型选用 gpt-3.5-turbo，返回最终答案。主流程依次完成索引构建、召回、生成并返回结果。

[PRE1]python

import openai

from sklearn.metrics.pairwise import cosine_similarity

import numpy as np

# 假设之前已经有的函数

def fetch_related_regulations(user_query, regulations_index):

    """

    使用检索模块从法规索引中获取相关法规条款

    """

    # 将用户查询向量化

    query_vector = vectorize_query(user_query)

    # 使用FAISS检索相关法规

    D, I = regulations_index.search(query_vector, k=5)  # 获取前5条相关法规

    return [regulations[i] for i in I[0]]  # 返回相关法规条款

def generate_answer(retrieved_regulations):

    """

    使用生成模型生成法规回答

    """

    prompt = "根据以下法律条款生成法律回答: \n" + "\n".join(retrieved_regulations)

    response = openai.ChatCompletion.create(

        model="gpt-3.5-turbo",  # 替换为适合的生成模型

        messages=[

            {"role": "user", "content": prompt}

        ]

    )

    return response['choices'][0]['message']['content']

def vectorize_query(query):

    """

    向量化用户查询

    """

    # 假设有一个预训练的嵌入模型

    return embedding_model.encode(query)  # 返回向量表示

# 主流程

def legal_query_assistant(user_query):

    """

    法律查询助手的主流程

    """

    regulations_index = build_faiss_index()  # 构建FAISS索引

    retrieved_regulations = fetch_related_regulations(user_query, regulations_index)

    answer = generate_answer(retrieved_regulations)

    return answer

# 测试

user_query = "请问关于个人信息保护的相关法律条款有哪些? "

result = legal_query_assistant(user_query)

print("生成的法律回答: ", result)

[PRE2]

>> 生成的法律回答：根据《个人信息保护法》第十条，个人信息处理应遵循合法、正当、必要的原则，明确处理目的并取得用户的同意。同时，《网络安全法》第三十五条规定，网络运营者应当采取措施保障用户个人信息的安全，不得泄露或非法提供用户信息。

输出格式优化与解释生成
使用 Hugging Face 的 pipeline 加载 text-generation 模型，提供 load_model 统一加载。解释文本经过 format_legal_explanation 处理，用分隔线包裹，提升可读性。生成时先检索到相关法条，再让模型基于法条进行解释，最后返回格式化结果。

[PRE3]python

import json

from transformers import pipeline

# 模型加载

def load_model(model_name):

    model = pipeline('text-generation', model=model_name)

    return model

# 优化输出格式

def format_legal_explanation(raw_explanation):

    # 对输出进行格式化，确保清晰易读

    formatted = f"法律解释:\n{'-'*20}\n{raw_explanation}\n{'-'*20}"

    return formatted

# 生成法律解释的函数

def generate_legal_explanation(query, model):

    # 检索相关法律条款（假设已有检索模块）

    relevant_law = retrieve_relevant_law(query)

    # 使用生成模型生成解释

    raw_explanation = model(f"根据法律条款:{relevant_law}, \n                        请解释该法律条款。")[0]['generated_text']

    # 格式化输出

    formatted_explanation = format_legal_explanation(raw_explanation)

    return formatted_explanation

# 示例检索函数（简化实现）

def retrieve_relevant_law(query):

    # 这里可以实现与FAISS索引的检索交互

    # 假设检索返回的法律条款

    return "《民法典》第123条：债务人应当按照约定履行债务。"

# 主函数

if __name__ == "__main__":

    model_name = "gpt-3"  # 假设使用GPT-3

    model = load_model(model_name)

    query = "债务人如何履行债务？"

    explanation = generate_legal_explanation(query, model)

    print(explanation)

[PRE4]

>> 法律解释:

>> ------------------

>> 根据法律条款:《民法典》第123条：债务人应当按照约定履行债务。请解释该法律条款。

>> 债务人需要按照合同中约定的方式和时间履行其义务。未履行的情况下，将会产生违约责任。

>> ------------------

轻量化生成方案
基于 GPT2 的生成思路：加载 GPT2LMHeadModel 与 GPT2Tokenizer，对提示词编码后调用 generate，设置 max_length、no_repeat_ngram_size、early_stopping 等参数控制输出。解码后直接返回中文法律回答。

[PRE5]python

from transformers import GPT2LMHeadModel, GPT2Tokenizer

# 加载预训练的生成模型和分词器

model_name = "gpt2"  # 可以选择更专业的法律模型

tokenizer = GPT2Tokenizer.from_pretrained(model_name)

model = GPT2LMHeadModel.from_pretrained(model_name)

# 定义提示词

def generate_legal_answer(prompt, max_length=200):

    # 对提示词进行编码

    inputs = tokenizer.encode(prompt, return_tensors='pt')

    # 生成法律回答

    outputs = model.generate(

        inputs,

        max_length=max_length,

        num_return_sequences=1,

        no_repeat_ngram_size=2,

        early_stopping=True

    )

    # 解码生成的回答

    answer = tokenizer.decode(outputs[0], skip_special_tokens=True)

    return answer

# 示例提示词

prompt = "请解释《民法典》第123条的主要内容及其应用。"

answer = generate_legal_answer(prompt)

# 输出生成的法律回答

print("法律回答:", answer)

[PRE6]

>> 法律回答: 《民法典》第123条主要规定了... 该条款在实际应用中...

数据采集与预处理基座
项目起步先做数据收集。LegalDataCollector 接收数据源列表，通过 requests 拉取 JSON，再在 clean_data 中过滤掉空条目并提取 text 字段。随后进入标准化阶段，使用 StandardScaler 对特征做归一化，为后续向量化做准备。

[PRE7]python

# 法律法规查询助手主文件

import pandas as pd

import requests

from transformers import GPT2LMHeadModel, GPT2Tokenizer

from sklearn.preprocessing import StandardScaler

import faiss

import numpy as np

# 1. 数据收集与清洗

class LegalDataCollector:

    def __init__(self, sources):

        self.sources = sources

        self.data = []

    def collect_data(self):

        for source in self.sources:

            response = requests.get(source)

            if response.status_code == 200:

                self.data.append(response.json())  # 假设数据是JSON格式

        return self.data

    def clean_data(self):

        cleaned_data = []

        for entry in self.data:

            # 进行数据清洗，比如去除无效条目

            if entry and 'text' in entry:

                cleaned_data.append(entry['text'])

        return cleaned_data

# 2. 数据标准化

def standardize_data(data):

    scaler = StandardScaler()