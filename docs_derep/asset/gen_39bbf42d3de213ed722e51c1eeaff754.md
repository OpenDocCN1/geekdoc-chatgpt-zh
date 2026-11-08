# 长文本与连环问答：通过参数精细化提升生成稳定性

当处理长文档生成或多轮对话时，控制生成过程的参数对结果一致性至关重要。下面给出一个可灵活调参的生成函数示例，便于在不同场景下调节多样性、长度和重复度。

def detailed_generate(prompt, max_length=100, temperature=0.5,

                    top_p=0.9, repetition_penalty=1.2):

    """

    高级生成函数，适合长文档生成任务

    参数：

    -prompt: 文本提示

    -max_length: 最大生成长度

    -temperature: 控制生成的多样性

    -top_p: 控制生成的概率阈值

    -repetition_penalty: 重复惩罚项，避免生成内容过于重复

    """

    input_ids=tokenizer.encode(prompt, return_tensors="pt").to(device)

    output=model.generate(

        input_ids,

        max_length=max_length,

        temperature=temperature,

        top_p=top_p,

        repetition_penalty=repetition_penalty,

        do_sample=True

    )

    answer=tokenizer.decode(output[0], skip_special_tokens=True)

    return answer

# 进一步测试优化后的生成

long_answer=detailed_generate(prompt, max_length=100)

print("\n高级生成的回答：")

print(long_answer)

[PRE0]

>> 生成模型加载完成： gpt2

>> 模型已加载至： cuda

>>

>> 生成的回答：

>> 糖尿病是一种慢性疾病，需要长期控制血糖。患者可以选择低糖、低脂肪的饮食，定期监控血糖水平，以保持健康。

>>

>> 优化后的回答：

[PRE1]python

# 导入必要的库

from transformers import AutoTokenizer, AutoModelForCausalLM, pipeline

import faiss

import numpy as np

import pickle

import torch

# Step 1: 加载向量数据库和生成模型

# 加载之前保存的FAISS索引文件

index=faiss.read_index("faiss_index_file.index")

print("成功加载FAISS索引")

# 加载嵌入数据和文本数据，以便在查询时返回原始内容

with open("text_embeddings.pkl", "rb") as f:

    embeddings=pickle.load(f)

# 假设文本数据在预处理阶段已经保存过，这里重新加载用于生成回答

data_file="preprocessed_data_with_embeddings.csv"

import pandas as pd

df=pd.read_csv(data_file)

# 加载生成模型

model_name="gpt2"

tokenizer=AutoTokenizer.from_pretrained(model_name)

model=AutoModelForCausalLM.from_pretrained(model_name)

device="cuda" if torch.cuda.is_available() else "cpu"

model.to(device)

print("生成模型加载完成：", model_name)

# Step 2: 定义检索与生成函数

# 1. 检索模块：查询最相似的上下文

# 2. 生成模块：基于上下文生成自然语言回答

def retrieve_context(query_text, top_k=1):

    """

    基于输入查询从向量数据库中检索最相似的上下文

    """

    # 将查询转换为嵌入向量

    query_embedding=tokenizer.encode(query_text,

                                     return_tensors="pt").to(device)

    query_embedding=model(query_embedding).last_hidden_state.mean(1) \

        .cpu().detach().numpy()  # 获取查询嵌入

    # 执行检索

    distances, indices=index.search(query_embedding, top_k)

    # 获取最相似的上下文文本

    similar_contexts=df.iloc[indices[0]]['clean_text'].tolist()

    return similar_contexts

def generate_answer(query, context, max_length=50):

    """

    使用生成模型根据上下文和查询生成回答

    """

    # 构造生成模型的提示

    prompt=f"{context} 问题：{query} 回答："

    # 使用生成模型生成答案

    input_ids=tokenizer.encode(prompt, return_tensors="pt").to(device)

    output=model.generate(input_ids, max_length=max_length,

                          temperature=0.7, top_k=50)

    answer=tokenizer.decode(output[0], skip_special_tokens=True)

    return answer

