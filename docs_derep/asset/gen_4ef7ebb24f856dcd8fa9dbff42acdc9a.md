# 文本标注+向量检索：从规则打标到语义找相似的完整手感

处理一批中文技术文档时，常见的工作流是先做规则化的分类与标签，再补充一些可量化的元信息，最后把文本变成向量放到检索库里做相似查询。下面用两个紧凑的实操片段把思路摊开。

第一段先做规则标注。借助正则判断关键词，给每条文本挂上大类 category 和多标签 labels，随后用一个小函数统计词数并检查是否出现“代码”二字，形成结构化的元数据。

if re.search(r'机器学习|分类|回归', text, re.IGNORECASE):

        labels.append('机器学习')

    return ', '.join(labels)

# Step 3: 应用标注和分类

df['category']=df['text'].apply(categorize_text)

df['labels']=df['text'].apply(label_text)

# 打印标注和分类后的数据

print("\n标注和分类后的数据: \n", df[['document_id', 'text', 'category', 'labels']])

# Step 4: 进一步的结构化操作

def generate_metadata(text):

    """

    基于文本内容生成元数据

    """

    word_count=len(text.split())

    contains_code='代码' in text

    return {'word_count': word_count, 'contains_code':contains_code}

# 应用元数据生成

df['metadata']=df['text'].apply(generate_metadata)

# 打印添加了元数据的信息

print("\n添加元数据后的数据: \n", df[['document_id', 'text', 'category', 'labels', 'metadata']])

[PRE0]

>> 原始数据:

>>     document_id                                      text

>> 0                1  Python教程：如何安装和使用Python环境

>> 1                2  深度学习介绍：使用TensorFlow进行图像分类

>> 2                3  数据分析入门：用Pandas和NumPy进行数据清理

>> 3                4  自然语言处理技术：介绍NLTK和spaCy的用法

>> 4                5  机器学习概述：分类与回归的应用

>>

>> 标注和分类后的数据:

>>     document_id                                      text             category      labels

>> 0                1  Python教程：如何安装和使用Python环境  数据分析与编程        数据分析

>> 1                2  深度学习介绍：使用TensorFlow进行图像分类          深度学习         深度学习

>> 2                3  数据分析入门：用Pandas和NumPy进行数据清理  数据分析与编程        数据分析

>> 3                4  自然语言处理技术：介绍NLTK和spaCy的用法      自然语言处理    自然语言处理

>> 4                5  机器学习概述：分类与回归的应用              机器学习         机器学习

>>

>> 添加元数据后的数据:

>>     document_id                                      text             category      labels          metadata

>> 0                1  Python教程：如何安装和使用Python环境  数据分析与编程        数据分析  {'word_count': 6, 'contains_code': False}

>> 1                2  深度学习介绍：使用TensorFlow进行图像分类          深度学习         深度学习  {'word_count': 6, 'contains_code': False}

>> 2                3  数据分析入门：用Pandas和NumPy进行数据清理  数据分析与编程        数据分析  {'word_count': 6, 'contains_code': False}

>> 3                4  自然语言处理技术：介绍NLTK和spaCy的用法      自然语言处理    自然语言处理  {'word_count': 6, 'contains_code': False}

>> 4                5  机器学习概述：分类与回归的应用              机器学习         机器学习  {'word_count': 5, 'contains_code': False}

第二段进入语义检索。先用 SentenceTransformer 把短文本转成向量，再把向量丢进 FAISS 的 L2 平面索引，查询时直接拿自然语言问题去匹配最接近的原文，最后把索引落盘验证加载一致性。

[PRE1]python

# 导入所需的库

import numpy as np

import pandas as pd

from sentence_transformers import SentenceTransformer

import faiss

[PRE2]python

# Step 1: 加载数据集

data={

    "document_id": [1, 2, 3, 4, 5],

    "text": [

        "Python编程的入门指南。",

        "深度学习中的图像分类应用。",

        "数据分析工具的使用方法。",

        "自然语言处理的基础技术。",

        "机器学习的模型评估方法。"

    ]

}

df=pd.DataFrame(data)

print("原始数据:\n", df)

# Step 2: 嵌入生成

