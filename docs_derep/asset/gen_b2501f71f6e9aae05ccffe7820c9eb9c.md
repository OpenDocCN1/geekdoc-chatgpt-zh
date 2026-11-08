# 向量检索与生成式问答的端到端示例

下面通过一个可运行的流程演示 Faiss 的 IVF 倒排索引如何加速相似向量检索，以及如何结合 Transformers 做基于上下文的生成式问答。整体思路是先用平方根经验法确定聚簇数，训练倒排索引并与全量 Flat 索引对比；随后加载预训练语言模型，配置采样参数并实现上下文生成，最后通过提示词优化适配企业问答场景。

# 设置倒排索引的聚类数（即簇数量），一般为数据量的平方根

nlist=int(np.sqrt(len(embeddings)))

# 使用倒排索引结构创建索引

quantizer=faiss.IndexFlatL2(dimension)

index_ivf=faiss.IndexIVFFlat(quantizer, dimension, nlist, faiss.METRIC_L2)

# 训练索引：先进行聚类，以便倒排索引结构加速检索

index_ivf.train(embeddings)

index_ivf.add(embeddings)

print("倒排索引已构建，包含向量数: ", index_ivf.ntotal)

IVF 的核心在于先聚类再搜索，用倒排结构缩小候选范围。接下来用同一个查询向量在 Flat 与 IVF 上各查一次，验证结果一致性并感受加速效果。

[PRE0]python

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

在小规模演示数据上，两种索引返回的 Top-3 文档 ID 与距离完全一致，说明 IVF 在查准的前提下提升了效率。

[PRE1]

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

检索环节确认无误后，进入生成式问答部分。先安装并导入 Transformers，加载分词器与语言模型，并根据硬件自动选择设备。

[PRE2]python

# 安装transformers库（如果未安装，请取消注释以下行）

# !pip install transformers

# 导入库

from transformers import AutoTokenizer, AutoModelForCausalLM

import torch

[PRE3]python

# 安装transformers库（如果未安装，请取消注释以下行）

# !pip install transformers

# 导入库

from transformers import AutoTokenizer, AutoModelForCausalLM

import torch

[PRE4]python

# 定义模型名称，可根据需求选择不同的模型

model_name="gpt2-medium"

# 加载模型和分词器

tokenizer=AutoTokenizer.from_pretrained(model_name)

model=AutoModelForCausalLM.from_pretrained(model_name)

# 检查是否有可用的GPU并将模型移动到GPU上

device="cuda" if torch.cuda.is_available() else "cpu"

model.to(device)

print(f"已加载生成模型: {model_name}, 设备: {device}")

生成质量受采样参数影响，下面统一配置最大长度、温度、top-k、top-p 等超参，便于复现。

[PRE5]python

# 配置生成参数

generation_config={

    "max_length": 150,      # 生成内容的最大长度

    "temperature": 0.7,     # 控制随机性

    "top_k": 50,            # 只保留前top_k个可能的下一个词

    "top_p": 0.95,          # nucleus sampling参数

    "num_return_sequences": 1  # 每次只生成一个回答

}

print("生成参数已配置完成。")

有了模型与参数，封装一个生成函数完成编码、采样生成与解码。

[PRE6]python

# 定义生成回答的函数

def generate_answer(context):

    # 将上下文编码为模型输入格式

    inputs=tokenizer(context, return_tensors="pt", truncation=True, padding=True).to(device)

    # 使用生成模型生成回答

    with torch.no_grad():

        outputs=model.generate(

            inputs["input_ids"],

            max_length=generation_config["max_length"],

            temperature=generation_config["temperature"],

            top_k=generation_config["top_k"],

            top_p=generation_config["top_p"],

            num_return_sequences=generation_config["num_return_sequences"],

            do_sample=True # 进行随机采样

        )

    # 将生成的ID序列解码为文本

    answer=tokenizer.decode(outputs[0], skip_special_tokens=True)

    return answer

用一段关于机器学习的简短上下文做快速验证，观察模型能否自然续写。

[PRE7]python

# 测试上下文

test_context="机器学习可以应用于多种场景，比如图像分类和自然语言处理。它依赖于"

# 调用生成函数生成回答

answer=generate_answer(test_context)

print("生成的回答：", answer)

[PRE8]text

>> 已加载生成模型：gpt2-medium，设备：cuda

>> 生成参数已配置完成。

>> 生成的回答：机器学习可以应用于多种场景，比如图像分类和自然语言处理。它依赖于大量的数据和计算能力来实现自动化的决策与预测。

为了适配企业问答，先确保模型已就绪，再构造带背景信息与问题的提示模板，提升回答的相关性。

[PRE9]python

# 重新确认模型和分词器是否已加载（如有需要）

from transformers import AutoTokenizer, AutoModelForCausalLM

import torch

model_name="gpt2-medium"

tokenizer=AutoTokenizer.from_pretrained(model_name)

model=AutoModelForCausalLM.from_pretrained(model_name)

device="cuda" if torch.cuda.is_available() else "cpu"

model.to(device)

[PRE10]python

# 示例提示词构造

def create_prompt(context, question):

    """

    构建用于生成回答的提示词。

    :param context: 上下文信息（例如文档内容或相关片段）

    :param question: 用户的具体问题

    :return: 构建好的提示词

    """

    prompt=f"企业问答系统：\n\n背景信息：\n{context}\n\n问题：{question}\n\n回答："

    return prompt

基于该模板封装优化后的生成函数，使用更保守的温度与采样参数，得到更稳健的业务回答。

[PRE11]python

def generate_optimized_answer(context, question):

    """

    生成优化后的回答，基于特定的企业问答系统场景。

    :param context: 提供的上下文内容

    :param question: 用户的问题

    :return: 模型生成的回答

    """

    # 使用优化后的提示词构造

    prompt=create_prompt(context, question)

    # 编码输入文本

    inputs=tokenizer(prompt, return_tensors="pt", truncation=True, padding=True).to(device)

    # 生成回答

    with torch.no_grad():

        outputs=model.generate(

            inputs["input_ids"],

            max_length=150,

            temperature=0.6,

            top_k=40,

            top_p=0.85,

            num_return_sequences=1,

            do_sample=True  # 随机采样

        )

    # 解码生成的文本

    answer=tokenizer.decode(outputs[0], skip_special_tokens=True)

    return answer

最后用一份公司年假政策作为上下文，提问并生成符合业务语气的回答，完整呈现检索加生成的闭环。

[PRE12]python

# 设置企业内部问答示例

context="公司政策规定，所有员工在入职满一年后可以享受每年10天的带薪年假。"

question="请问公司对于带薪年假的政策是怎样的？"

# 调用优化后的生成函数

answer=generate_optimized_answer(context, question)

print("生成的回答：", answer)