# Step 3: 问答系统流程：集成检索与生成

def qa_system(query_text):

    """

    完整问答系统流程：从检索模块到生成模块

    """

    # 1. 检索相关上下文

    context = retrieve_context(query_text)

    # 2. 生成回答

    answer = generate_answer(query_text, context)

    return answer

[PRE2]python

"""

print(f"\n用户查询: {query_text}")

# 1. 使用检索模块获取最相关的上下文

context=retrieve_context(query_text, top_k=1)[0]

print(f"检索到的相关上下文: {context}")

# 2. 将查询与上下文输入至生成模块生成回答

answer=generate_answer(query_text, context)

print(f"生成的回答: {answer}")

return answer

# Step 4: 测试完整问答系统

sample_query="糖尿病患者如何管理饮食?"

qa_system(sample_query)

[PRE3]

>> 用户查询: 糖尿病患者如何管理饮食?

>>

>> 检索到的相关上下文: 糖尿病是一种慢性疾病, 需要长期控制血糖。

>>

>> 生成的回答: 糖尿病是一种慢性疾病, 需要长期控制血糖。糖尿病患者应选择低糖饮食, 减少摄入含糖食品, 定期监测血糖并与医生合作制定健康饮食计划。

[PRE4]python

# 导入必要的库

from transformers import AutoTokenizer, AutoModelForCausalLM, pipeline

import faiss

import numpy as np

import pickle

import torch

import pandas as pd

# 加载之前的模型和索引

index=faiss.read_index("faiss_index_file.index")

print("FAISS索引加载成功")

data_file="preprocessed_data_with_embeddings.csv"

df=pd.read_csv(data_file)

model_name="gpt2"

tokenizer=AutoTokenizer.from_pretrained(model_name)

model=AutoModelForCausalLM.from_pretrained(model_name)

device="cuda" if torch.cuda.is_available() else "cpu"

model.to(device)

print("生成模型加载成功：", model_name)

# Step 1: 测试生成的参数设置

# 定义一个更为灵活的生成函数，允许动态调整生成参数

def generate_optimized_answer(prompt, \

    max_length=60, temperature=0.6, top_p=0.9, repetition_penalty=1.1):

    """

    使用生成模型生成优化后的回答

    -top_p: 用于核采样，控制生成的概率阈值

    -repetition_penalty: 避免内容重复

    """

    input_ids=tokenizer.encode(prompt, return_tensors="pt").to(device)

    output=model.generate(

        input_ids,

        max_length=max_length,

        temperature=temperature,

        top_p=top_p,

        repetition_penalty=repetition_penalty,

        do_sample=True

    )

    answer=tokenizer.decode(output[0], skip_special_tokens=True)

    return answer

# Step 2: 优化检索模块

# 调整检索函数，增强查询与上下文的语义匹配

def retrieve_optimized_context(query_text, top_k=2):

    """

    通过FAISS索引进行优化检索，返回多个相似的上下文，并选择最相关的上下文

    """

    query_embedding=tokenizer.encode(query_text,

                                     return_tensors="pt").to(device)

    query_embedding=model(query_embedding).last_hidden_state \

        .mean(1).cpu().detach().numpy()

    distances, indices=index.search(query_embedding, top_k)

    # 如果检索出多个上下文，筛选出与查询最相关的上下文

    similar_contexts=df.iloc[indices[0]]['clean_text'].tolist()

    # 将多个上下文组合成一个字符串，便于生成模块处理

    combined_context=" ".join(similar_contexts)

    return combined_context

# Step 3: 集成优化的问答系统

def qa_optimized_system(query_text):

    """

    优化的问答系统：结合多重检索与参数调优，提升回答的准确性

    """

    print(f"\n用户查询: {query_text}")

    # 1. 使用优化检索模块获取相关上下文