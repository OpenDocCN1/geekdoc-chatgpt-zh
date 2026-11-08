# 向量相似检索实战：从暴力比对到FAISS加速

想快速找到和给定文本最接近的那几条内容，核心思路就是把文本转成向量后做邻近搜索。本文用三个递进的例子演示思路：先用最小的3维玩具数据对比欧氏距离和余弦相似度，再实现一次线性的暴力扫描，最后用FAISS把检索搬到万级规模。

## 1. 最小样例先把度量讲明白

先造5条三维嵌入，给每条配上 text_id，方便观察。随后封装两个度量：欧氏距离越近代表越像，余弦相似度越高代表方向越一致。检索函数统一入口，metric 切换欧氏或余弦，top_k 控制返回数量。查询点设为 [0.45, 0.25, 0.15]，分别跑两套度量并在3D里把数据点和查询点画出来，直观感受空间位置。

[PRE0]python

import numpy as np

from sklearn.metrics.pairwise import cosine_similarity, euclidean_distances

import pandas as pd

# Step 1: 构造示例数据集

# 假设有5个文本样本，每个文本经过嵌入生成三维向量

# 在实际应用中，向量可以是更高维度，此处简化为三维向量以便于理解

data_vectors=np.array([[0.5, 0.2, 0.1],

                       [0.4, 0.3, 0.9],

                       [0.6, 0.1, 0.7],

                       [0.9, 0.6, 0.2],

                       [0.2, 0.5, 0.3] ])

# 创建一个数据框存储向量及其对应的文本ID

df=pd.DataFrame(data_vectors, columns=["dim1", "dim2", "dim3"])

df['text_id']=["text1", "text2", "text3", "text4", "text5"]

print("数据集向量表示: \n", df)

# Step 2: 定义相似性度量函数

# Euclidean Distance计算函数

def euclidean_similarity(query_vector, data_vectors):

    """

    计算查询向量与数据集中向量的欧氏距离

    """

    return euclidean_distances([query_vector], data_vectors)[0]

# Cosine Similarity计算函数

def cosine_similarity_score(query_vector, data_vectors):

    """

    计算查询向量与数据集中向量的余弦相似度

    """

    return cosine_similarity([query_vector], data_vectors)[0]

# Step 3: 构建基础检索函数

def search_by_similarity(query_vector, data_vectors, metric='euclidean', top_k=3):

    """

    根据相似性度量方法对查询向量进行检索

    -metric: 'euclidean'或'cosine', 选择相似性度量方法

    -top_k: 返回前x个相似数据点

    """

    if metric=='euclidean':

        distances=euclidean_similarity(query_vector, data_vectors)

        # 欧氏距离越小越相似，升序排序

        sorted_indices=np.argsort(distances)[:top_k]

    elif metric=='cosine':

        scores=cosine_similarity_score(query_vector, data_vectors)

        # 余弦相似度越大越相似，降序排序

        sorted_indices=np.argsort(-scores)[:top_k]

    else:

        raise ValueError("不支持的度量方法。请选择 'euclidean' 或 'cosine'")

    # 返回最相似的top_k个向量及其对应的相似度/距离值

    results=[]

    for idx in sorted_indices:

        if metric=='euclidean':

            results.append((df.iloc[idx]['text_id'], distances[idx]))

        else:

            results.append((df.iloc[idx]['text_id'], scores[idx]))

    return results

# Step 4: 进行示例检索

# 设置查询向量，用于计算与数据集中向量的相似性

query_vector=np.array([0.45, 0.25, 0.15])

# 欧氏距离检索

print("\n使用欧氏距离检索结果：")

euclidean_results=search_by_similarity(query_vector,

                                       data_vectors, metric='euclidean')

for text_id, distance in euclidean_results:

    print(f"文本ID: {text_id}, 距离: {distance:.4f}")

# 余弦相似度检索

print("\n使用余弦相似度检索结果：")

cosine_results=search_by_similarity(query_vector, data_vectors, metric='cosine')

for text_id, score in cosine_results:

    print(f"文本ID: {text_id}, 相似度: {score:.4f}")

# Step 5: 将查询结果可视化

# 使用Matplotlib展示向量的分布和查询点的位置

import matplotlib.pyplot as plt

from mpl_toolkits.mplot3d import Axes3D

fig=plt.figure(figsize=(10, 6))

ax=fig.add_subplot(111, projection='3d')

# 绘制数据集中的样本向量

ax.scatter(data_vectors[:, 0], data_vectors[:, 1], data_vectors[:, 2],

           color='blue', label='Data Vectors')

# 绘制查询向量

ax.scatter(query_vector[0], query_vector[1], query_vector[2], color='red',

           s=100, label='Query Vector')

