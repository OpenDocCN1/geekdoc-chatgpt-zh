# 用 Qwen3 + RAG 搭一个只说手册、不瞎编的客服

做跨境客服最怕模型自由发挥。今天这个方案核心就一条：模型只能在给定的资料里找答案，找不到就老实回“找不到”。检索用 BM25，先把相关段落筛出来，再喂给 Qwen3-1.7B 做严格约束生成。

先看模型怎么懒加载。一次初始化后复用，避免重复下载和占用。生成时把输入部分截掉，只返回新增内容，保证输出干净。

[PRE0]python

from modelscope import AutoModelForCausalLM, AutoTokenizer

import torch

##### 模型加载（单例模式优化）

model_name = "Qwen/Qwen3-1.7B"

device = "cuda" if torch.cuda.is_available() else "cpu"

def get_model_tokenizer():

    if not hasattr(get_model_tokenizer, "model"):

        model = AutoModelForCausalLM.from_pretrained(

            model_name,

            torch_dtype="auto",

            device_map=device,

            trust_remote_code=True

        )

        tokenizer = AutoTokenizer.from_pretrained(model_name)

        get_model_tokenizer.model = model

        get_model_tokenizer.tokenizer = tokenizer

    return get_model_tokenizer.model, get_model_tokenizer.tokenizer

def generate_response(prompt):

    model, tokenizer = get_model_tokenizer()

    inputs = tokenizer([prompt], return_tensors="pt").to(device)

    generated_ids = model.generate(

        **inputs,

        max_new_tokens=512

    )

    # 过滤输入部分并解码

    output_ids = generated_ids[0][len(inputs.input_ids[0]):]

    return tokenizer.decode(output_ids, skip_special_tokens=True).strip()

def customer_chat(query, informations):

    # 构建RAG增强Prompt

    system_prompt = (

        f"""

        你是一个跨境智能客服服务器，你现在需要回答用户的提问，使用如下知识和内容：'{informations}'

        你在回答问题时只能从给你的知识和内容中进查找，如果找不到对应的内容，就必须回答'找不到'。

        你不能回答给你的参考资料之外的内容。

        """

    )

    messages = [

        {"role": "system", "content": system_prompt},

        {"role": "user", "content": query}

    ]

    tokenizer = get_model_tokenizer()[1]

    prompt = tokenizer.apply_chat_template(

        messages,

        tokenize=False,

        add_generation_prompt=True

    )

    # 生成完整响应

    return generate_response(prompt)

##### 使用示例

if __name__ == "__main__":

    with open("phone.txt", "r", encoding="utf-8") as f:

        content = f.read()

    informations = content

    # 模拟RAG检索到的信息

    queries = [

        "手机的地域范围是哪里？",

        "我在什么情况下可以免费更换手机？",

        "手机的质保期是多久？"

    ]

    for query in queries:

        response = customer_chat(query, informations)

        print(f"Q: {query}\nA: {response}\n{'=' * 40}")

客服对话层用系统提示把边界锁死：回答必须基于传入的 informations，检索不到就返回找不到，不允许 hallucination。真实使用时 informations 由检索模块提供，而不是整本手册。

检索部分用经典 BM25。打分公式如下：

[PRE1]
Score(Query, D_i) = \sum_{i}^{n} W_i \cdot R(Q_i, D_j)

依赖安装注意下划线：

[PRE2]
pip install rank_bm25    #注意下画线

下面是中文分词不友好时的字符级 BM25 实现，直接把每个文档拆成字符列表来算相似度，返回最接近的 top_n 条：

[PRE3]
def get_top_n_sim_text(query: str, documents: List[str],top_n = 3):

    tokenized_corpus = []

    for doc in documents:

        text = []

        for char in doc:

            text.append(char)

        tokenized_corpus.append(text)

    bm25 = BM25Okapi(tokenized_corpus)

    tokenized_query = [char for char in query]

    #doc_scores = bm25.get_scores(tokenized_query)  # array([0., 0.93729472, 0.])

    results = bm25.get_top_n(tokenized_query, tokenized_corpus, n=top_n)

    results = ["".join(res) for res in results]

    return results

简单验证一下，找到与查询最相近的句子：

[PRE4]
import utils

prompt_text = "明天是什么天气"

context_list = ["哪个颜色好看","今天晚上吃什么","你家电话多少","明天的天气是晴天","晚上的月亮好美呀"]

sim_results = utils.get_top_n_sim_text(query=prompt_text, documents=context_list, top_n=1)

print(sim_results)

结果：

[PRE5]
['明天的天气是晴天']

把检索到的片段拼成 informations 喂给 customer_chat，就得到一个可控、只说资料里的客服。整个流程就是：BM25 召回 → 构造强约束 Prompt → Qwen3 生成 → 只输出手册内容。

[PRE6]
from modelscope import AutoModelForCausalLM, AutoTokenizer

import torch

##### 模型加载（单例模式优化）

model_name = "Qwen/Qwen3-1.7B"

device = "cuda" if torch.cuda.is_available() else "cpu"

def get_model_tokenizer():

    if not hasattr(get_model_tokenizer, "model"):

        model = AutoModelForCausalLM.from_pretrained(

            model_name,