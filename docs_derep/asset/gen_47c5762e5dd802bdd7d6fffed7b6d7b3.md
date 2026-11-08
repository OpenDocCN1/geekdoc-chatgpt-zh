[PRE0]python

import pandas as pd

from transformers import BertTokenizer, BertModel

import torch

# 加载预训练BERT模型及对应的分词器
model_name="bert-base-uncased"
tokenizer=BertTokenizer.from_pretrained(model_name)
model=BertModel.from_pretrained(model_name)

# 示例法条列表，实际项目中可从DataFrame读取
law_articles=[
    "The right to free speech is guaranteed by the First Amendment.",
    "No person shall be deprived of life, liberty, \n    or property, without due process of law.",
    "The Constitution provides for the separation \n    of powers among the legislative, executive, and judicial branches."
]

# 将法条文本映射为向量表示
def vectorize_law_articles(articles):
    vectors=[]
    for article in articles:
        # 对当前法条进行分词编码
        inputs=tokenizer(article, return_tensors='pt',\n            padding=True, truncation=True, max_length=512)
        with torch.no_grad():
            # 关闭梯度，在无训练模式下读取隐藏层输出
            outputs=model(**inputs)
            # 用[CLS]位置的向量作为整条法条的语义表征
            cls_vector=outputs.last_hidden_state[0][0].numpy()
            vectors.append(cls_vector)
    return vectors

# 调用函数生成向量
law_vectors=vectorize_law_articles(law_articles)

# 把向量装入DataFrame以便后续分析
law_vectors_df=pd.DataFrame(law_vectors)
law_vectors_df.columns=[f"dim_{i}" for i in range (law_vectors_df.shape[1])]
law_vectors_df["law_article"]=law_articles

# 打印DataFrame预览
print(law_vectors_df)

[PRE1]
>>    dim_0    dim_1    dim_2  ...    dim_n              law_article
>> 0  0.123456  0.654321  0.789012  ...  0.234567  The right to free speech is guaranteed by the First Amendment.
>> 1  0.987654  0.543210  0.210987  ...  0.876543  No person shall be deprived of life, liberty, or property, without due process of law.
>> 2  0.543210  0.321098  0.654321  ...  0.098765  The Constitution provides for the separation of powers among the legislative, executive, and judicial branches.

[PRE2]bash
pip install faiss-cpu

[PRE3]bash
pip install faiss-gpu

[PRE4]python
import numpy as np

# 以下为已生成的法条向量示例
law_vectors=np.array([
    # dim_0, dim_1, dim_2, ..., dim_n
    [0.123456, 0.654321, 0.789012],
    [0.987654, 0.543210, 0.210987],
    [0.543210, 0.321098, 0.654321]
])

[PRE5]python
import faiss  # 导入FAISS库用于相似度检索

# 步骤一：构建索引
def build_faiss_index(vectors):
    """
    为向量创建FAISS可检索索引
    :param vectors: 输入的NumPy向量矩阵
    :return: 可用的FAISS索引实例
    """
    # 新建基于L2欧氏距离的扁平索引
    dimension=vectors.shape[1]
    index=faiss.IndexFlatL2(dimension)  # 采用L2距离度量
    index.add(vectors)                  # 把法条向量写入索引
    return index

# 步骤二：实例化索引
faiss_index=build_faiss_index(law_vectors)

# 步骤三：封装查询逻辑
def query_faiss_index(index, query_vector, k=2):
    """
    在已构建的FAISS索引中执行近邻检索
    :param index: FAISS索引对象
    :param query_vector: 待查询向量
    :param k: 需要返回的最近邻个数
    :return: 距离与对应索引
    """
    distances, indices=index.search(query_vector, k)  # 执行近邻搜索
    return distances, indices

# 步骤四：执行一次查询
# 这里用示例向量代替真实的法条编码结果
query_vector=np.array([[0.1, 0.6, 0.7]], dtype=np.float32)  # 示例查询向量
distances, indices=query_faiss_index(faiss_index, query_vector)

# 步骤五：输出查询结果
print("查询向量：", query_vector)
print("最近邻索引：", indices)
print("最近邻距离：", distances)

# 步骤六：映射回原文法条
for idx in indices[0]:
    if idx < len(law_articles):
        print("匹配的法律条款：", law_articles[idx])

[PRE6]text
>> 查询向量： [[0.1 0.6 0.7]]
>> 最近邻索引： [[0 1]]
>> 最近邻距离： [[0.123456 0.654321]]
>> 匹配的法律条款： The right to free speech is guaranteed by the First Amendment.
>> 匹配的法律条款： No person shall be deprived of life, liberty, or property, without due process of law.

[PRE7]python
import numpy as np
import faiss

# 法条向量在此处已准备好
law_vectors=np.array([
    [0.123456, 0.654321, 0.789012],
    [0.987654, 0.543210, 0.210987],
    [0.543210, 0.321098, 0.654321]
]).astype(np.float32)

law_articles=[
    "第一条：言论自由受到宪法保护。",
    "第二条：无论何人，不得在未经过法定程序的情况下剥夺其生命、自由或财产。",
    "第三条：所有人都有权平等享有法律的保护。"
]

# 创建FAISS索引
def build_faiss_index(vectors):
    index=faiss.IndexFlatL2(vectors.shape[1])
    index.add(vectors)
    return index

faiss_index=build_faiss_index(law_vectors)

# 查询优化函数
def optimize_query(query, index, k=2):
    """
    统一查询前处理与检索流程
    :param query: 用户输入的自然语言查询
    :param index: FAISS索引对象
    :param k: 返回结果数
    :return: 过滤排序后的结果
    """
    # 查询预处理：分词与向量化
    query_vector=preprocess_query(query)
    # 执行相似度检索
    distances, indices=index.search(query_vector, k)
    # 结果筛选和排序
    return filter_and_sort_results(indices, distances)

def preprocess_query(query):
    """
    对用户查询做预处理得到向量
    :param query: 原始用户输入
    :return: 可用于检索的向量
    """
    # 可在此加入分词、去停用词等步骤
    # 当前以示例向量占位
    return np.array([[0.1, 0.6, 0.7]], dtype=np.float32)

def filter_and_sort_results(indices, distances):
    """