# 标注文本ID

for i, text_id in enumerate(df['text_id']):

    ax.text(data_vectors[i, 0], data_vectors[i, 1], data_vectors[i, 2], text_id)

# 设置图形属性

ax.set_xlabel('Dimension 1')

ax.set_ylabel('Dimension 2')

ax.set_zlabel('Dimension 3')

plt.title("向量空间中的数据点和查询向量")

plt.legend()

plt.show()

[PRE1]

>> 数据集向量表示:

>>   dim1  dim2  dim3 text_id

>> 0  0.5   0.2   0.1  text1

>> 1  0.4   0.3   0.9  text2

>> 2  0.6   0.1   0.7  text3

>> 3  0.9   0.6   0.2  text4

>> 4  0.2   0.5   0.3  text5

>> 

>> 使用欧氏距离检索结果:

>> 文本ID: text1, 距离: 0.0707

>> 文本ID: text5, 距离: 0.4000

>> 文本ID: text3, 距离: 0.5916

>> 

>> 使用余弦相似度检索结果:

>> 文本ID: text1, 相似度: 0.9953

>> 文本ID: text5, 相似度: 0.9535

>> 文本ID: text3, 相似度: 0.6716

从输出看，text1 在两种度量下都最接近，欧氏距离仅0.0707，余弦高达0.9953；不同之处在于距离对绝对位置敏感，余弦更关注方向。

## 2. 线性扫描的通用写法

去掉 DataFrame 映射，直接对索引做排序，metric 只接受 euclidean 或 cosine。先算全部距离或相似度，再按升序/降序取 top_k，这就是最直观的暴力检索，适合验证逻辑。

[PRE2]python

import numpy as np

from sklearn.metrics.pairwise import euclidean_distances, cosine_similarity

# 示例数据

data_vectors=np.array([[0.1, 0.2, 0.3],

                       [0.4, 0.5, 0.6],

                       [0.7, 0.6, 0.9],

                       [0.2, 0.3, 0.4],

                       [0.9, 0.6, 0.1]])

# 查询向量

query_vector=np.array([0.15, 0.25, 0.35])

# 线性搜索函数

def linear_search(query_vector, data_vectors, metric='euclidean', top_k=3):

    if metric=='euclidean':

        distances=euclidean_distances([query_vector], data_vectors)[0]

        sorted_indices=np.argsort(distances)[:top_k]

        results=[(idx, distances[idx]) for idx in sorted_indices]

    elif metric=='cosine':

        scores=cosine_similarity([query_vector], data_vectors)[0]

        sorted_indices=np.argsort(-scores)[:top_k]

        results=[(idx, scores[idx]) for idx in sorted_indices]

    else:

        raise ValueError("不支持的度量方法。选择 'euclidean' 或 'cosine'")

    return results

# 运行线性搜索

print("线性搜索-欧氏距离结果：")

euclidean_results=linear_search(query_vector, data_vectors, metric='euclidean')

for idx, distance in euclidean_results:

    print(f"向量索引: {idx}, 距离: {distance:.4f}")

print("\n线性搜索-余弦相似度结果：")

cosine_results=linear_search(query_vector, data_vectors, metric='cosine')

for idx, score in cosine_results:

    print(f"向量索引: {idx}, 相似度: {score:.4f}")

## 3. 上量后用FAISS近似加速

当向量数到上万、维度上百时，线性扫描成本太高。FAISS 的 IndexIVFFlat 先聚类再分区，训练后再把向量全量写入索引，查询时只探查 nprobe 个子列表，在精度和速度间做权衡。下面是 1万条 126维向量的标准流程。

[PRE3]python

import faiss

# 构造示例数据

data_vectors=np.random.rand(10000, 126).astype('float32')  # 10000个126维向量

# 查询向量

query_vector=np.random.rand(1, 126).astype('float32')

# 创建FAISS索引（倒排平面索引）并训练

dimension=126

index=faiss.IndexIVFFlat(faiss.IndexFlatL2(dimension), dimension, nlist=100)

index.train(data_vectors)

index.add(data_vectors)

print(f"FAISS索引已创建并添加向量，总数: {index.ntotal}")

# 搜索Top-K结果

k=5

index.nprobe=10  # 增加nprobe值以提高查准率

distances, indices=index.search(query_vector, k)

print("FAISS近似最近邻检索结果: ")

for i in range(k):

    print(f"向量索引: {indices[0][i]}, 距离: {distances[0][i]:.4f}")

三步走下来，从度量选择到暴力验证再到工业级加速的完整路径就清晰了：小数据先用欧氏/余弦对比，大数据交给 FAISS 用 IVF 平衡召回与耗时。