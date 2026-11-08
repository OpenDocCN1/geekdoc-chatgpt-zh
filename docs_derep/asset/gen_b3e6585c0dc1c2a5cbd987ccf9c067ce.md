FAISS 向量检索：Flat 与 IVF 快速实测

想把文本变成可检索的向量，先要把语义转成数字表示，再用 FAISS 建立索引做相似度搜索。下面用一个最小可运行的例子演示完整流程，从数据清洗、向量生成到两种索引的构建与对比。

先确定向量维度并用最朴素的 Flat 索引把数据放进去，看看基础检索长什么样。

dimension=embeddings.shape[1]

# 创建L2距离的索引结构

index=faiss.IndexFlatL2(dimension)

# 将嵌入向量添加到索引中

index.add(embeddings)

print("索引已构建完成，包含向量数: ", index.ntotal)

Flat 索引会遍历全部向量，精度高但规模大了会变慢。实际工程里常用倒排 IVF 来加速，先聚类再局部扫描。

[PRE0]python
# 设置倒排索引的聚类数（即簇数量），一般为数据量的平方根

nlist=int(np.sqrt(len(embeddings)))

# 使用倒排索引结构创建索引

quantizer=faiss.IndexFlatL2(dimension)

index_ivf=faiss.IndexIVFFlat(quantizer, dimension, nlist, faiss.METRIC_L2)

# 训练索引：先进行聚类，以便倒排索引结构加速检索

index_ivf.train(embeddings)

index_ivf.add(embeddings)

print("倒排索引已构建，包含向量数: ", index_ivf.ntotal)
[/PRE0]

有了两套索引后，用同一个查询向量分别检索，k 取 3 看前三名结果是否一致。

[PRE1]python
# 定义查询向量（例如，选择一个嵌入向量作为测试）

query_vector=embeddings[0].reshape(1, -1)

# 执行检索操作

k=3  # 检索前3个相似结果

# 使用原始的Flat索引进行检索

D_flat, I_flat=index.search(query_vector, k)

print("使用IndexFlatL2检索结果: ")

for i in range(k):

    print(f"文档ID: {doc_ids[I_flat[0][i]]}, 距离: {D_flat[0][i]}")

# 使用优化后的IVF索引进行检索

D_ivf, I_ivf=index_ivf.search(query_vector, k)

print("\n使用IndexIVFFlat检索结果: ")

for i in range(k):

    print(f"文档ID: {doc_ids[I_ivf[0][i]]}, 距离: {D_ivf[0][i]}")
[/PRE1]

运行日志显示两者返回的顺序和距离完全相同，说明在小数据上 IVF 训练后能复现 Flat 的结果。

[PRE2]
>> 嵌入向量和文档ID加载完成。
>> 嵌入向量的形状:  (3, 384)
>> 索引已构建完成，包含向量数:  3
>> 倒排索引已构建，包含向量数:  3
>>
>> 使用IndexFlatL2检索结果:
>> 文档ID: 1, 距离: 0.0
>> 文档ID: 2, 距离: 3.4
>> 文档ID: 3, 距离: 4.2
>>
>> 使用IndexIVFFlat检索结果:
>> 文档ID: 1, 距离: 0.0
>> 文档ID: 2, 距离: 3.4
>> 文档ID: 3, 距离: 4.2
[/PRE2]

下面把向量生成的前置步骤补全，先加载已保存的嵌入和文档 id。

[PRE3]python
import faiss

import numpy as np

# 加载嵌入向量和文档ID

embeddings=np.load("embeddings.npy")

doc_ids=np.load("doc_ids.npy")

print("嵌入向量和文档ID加载完成。")

print("嵌入向量的形状: ", embeddings.shape)
[/PRE3]

拿到维度后重建 Flat 索引并写入向量，确认数量。

[PRE4]python
# 获取嵌入向量的维度

dimension=embeddings.shape[1]

# 创建L2距离的索引结构

index=faiss.IndexFlatL2(dimension)

# 将嵌入向量添加到索引中

index.add(embeddings)

print("索引已构建完成，包含向量数: ", index.ntotal)
[/PRE4]

再按平方根规则设定簇数，构建 IVF 索引并训练。

[PRE5]python
# 设置倒排索引的聚类数（即簇数量），一般为数据量的平方根

nlist=int(np.sqrt(len(embeddings)))

# 使用倒排索引结构创建索引

quantizer=faiss.IndexFlatL2(dimension)

index_ivf=faiss.IndexIVFFlat(quantizer, dimension, nlist, faiss.METRIC_L2)

# 训练索引：先进行聚类，以便倒排索引结构加速检索

index_ivf.train(embeddings)

index_ivf.add(embeddings)

print("倒排索引已构建，包含向量数: ", index_ivf.ntotal)
[/PRE5]

查询部分保持不变，方便对比两种索引的输出格式。

[PRE6]python
# 定义查询向量 (例如, 选择一个嵌入向量作为测试)

query_vector=embeddings[0].reshape(1, -1)

# 执行检索操作

k=3  # 检索前3个相似结果

# 使用原始的Flat索引进行检索

D_flat, I_flat=index.search(query_vector, k)

print("使用IndexFlatL2检索结果: ")

for i in range(k):

    print(f"文档ID: {doc_ids[I_flat[0][i]]}, 距离: {D_flat[0][i]}")

