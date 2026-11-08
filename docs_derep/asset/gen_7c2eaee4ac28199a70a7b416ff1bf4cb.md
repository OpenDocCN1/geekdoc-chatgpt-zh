# 医疗文献检索提速指南：从向量到精准召回

想把海量医学论文瞬间变成可用的知识库，关键在于三件事：把文本变成高质量向量、用 FAISS 做快速近邻搜索、再用二次重排把相关度拉满。下面的流程用 DistilBERT 生成嵌入，先建平面索引验证，再升级到 IVF 和 IVFPQ 控制精度与速度，最后用余弦相似度做重排，并支持动态调节 nprobe 和 top_k。实测检索 `diabetes treatment advancements`、`cancer treatment` 与 `cancer immunotherapy` 都能快速返回标题、摘要和距离。

        results.append(result)

    return results

# 测试查询，确保优化后的索引结构能够快速匹配

query="diabetes treatment advancements"

results=search_optimized_medical_documents(query)

print("\n优化后的查询结果: ")

for i, res in enumerate(results, 1):

    print(f"\nResult {i}")

    print("ID:", res["id"])

    print("Title:", res["title"])

    print("Abstract:", res["abstract"])

    print("Distance:", res["distance"])

[PRE0]python

from sklearn.metrics.pairwise import cosine_similarity

def rerank_results(query_embedding, initial_results):

    """

    对初步检索结果进行重排序,提高检索精确度

    """

    reranked_results=[]

    for result in initial_results:

        # 提取每个初步检索结果的嵌入向量

        doc_embedding=np.array(result["embedding"]).reshape(1, -1)

        # 计算余弦相似度

        similarity_score=cosine_similarity(query_embedding, doc_embedding)[0][0]

        result["similarity_score"]=similarity_score

        reranked_results.append(result)

    # 根据相似度分数对结果排序

    reranked_results.sort(key=lambda x: x["similarity_score"], reverse=True)

    return reranked_results

# 查询并重排序

query_embedding=generate_embedding("diabetes treatment advancements").reshape(1, -1)

initial_results=search_optimized_medical_documents("diabetes treatment advancements")

final_results=rerank_results(query_embedding, initial_results)

print("\n重排序后的查询结果: ")

for i, res in enumerate(final_results, 1):

    print(f"\nResult {i}")

    print("ID:", res["id"])

    print("Title:", res["title"])

    print("Abstract:", res["abstract"])

    print("Similarity Score:", res["similarity_score"])

[PRE1]python

def dynamic_search_medical_documents(query, top_k=5, nprobe=10):

    """

    动态参数查询函数，支持动态调节 nprobe 和 top_k 参数

    """

    index_ivfpq.nprobe=nprobe

    query_embedding=generate_embedding(query).reshape(1, -1).astype('float32')

    distances, indices=index_ivfpq.search(query_embedding, top_k)

    results=[]

    for dist, idx in zip(distances[0], indices[0]):

        result={

            "id": cleaned_df.iloc[idx]["id"],

            "title": cleaned_df.iloc[idx]["title"],

            "abstract": cleaned_df.iloc[idx]["abstract"],

            "distance": dist

        }

        results.append(result)

    return results

# 测试动态查询

print("动态查询结果：")

dynamic_results=dynamic_search_medical_documents("cancer immunotherapy", top_k=5, nprobe=15)

for i, res in enumerate(dynamic_results, 1):

    print(f"\nResult {i}")

    print("ID:", res["id"])

    print("Title:", res["title"])

    print("Abstract:", res["abstract"])

    print("Distance:", res["distance"])

[PRE2]python

import faiss

import numpy as np

import pandas as pd

# 假设 cleaned_df 包含之前生成的嵌入向量

# cleaned_df 是一个DataFrame，列名包含 'id'、'title'、'abstract' 和 'embedding'

# 读取或加载清洗好的数据集

# cleaned_df=pd.read_csv('processed_medical_data.csv')  # 示例加载命令

[PRE3]python

# 定义嵌入维度

embedding_dim=cleaned_df['embedding'].iloc[0].shape[0]

# 创建FAISS索引

index=faiss.IndexFlatL2(embedding_dim)  # 使用L2距离的平面索引

# 将嵌入向量添加到索引中

embeddings=np.vstack(cleaned_df['embedding'].values).astype('float32')

index.add(embeddings)

print("FAISS索引创建完成。索引中的向量数量:", index.ntotal)

[PRE4]python

# 创建倒排索引结构（IVF），将数据分为 100 个聚类

nlist=100  # 聚类数量

quantizer=faiss.IndexFlatL2(embedding_dim)  # 量化器

index_ivf=faiss.IndexIVFFlat(quantizer, embedding_dim, nlist, faiss.METRIC_L2)

# 训练索引

index_ivf.train(embeddings)

index_ivf.add(embeddings)

print("倒排索引结构创建完成，向量数量:", index_ivf.ntotal)

[PRE5]python

from transformers import AutoTokenizer, AutoModel

# 加载同样的嵌入模型

tokenizer=AutoTokenizer.from_pretrained("distilbert-base-uncased")

model=AutoModel.from_pretrained("distilbert-base-uncased")

def generate_embedding(text):

    """生成查询文本的嵌入向量"""

    inputs=tokenizer(text, return_tensors="pt", truncation=True, max_length=512)

    outputs=model(**inputs)

    embedding=outputs.last_hidden_state[:, 0, :].detach().numpy()

    return embedding.flatten()

def search_medical_documents(query, top_k=5):

    """

    使用FAISS进行相似度查询，返回最匹配的top_k个文档

    """

    # 生成查询的嵌入向量

    query_embedding=generate_embedding(query).reshape(1, -1).astype('float32')

    # 在倒排索引中检索

    index_ivf.nprobe=10  # 设置nprobe值以控制检索效率

    distances, indices=index_ivf.search(query_embedding, top_k)

    results=[]

    for dist, idx in zip(distances[0], indices[0]):

        result={

            "id": cleaned_df.iloc[idx]["id"],

            "title": cleaned_df.iloc[idx]["title"],

            "abstract": cleaned_df.iloc[idx]["abstract"],

            "distance": dist

        }

        results.append(result)

    return results

[PRE6]python

query="cancer treatment"

results=search_medical_documents(query, top_k=5)

print("\n查询结果：")

for i, res in enumerate(results, 1):

    print(f"\nResult {i}")

    print("ID:", res["id"])

    print("Title:", res["title"])

    print("Abstract:", res["abstract"])

    print("Distance:", res["distance"])

[PRE7]

>> FAISS索引创建完成。索引中的向量数量：10000

>> 倒排索引结构创建完成，向量数量：10000

>>

>> 查询结果:

>>

>> Result 1

>> ID: 342

>> Title: Advances in Cancer Treatment

>> Abstract: Recent studies have shown progress in various cancer therapies...

>> Distance: 0.246

>>

>> Result 2

>> ID: 765

>> Title: Oncology and Chemotherapy Innovations

>> Abstract: Exploring new methodologies for cancer treatment through chemotherapy...

>> Distance: 0.289

>> ...

[PRE8]python

# 使用倒排产品量化 (IndexIVFPQ) 索引

nlist=100	# 聚类数量，与10.2.1节保持一致

m=8	# 产品量化的维度

n_bits=8	# 每个子向量的比特数

这套方案先用向量索引保证召回速度，再用余弦重排保证排序质量，最后通过可调 nprobe/top_k 平衡效率与精度，适合做医学问答、文献追踪等高要求检索场景。