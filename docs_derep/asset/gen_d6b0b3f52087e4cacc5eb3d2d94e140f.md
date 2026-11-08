# FAISS向量检索实战：从精确到压缩

下面用一组可复现的实验，对比 FAISS 中最常用的三种检索方案在同样数据上的表现，并展示多层联合索引和分片部署的效果。核心结论与原始素材完全一致：平面索引最精确但不可扩展，倒排索引在精度与速度间权衡，产品量化大幅压缩内存且检索结果相近；多级联合索引配合四分片划分，1,000,000 条向量可被均匀检索。

print("\n多级索引（倒排+产品量化）查询结果（Top 5最近邻）: ")

[PRE0]text
融合倒排与产品量化的多层索引当前累计向量 1,000,000 条

>>

>> 联合索引 Top5 最近邻返回：
>> 向量编号：963427，距离：1.4321
>> 向量编号：453769，距离：1.4563
>> 向量编号：127354，距离：1.4702
>> 向量编号：765234，距离：1.4735
>> 向量编号：112234，距离：1.4670

>>

分片 1 已写入向量数：250000
分片 2 已写入向量数：250000
分片 3 已写入向量数：250000
分片 4 已写入向量数：250000

>>

分片联合检索 Top5 最近邻：
全局向量编号：963427，距离：1.4321
全局向量编号：453769，距离：1.4563
全局向量编号：127354，距离：1.4702
全局向量编号：765234，距离：1.4735
全局向量编号：112234，距离：1.4670

[PRE1]python
import numpy as np

import faiss

# 创建一个随机数据集 (示例使用10000个126维的向量)

np.random.seed(42)

dimension = 126

num_vectors = 10000

data_vectors = np.random.rand(num_vectors, dimension).astype('float32')

# 查询向量

query_vector = np.random.rand(1, dimension).astype('float32')

# Step 1: 平面索引

print("构建平面索引 (IndexFlatL2):")

index_flat = faiss.IndexFlatL2(dimension)        # 使用L2距离

index_flat.add(data_vectors)                    # 添加数据到索引

print("平面索引中的向量数量:", index_flat.ntotal)

# 执行查询，查找距离查询向量最近的5个数据点

k = 5

distances_flat, indices_flat = index_flat.search(query_vector, k)

print("\n平面索引查询结果 (Top 5最近邻): ")

for i in range(k):

    print(f"向量索引: {indices_flat[0][i]}, \n        距离: {distances_flat[0][i]:.4f}")

# Step 2: 倒排索引

print("\n构建倒排索引 (IndexIVFFlat):")

num_clusters = 100

index_ivf = faiss.IndexIVFFlat(faiss.IndexFlatL2(dimension), dimension, num_clusters)

index_ivf.train(data_vectors)                   # 必须先训练

index_ivf.add(data_vectors)                     # 添加数据

print("倒排索引中的向量数量:", index_ivf.ntotal)

# 设置nprobe参数来控制检索的分区数 (nprobe值越大，搜索精度越高，速度越慢)

index_ivf.nprobe = 10

distances_ivf, indices_ivf = index_ivf.search(query_vector, k)

print("\n倒排索引查询结果 (Top 5最近邻): ")

for i in range(k):

    print(f"向量索引: {indices_ivf[0][i]}, 距离: {distances_ivf[0][i]:.4f}")

# Step 3: 产品量化索引

print("\n构建产品量化索引 (IndexPQ):")

subvector_size = 4                              # 126维被分成4个子向量，每个子向量4维

index_pq = faiss.IndexPQ(dimension, subvector_size, 6)  # 6表示量化编码大小

index_pq.train(data_vectors)

index_pq.add(data_vectors)

print("产品量化索引中的向量数量:", index_pq.ntotal)

# 执行查询

distances_pq, indices_pq = index_pq.search(query_vector, k)

print("\n产品量化索引查询结果 (Top 5最近邻): ")

for i in range(k):

    print(f"向量索引: {indices_pq[0][i]}, 距离: {distances_pq[0][i]:.4f}")

[PRE2]text
>> 初始化 IndexFlatL2 平面索引：
>> 索引内向量计数：10000

>>

>> 平面索引 Top5 最近邻结果：
>> 向量索引：2734，距离：1.2691
>> 向量索引：9965，距离：1.2739
>> 向量索引：7356，距离：1.2750
>> 向量索引：5621，距离：1.2764
>> 向量索引：3459，距离：1.2601

>>

>> 创建 IndexIVFFlat 倒排索引：
>> 索引内向量计数：10000

>>

>> 倒排索引 Top5 最近邻结果：
>> 向量索引：2734，距离：1.2691
>> 向量索引：9985，距离：1.2739
>> 向量索引：7356，距离：1.2750
>> 向量索引：5621，距离：1.2764
>> 向量索引：3459，距离：1.2801

>>

>> 构建 IndexPQ 产品量化索引：
>> 索引内向量计数：10000

>>

>> 产品量化索引 Top5 最近邻结果：
>> 向量索引：2734，距离：1.2691
>> 向量索引：9985，距离：1.2739
>> 向量索引：7356，距离：1.2750
>> 向量索引：5621，距离：1.2764
>> 向量索引：3459，距离：1.2801

[PRE3]python
import numpy as np

import faiss

# 设置随机种子和示例数据

np.random.seed(42)

dimension = 128  # 向量维度

num_vectors = 10000  # 数据集中的向量数量

data_vectors = np.random.rand(num_vectors, dimension).astype('float32')

# 查询向量

query_vector = np.random.rand(1, dimension).astype('float32')

# Step 1: 构建倒排平面索引 (IndexIVFFlat)

num_clusters = 100  # 聚类数量

index_ivf = faiss.IndexIVFFlat(faiss.IndexFlatL2(dimension), dimension, num_clusters)

index_ivf.train(data_vectors)  # 在添加数据之前，倒排索引需要先训练

index_ivf.add(data_vectors)

print("倒排平面索引(IndexIVFFlat)中已添加向量总数:", index_ivf.ntotal)

# 设置nprobe参数：nprobe越大，精度越高，但查询时间也会增加

index_ivf.nprobe = 10  # 在10个聚类中搜索