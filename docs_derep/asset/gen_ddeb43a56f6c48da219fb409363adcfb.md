# 用向量检索给大模型装上搜索引擎

想让回答既可信又能追溯来源，RAG 是目前最顺手的思路。下面用 Qdrant 做向量库，把文档切分后存进去，再通过检索把相关片段拼进提示词，让模型只在给定的上下文里作答。

先搭客户端，本地服务最常见，内存或磁盘路径也方便调试。

client = QdrantClient(host="localhost", port=6333)

[PRE0]python
# client = QdrantClient(":memory:")
# 或
# client = QdrantClient(path="path/to/db")

建集合时指定向量维度 1536，距离度量用余弦，名字统一叫 doc_qa。

[PRE1]python
from qdrant_client.models import Distance, VectorParams

client.recreate_collection(

    collection_name="doc_qa",

    vectors_config=VectorParams(size=1536, distance=Distance.COSINE),

)

需要清空重来时直接删除集合。

[PRE2]python
client.delete_collection("doc_qa")

准备元数据，把内容、标题、小标题和 token 数一起保存，方便后续截断。

[PRE3]python
payload=[

[PRE4]
{"content": v.content, "heading": v.heading, "title": v.title,
 "tokens": v.tokens} for v in df.itertuples()
]

把向量和负载一起写入。

[PRE5]
client.upload_collection(
    collection_name="doc_qa",
    vectors=emds,
    payload=payload
)

检索流程很简单：问题先转成向量，再在集合里找最相似的几条。

[PRE6]
query = "Who won the 2020 Summer Olympics men's high jump?"
query_vector = get_embedding(query, engine="text-embedding-ada-002")
hits = client.search(
    collection_name="doc_qa",
    query_vector=query_vector,
    limit=5
)

返回的 ScoredPoint 包含分数和对应的 payload。

[PRE7]
ScoredPoint(id=236, version=3, score=0.90316474, payload={'content': '<CONTENT>', 'heading': 'Summary', 'title': "Athletics at the 2020 Summer Olympics – Men's high jump", 'tokens': 275}, vector=None)

为了控制提示词长度，设定单段上限和分隔符。

[PRE8]
MAX_SECTION_LEN = 500
SEPARATOR = "\n* "
separator_len = 3

封装一个构造提示词的函数：检索前 5 条，按 token 累加不超过上限，拼接成上下文，并要求模型在信息不足时回答 I don't know。

[PRE9]
def construct_prompt(question: str) -> str:
    query_vector = get_embedding(question, engine="text-embedding-ada-002")
    hits = client.search(
        collection_name="doc_qa",
        query_vector=query_vector,
        limit=5
    )
    choose = []
    length = 0
    indexes = []
    for hit in hits:
        doc = hit.payload
        length += doc["tokens"] + separator_len
        if length > MAX_SECTION_LEN:
            break
        choose.append(SEPARATOR + doc["content"].replace("\n", " "))
        indexes.append(doc["title"] + doc["heading"])
    # 简单的日志
    print(f"Selected {len(choose)} document sections:")
    print("\n".join(indexes))
    header = """Answer the question as truthfully as possible using the provided context, and if the answer is not contained within the text below,
say "I don't know."\n\nContext:\n"""
    return header + "".join(choose) + "\n\n Q: " + question + "\n A:"

实际调用看看效果。

[PRE10]
prompt = construct_prompt("Who won the 2020 Summer Olympics men's high jump?")
print("===\n", prompt)

检索日志和生成的提示词如下。

[PRE11]
Selected 2 document sections:
Athletics at the 2020 Summer Olympics – Men's high jumpSummary
Athletics at the 2020 Summer Olympics – Men's long jumpSummary
===
  Answer the question as truthfully as possible using the provided context, and if the answer is not contained within the text below, say "I don't know."
Context:
* <CONTENT 1>
* <CONTENT 2>
  Q: Who won the 2020 Summer Olympics men's high jump?
  A:

接下来的调用用两种 OpenAI 接口做对比。

[PRE12]
def complete(prompt: str) -> str:
    response = openai.Completion.create(
        prompt=prompt,
        temperature=0,
        max_tokens=300,
        top_p=1,
        frequency_penalty=0,
        presence_penalty=0,
        model="text-davinci-003"
    )
    ans = response["choices"][0]["text"].strip(" \n")
    return ans