# 使用 SentenceTransformer 加载嵌入生成模型

model=SentenceTransformer('paraphrase-MiniLM-L6-v2')

# 生成文本的嵌入向量

df['embedding']=df['text'].apply(lambda x: model.encode(x))

print("\n生成的嵌入向量:")

for idx, embedding in enumerate(df['embedding']):

    print(f"Document ID {df['document_id'][idx]} Embedding:\n{embedding[:5]}...")

# Step 3: 将嵌入向量存储到向量数据库

# 提取所有嵌入并转换为FAISS可处理的矩阵

embeddings_matrix=np.vstack(df['embedding'].values).astype('float32')

# 初始化FAISS索引 (L2距离)

dimension=embeddings_matrix.shape[1]

index=faiss.IndexFlatL2(dimension)                # 使用L2距离构建平面索引

index.add(embeddings_matrix)                       # 将所有嵌入添加到索引中

# 查看索引中的向量数量

print("\n向量数据库中已存储的嵌入数量:", index.ntotal)

# Step 4: 检索测试

# 定义查询文本

query_text="如何开始学习Python编程?"

query_embedding=model.encode(query_text).astype('float32').reshape(1, -1)

# 检索最相似的3个文档

k=3

distances, indices=index.search(query_embedding, k)

print("\n查询文本:", query_text)

print("\n最相似的文档ID及距离:")

for i in range(k):

    doc_id=df['document_id'][indices[0][i]]

    distance=distances[0][i]

    print(f"文档ID {doc_id}-距离: {distance}-内容: {df['text'][indices[0][i]]}")

[PRE3]python

# Step 5: 将向量索引保存到文件, 便于后续使用

faiss.write_index(index, "vector_index.faiss")

print("\n向量索引已保存为 'vector_index.faiss'")

# Step 6: 加载已保存的索引 (验证保存功能)

loaded_index=faiss.read_index("vector_index.faiss")

# 再次查询已保存的索引, 验证一致性

distances, indices=loaded_index.search(query_embedding, k)

print("\n使用加载的索引进行查询, 验证一致性: ")

for i in range(k):

    doc_id=df['document_id'][indices[0][i]]

    distance=distances[0][i]

    print(f"文档ID {doc_id}-距离: {distance}-内容: {df['text'][indices[0][i]]}")

[PRE4]

>> 原始数据:

>> document_id text

>> 0 1 Python编程的入门指南。

>> 1 2 深度学习中的图像分类应用。

>> 2 3 数据分析工具的使用方法。

>> 3 4 自然语言处理的基础技术。

>> 4 5 机器学习的模型评估方法。

>>

>> 生成的嵌入向量:

>> Document ID 1 Embedding:

>> [ 0.00036186 -0.045214 -0.0123619 0.0187014 0.0202273 ]...

>> Document ID 2 Embedding:

>> [ 0.00051948 -0.0364521 -0.00784334 0.0148378 0.0194768 ]...

>> Document ID 3 Embedding:

>> [ 0.00043216 -0.0421793 -0.085871 0.0162324 0.0213721 ]...

>> Document ID 4 Embedding:

>> [ 0.00040698 -0.0397918 -0.00979435 0.0150638 0.0207591 ]...

>> Document ID 5 Embedding:

>> [ 0.00045122 -0.0425637 -0.089758 0.0167155 0.0218445 ]...

>>

>> 向量数据库中已存储的嵌入数量: 5

>>

>> 查询文本: 如何开始学习Python编程?

>>

>> 最相似的文档ID及距离:

>> 文档ID 1-距离: 0.2501-内容: Python编程的入门指南。

>> 文档ID 2-距离: 0.3647-内容: 深度学习中的图像分类应用。

>> 文档ID 3-距离: 0.4929-内容: 数据分析工具的使用方法。

>>

>> 向量索引已保存为 'vector_index.faiss'

>>

>> 使用加载的索引进行查询, 验证一致性:

>> 文档ID 1-距离: 0.2501-内容: Python编程的入门指南。

>> 文档ID 2-距离: 0.3647-内容: 深度学习中的图像分类应用。

>> 文档ID 3-距离: 0.4929-内容: 数据分析工具的使用方法。