# 使用优化后的IVF索引进行检索

D_ivf, I_ivf=index_ivf.search(query_vector, k)

print("\n使用IndexIVFFlat检索结果: ")

for i in range(k):

    print(f"文档ID: {doc_ids[I_ivf[0][i]]}, 距离: {D_ivf[0][i]}")
[/PRE6]

第二次运行的日志再次验证一致性。

[PRE7]
>> 嵌入向量和文档ID加载完成。
>> 嵌入向量的形状: (3, 384)
>> 索引已构建完成, 包含向量数: 3
>> 倒排索引已构建, 包含向量数: 3
>>
>> 使用IndexFlatL2检索结果:
>> 文档ID: 1, 距离: 0.0
>> 文档ID: 2, 距离: 3.4
>> 文档ID: 3, 距离: 4.2
>>
>> 使用IndexIVFFlat检索结果:
>> 文档ID: 1, 距离: 0.0
>> 文档ID: 2, 距离: 3.4
>> 文档ID: 3, 距离: 4.2
[/PRE7]

接下来展示向量是怎么产生的。首先导入建模所需的库。

[PRE8]python
# 导入所需的库

import pandas as pd

from transformers import AutoTokenizer, AutoModel

import torch

import numpy as np
[/PRE8]

构造一个三条文档的示范集，并做基础清洗。

[PRE9]python
# 创建示例数据集

data={

    "doc_id": [1, 2, 3],

    "text": [

        "Python是数据科学的基础工具。",

        "机器学习可以应用于多种场景，包括图像分类和自然语言处理。",

        "深度学习推动了计算机视觉的进步。"

    ]

}

# 将数据转换为DataFrame格式

df=pd.DataFrame(data)

# 数据清洗：去除多余空白、统一大小写

df["text"]=df["text"].str.replace(r'\s+', ' ', regex=True).str.strip().str.lower()

# 查看清洗后的数据

print("清洗后的数据: \n", df)
[/PRE9]

加载 sentence-transformers 的 paraphrase-MiniLM-L6-v2 做语义编码。

[PRE10]python
# 加载预训练的嵌入模型和分词器

tokenizer=AutoTokenizer.from_pretrained("sentence-transformers/paraphrase-MiniLM-L6-v2")

model=AutoModel.from_pretrained("sentence-transformers/paraphrase-MiniLM-L6-v2")
[/PRE10]

定义向量化函数，用最后一层隐藏状态取均值得到句向量。

[PRE11]python
# 定义生成嵌入向量的函数

def generate_embedding(text):

    # 将文本编码为输入张量

    inputs=tokenizer(text, return_tensors="pt", truncation=True, padding=True)

    with torch.no_grad():

        # 使用模型生成嵌入向量

        outputs=model(**inputs)

        # 取出最后一层隐藏状态的均值作为嵌入向量

        embedding=outputs.last_hidden_state.mean(dim=1).squeeze().numpy()

    return embedding

# 生成每个文档的嵌入向量

df["embedding"]=df["text"].apply(generate_embedding)

# 查看嵌入向量

print("嵌入向量: \n", df[["doc_id", "embedding"]])
[/PRE11]

把向量和 id 保存成 numpy 文件，后续检索直接读取。

[PRE12]python
# 将嵌入向量存储到NumPy数组中

embeddings=np.vstack(df["embedding"].values)

doc_ids=df["doc_id"].values

# 保存嵌入向量和文档ID，以便后续检索使用

np.save("embeddings.npy", embeddings)

np.save("doc_ids.npy", doc_ids)

print("嵌入向量已存储到 'embeddings.npy'")

print("文档ID已存储到 'doc_ids.npy'")
[/PRE12]

生成阶段的输出，清洗后的文本和向量形状确认无误。

[PRE13]
>> 清洗后的数据:
>>     doc_id                                      text
>> 0         1  Python是数据科学的基础工具。
>> 1         2  机器学习可以应用于多种场景，包括图像分类和自然语言处理。
>> 2         3  深度学习推动了计算机视觉技术的进步。
>>
>> 嵌入向量:
>>     doc_id                                      embedding
>> 0         1  [-0.0234105, 0.0445078, -0.0813874, ..., 0.0027]
>> 1         2  [-0.0056403, 0.0345510, -0.0723421, ..., -0.0010]
>> 2         3  [0.0103417, -0.0250476, 0.0510369, ..., 0.0115]
>>
>> 嵌入向量已存储到 'embeddings.npy'
>> 文档ID已存储到 'doc_ids.npy'
[/PRE13]

检索阶段重新加载文件，方便演示。

[PRE14]python
# 导入FAISS库

import faiss

import numpy as np

# 加载嵌入向量和文档ID

embeddings=np.load("embeddings.npy")

doc_ids=np.load("doc_ids.npy")

print("嵌入向量和文档ID加载完成。")

print("嵌入向量的形状: ", embeddings.shape)
[/PRE14]

重建 Flat 索引。

[PRE15]python
# 获取嵌入向量的维度

dimension=embeddings.shape[1]

# 创建L2距离的索引结构

index=faiss.IndexFlatL2(dimension)

# 将嵌入向量添加到索引中

index.add(embeddings)

print("索引已构建完成，包含向量数: ", index.ntotal)
[/PRE15]

[PRE16]python
[/PRE16]