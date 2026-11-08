# 让大模型回答更靠谱的检索增强套路

想让生成结果不跑偏，最直接的方法就是先检索再生成。先把用户问题转成向量，再用余弦相似度从知识库里筛出最相关的片段，把这些片段拼成上下文喂给生成模型，最后得到的回答既有依据又更贴题。下面是一个可直接跑通的极简实现，思路清晰、闭环完整。

query_embedding=generate_embeddings([query])[0]

# Step 4: 计算相似度得分并进行排序

similarity_scores=cosine_similarity([query_embedding], retrieved_embeddings)[0]

top_n=2 # 选择两个最相关的检索结果

top_indices=np.argsort(similarity_scores)[-top_n:][::-1]

# Step 5: 组合最相关的文本片段作为生成模型的输入上下文

contextual_text=" ".join([retrieved_texts[i] for i in top_indices])

print("生成模型上下文:", contextual_text)

# Step 6: 使用生成模型创建回答

generator=pipeline("text-generation", model="gpt-3.5-turbo")

result=generator(f"问题: {query} 上下文信息: {contextual_text} 请详细回答。", max_length=150, num_return_sequences=1)

print("\n生成的回答:", result[0]["generated_text"])

[PRE0]
>> 生成模型上下文：大语言模型的发展在生成任务中非常关键。语义相似度有助于提高检索与生成的相关性。

>> 生成的回答：问题：如何使用语义相似度来提升生成内容的准确性？上下文信息：大语言模型的发展在生成任务中非常关键。语义相似度有助于提高检索与生成的相关性。请详细回答。

>> 语义相似度计算帮助生成模型选择最相关的内容，从而提高生成结果的准确性。通过向量化表示和嵌入技术，系统可以在文本之间找到潜在的语义联系，在复杂问题下结合语义信息生成连贯的回答。这种方法在检索和生成系统中能够有效地提升内容相关性与生成的精确度。

整体流程就是先向量化、再匹配、后拼接、最后生成。向量化让文本具备可计算的语义表征，余弦相似度负责找出最贴近问题的资料，拼接成的上下文相当于给模型一个可靠的短时记忆，生成环节就能在限定范围内产出更准更连贯的答案。

[PRE1]python
# 导入所需的库

from transformers import AutoTokenizer, AutoModel, pipeline

import torch

import numpy as np

from sklearn.metrics.pairwise import cosine_similarity

# 设置设备

device="cuda" if torch.cuda.is_available() else "cpu"

# Step 1: 嵌入生成函数

def generate_embeddings(texts, model_name="bert-base-uncased"):

    """

    生成文本嵌入向量

    texts: 文本列表

    model_name: 使用的模型名称

    """

    tokenizer=AutoTokenizer.from_pretrained(model_name)

    model=AutoModel.from_pretrained(model_name).to(device)

    embeddings=[]
[PRE2]python
# 导入所需的库

from transformers import AutoTokenizer, AutoModel, pipeline

import torch

import numpy as np

from sklearn.metrics.pairwise import cosine_similarity

# 设置设备

device = "cuda" if torch.cuda.is_available() else "cpu"

# Step 1: 定义嵌入生成函数

def generate_embeddings(texts, model_name="bert-base-uncased"):

    """

    生成文本的嵌入向量

    texts: 文本列表

    model_name: 使用的模型名称

    """

    tokenizer = AutoTokenizer.from_pretrained(model_name)

    model = AutoModel.from_pretrained(model_name).to(device)

    embeddings = []

    for text in texts:

        inputs = tokenizer(text, return_tensors="pt",

                           truncation=True, padding=True).to(device)

        with torch.no_grad():

            outputs = model(**inputs)

            embedding = outputs.last_hidden_state.mean(dim=1).cpu().numpy()

            embeddings.append(embedding)

    return np.vstack(embeddings)

# Step 2: 准备检索到的示例文本数据

retrieved_texts = [

    "大模型的发展涉及多层神经网络的深度学习。",

    "生成式 AI 的核心是理解语言的复杂性和多样性。",

    "嵌入技术让模型能够从数据中提取有意义的特征。",

    "人工智能系统通过数据驱动生成有意义的内容。"

]

# 生成嵌入

retrieved_embeddings = generate_embeddings(retrieved_texts)

# Step 3: 使用生成模型创建上下文回答

# 定义生成模型

generator = pipeline("text-generation", model="gpt-3.5-turbo")

# 输入问题

query = "请解释大模型如何在内容生成中理解上下文。"

在这个版本里，嵌入函数先对每条文本做截断和填充，再取最后一层隐藏状态的均值作为表示。知识库文本提前向量化，问题进来后立刻得到它的向量，就能和库里所有向量做相似度比较。

[PRE3]python
query_embedding = generate_embeddings([query])[0]

# Step 4: 计算相似度并选择最相关的检索结果

similarity_scores = cosine_similarity([query_embedding], retrieved_embeddings)[0]

top_n = 2  # 选择两个最相关的检索结果

top_indices = np.argsort(similarity_scores)[-top_n:][::-1]

# 组合最相关的文本片段作为生成模型的输入

contextual_text = " ".join([retrieved_texts[i] for i in top_indices])

print("生成模型上下文:", contextual_text)

# 使用生成模型生成回答

result = generator(f"问题: {query} 上下文信息: \n{contextual_text} 请提供详细回答。", max_length=150,

                   num_return_sequences=1)

print("\n生成的回答:", result[0]["generated_text"])

相似度排序后取前N个片段拼接成上下文，再把问题和上下文一起作为提示输入给生成器，模型在有参考的情况下输出细节更充分的答案。把检索和生成串起来，就是现在非常主流的RAG思路，落地简单且效果立竿见影。