[PRE13]
ans = complete(prompt)

聊天式调用。

[PRE14]
def ask(content: str) -> str:
    response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": content}]
    )
    ans = response.get("choices")[0].get("message").get("content")
    return ans

[PRE15]
ans = ask(prompt)

换一个奥运奖牌相关的问题。

[PRE16]
query = "In the 2020 Summer Olympics, how many gold medals did the country which won the most medals win?"
prompt = construct_prompt(query)
answer = complete(prompt)
print(f"\nQ: {query}\nA: {answer}")

检索到的片段和完成模型输出。

[PRE17]
Selected 2 document sections:
2020 Summer Olympics medal tableSummary
List of 2020 Summer Olympics medal winnersSummary
Q: In the 2020 Summer Olympics, how many gold medals did the country which won the most medals win?
A: The United States won the most medals overall, with 113, and the most gold medals, with 39.

用聊天模型再跑一次。

[PRE18]
answer = ask(prompt)
print(f"\nQ: {query}\nA: {answer}")

[PRE19]
Q: In the 2020 Summer Olympics, how many gold medals did the country which won the most medals win?
A: The country that won the most medals at the 2020 Summer Olympics was the United States, with 113 medals, including 39 gold medals.

当问题超出检索范围时，模型会按指令说不知道。

[PRE20]
query = "What is the tallest mountain in the world?"
prompt = construct_prompt(query)
answer = complete(prompt)
print(f"\nQ: {query}\nA: {answer}")

[PRE21]
Selected 3 document sections:
Sport climbing at the 2020 Summer Olympics – Men's combinedRoute-setting
Ski mountaineering at the 2020 Winter Youth Olympics – Boys' individualSummary
Ski mountaineering at the 2020 Winter Youth Olympics – Girls' individualSummary
Q: What is the tallest mountain in the world?
A: I don't know.

[PRE22]
answer = ask(prompt)
print(f"\nQ: {query}\nA: {answer}")

[PRE23]
Q: What is the tallest mountain in the world?
A: I don't know.

下面换一个文本分类的例子，演示同样的提示词构造思路在 tnews 数据集上的应用。

[PRE24]
import pnlp
lines = pnlp.read_file_to_list_dict("./dataset/tnews.json")
len(lines) == 10000

单条样例长这样。

[PRE25]
lines[59] == {
    "label": "101",
    "label_desc": "news_culture",
    "sentence": "上联：银笛吹开云天月，下联怎么对？",
    "keywords": ""
}

先看类别分布。

[PRE26]
from collections import Counter
ct = Counter([v["label_desc"] for v in lines])
ct.most_common() == [
    ('news_tech', 1089),
    ('news_finance', 956),
    ('news_entertainment', 910),
    ('news_world', 905),
    ('news_car', 791),
    ('news_sports', 767),
    ('news_culture', 736),
    ('news_military', 716),
    ('news_travel', 693),
    ('news_game', 659),
    ('news_edu', 646),
    ('news_agriculture', 494),
    ('news_house', 378),
    ('news_story', 215),
    ('news_stock', 45)
]

构造用于分类的提示模板。

[PRE27]
def get_prompt(text: str) -> str:
    prompt = f"""对给定文本进行分类，类别包括：科技、金融、娱乐、世界、汽车、运动、文化、军事、旅游、游戏、教育、农业、房产、社会、股票。

给定文本：

{text}

类别：

"""
    return prompt

[PRE28]
prompt = get_prompt(lines[0]["sentence"])
print(prompt)

渲染后的样子。

[PRE29]
对给定文本进行分类，类别包括：科技、金融、娱乐、世界、汽车、运动、文化、军事、旅游、游戏、教育、农业、房产、社会、股票。

给定文本：

上联：银笛吹开云天月，下联怎么对？

类别：

初始化 OpenAI 客户端。

[PRE30]
import openai
import os
OPENAI_API_KEY = os.environ.get("OPENAI_API_KEY")
openai.api_key = OPENAI_API_KEY

完成调用的基础封装。

[PRE31]
def complete(prompt: str) -> str:
    response = openai.Completion.create(
        prompt=prompt,