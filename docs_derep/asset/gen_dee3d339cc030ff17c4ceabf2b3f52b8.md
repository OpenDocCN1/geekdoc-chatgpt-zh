# 本地模型就能玩转的电影灵感站

想做一个输入口味关键词就能立刻吐出片单的工具，却不想依赖在线 API？下面这套方案把电影文本本地化向量化，用 FAISS 做毫秒级相似检索，再用 Gradio 套一层可视化交互，整套流程全在自己机器上跑。先看一个最简启动示意，整体逻辑后面会展开。

if __name__ == "__main__":

    print("系统启动中...请确保Ollama服务已运行！")

    interface.launch(server_name="127.0.0.1", share=True)

[PRE0]
pip install gradio faiss transformers requests

依赖就这四项，装好后就能直接跑本地的中文语义模型。准备好电影数据目录和模型权重，按下面的完整脚本搭建索引与检索即可。

[PRE1]python
import os

import gradio as gr

import faiss

import numpy as np

import json

from transformers import AutoTokenizer, BertModel

import torch

import random

DEVICE = "cuda" if torch.cuda.is_available() else "cpu"

os.environ["KMP_DUPLICATE_LIB_OK"] = "TRUE"

##### 配置参数

DATA_DIR = "movies"  # 电影描述数据目录

EMBEDDING_MODEL_PATH = "models/bge-small-zh-v1.5"  # 本地模型路径

TOP_K = 3  # 推荐数量

##### 初始化模型

tokenizer = AutoTokenizer.from_pretrained(EMBEDDING_MODEL_PATH)

model = BertModel.from_pretrained(EMBEDDING_MODEL_PATH).to(DEVICE)

##### 处理电影数据（每个 txt 文件包含电影名称和描述）

movies = []

for filename in os.listdir(DATA_DIR):

    if filename.endswith(".txt"):

        with open(os.path.join(DATA_DIR, filename), "r", encoding="utf-8") as f:

            content = f.read().strip().split("\n", 1)

            if len(content) == 2:

                movies.append({"title": content[0], "description": content[1]})

##### 生成嵌入向量

def get_embedding(text):

    inputs = tokenizer(text, return_tensors="pt",

                       padding=True, truncation=True, max_length=512).to(DEVICE)

    with torch.no_grad():

        outputs = model(**inputs)

    return torch.nn.functional.normalize(outputs.last_hidden_state[:, 0], p=2,

                                         dim=1).cpu().numpy()

##### 创建 FAISS 索引

movie_descriptions = [m["description"] for m in movies]

embeddings = np.vstack([get_embedding(desc) for desc in movie_descriptions])

index = faiss.IndexFlatL2(embeddings.shape[1])

index.add(embeddings)

##### 推荐函数

def recommend(query=None):

    if query:  # 基于用户输入推荐

        query_embedding = get_embedding(query)

    else:  # 一键随机推荐

        random_movie = random.choice(movie_descriptions)

        query_embedding = get_embedding(random_movie)

    distances, indices = index.search(query_embedding, TOP_K)

    return "\n\n".join([

        f"🎬 {movies[i]['title']}\n📝 {movies[i]['description'][:100]}..."

        for i in indices[0]

    ])

##### 创建界面

interface = gr.Interface(

    fn=lambda x: recommend(x) if x else recommend(),

    inputs=gr.Textbox(label="输入偏好（可选）", placeholder="例如：科幻太空冒险..."),

    outputs=gr.Textbox(label="推荐结果"),

    title="电影智能推荐系统",

    description="输入偏好关键词或直接点击提交获取推荐",

    allow_flagging="never",

    examples=[

        ["科幻时间旅行"],

        ["浪漫爱情故事"],

        [""]  # 一键推荐示例

    ]

)

##### 运行程序

if __name__ == "__main__":

    print("推荐系统已启动！")

    interface.launch(server_name="127.0.0.1", share=True)

[PRE2]

跑起来后，空输入会触发随机种子推荐，填入口味描述则基于语义相似度返回 Top3 片名与摘要，完全离线也能用。把 movies 目录换成自己的 txt 集合，就能快速复用到书籍、音乐等其他内容推荐场景。