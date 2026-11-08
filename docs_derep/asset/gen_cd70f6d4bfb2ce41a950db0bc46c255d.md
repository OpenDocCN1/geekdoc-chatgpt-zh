日志异常检测与推荐反馈闭环实战笔记

这篇笔记把两条常见的工程链路串成一条可复用的练习：先用模拟日志验证异常检测的触发与告警，再用 Flask 搭建一个带反馈采集和效果评估的推荐小系统，最后给出基于 FastAPI 的文本嵌入服务雏形。整套流程兼顾可观测性和闭环迭代，适合作为自媒体技术拆解的参考模板。

模拟部分会构造一条包含消息内容和时间戳的 JSON，发送后打印服务端返回并间隔 1 秒，避免请求过密。入口函数直接调用模拟器启动循环。

        json={"message": log_message, "timestamp": time.time()}

        )

        print(response.json())

        time.sleep(1)

if __name__ == "__main__":

    simulate_logs()

先把 Elasticsearch 拉起来作为日志存储与检索的基础。

[PRE0]bash

./bin/elasticsearch

随后启动应用服务，监听本地端口。

[PRE1]bash

python app.py

再起一个独立的日志生产脚本，持续写入测试数据。

[PRE2]bash

python simulate_logs.py

用 curl 触发一次检测接口，观察是否能命中异常。

[PRE3]bash

curl http://localhost:5000/detect

正常的写入会返回成功提示。

[PRE4]json

{

  "message": "日志记录成功"

}

当最近 10 分钟内异常日志占比超过 20% 时，系统会发出告警，示例中总共 20 条日志，异常 5 条，正好触发阈值。

[PRE5]text

主题: 异常检测告警

正文: 最近 10 分钟内检测到的异常日志比例超过 20%。

总日志数量: 20

异常日志数量: 5

异常样本里可以看到典型的数据库连接失败记录。

[PRE6]json

{

  "message": "异常: 数据库连接失败",

  "timestamp": "2024-11-28T10:00:00Z"

}

检测触发后会推送简短的状态消息。

[PRE7]json

{

  "message": "异常检测已触发"

}

切换到推荐侧，先安装必要的依赖。

[PRE8]bash

pip install flask pandas

核心服务使用 Flask 暴露两个接口：/feedback 用于接收用户点击与评分并更新内存表，/evaluate 用于计算点击率和平均评分。下方是完整的服务实现。

[PRE9]python

from flask import Flask, request, jsonify

import pandas as pd

import logging

import json

###### 初始化 Flask 应用

app = Flask(__name__)

###### 配置日志

logging.basicConfig(level=logging.INFO, format='%(asctime)s-%(message)s')

logger = logging.getLogger()

###### 模拟推荐数据与用户反馈

recommendation_data = pd.DataFrame({

    "user_id": [1, 2, 3, 4],

    "item_id": ["A", "B", "C", "D"],

    "recommended_score": [0.9, 0.85, 0.75, 0.8],

    "user_click": [1, 0, 1, 0],  # 1 表示点击, 0 表示未点击

    "user_rating": [5, None, 4, None]  # 用户反馈评分

})

###### 接收用户反馈

@app.route("/feedback", methods=["POST"])

def collect_feedback():

    try:

        data = request.json

        if not data or "user_id" not in data or "item_id" not in data:

            return jsonify({"error": "缺少必要字段"}), 400

        # 更新推荐数据中的反馈

        global recommendation_data

        recommendation_data.loc[

            (recommendation_data["user_id"] == data["user_id"]) &

            (recommendation_data["item_id"] == data["item_id"]),

            ["user_click", "user_rating"]

        ] = [data.get("user_click", 0), data.get("user_rating", None)]

        return jsonify({"message": "反馈记录成功"}), 200

    except Exception as e:

        logger.error(f"反馈记录失败: {e}")

        return jsonify({"error": "反馈记录失败"}), 500

###### 推荐效果评估

@app.route("/evaluate", methods=["GET"])

def evaluate_recommendation():

    try:

        global recommendation_data

        # 计算点击率

        total_recommendations = len(recommendation_data)

        total_clicks = recommendation_data["user_click"].sum()

        click_through_rate = total_clicks / total_recommendations

        # 计算平均评分（排除未评分的数据）

        rated_items = recommendation_data["user_rating"].notnull().sum()

        average_rating = recommendation_data["user_rating"].mean()

        result = {

            "total_recommendations": total_recommendations,

            "total_clicks": int(total_clicks),

            "click_through_rate": round(click_through_rate, 2),

            "rated_items": int(rated_items),

            "average_rating": round(average_rating, 2) if rated_items > 0 else None

        }

        return jsonify(result), 200

    except Exception as e:

        logger.error(f"评估失败: {e}")

        return jsonify({"error": "评估失败"}), 500

if __name__ == "__main__":

    app.run(host="0.0.0.0", port=5000)

客户端脚本先推送两条用户反馈，再拉取一次评估结果。

[PRE10]python

import requests

###### 模拟用户反馈

def send_feedback():

    feedback_data = [

        {"user_id": 2, "item_id": "B", "user_click": 1, "user_rating": 4},

        {"user_id": 4, "item_id": "D", "user_click": 1, "user_rating": 5},

    ]

    for feedback in feedback_data:

        response = requests.post("http://localhost:5000/feedback", json=feedback)

        print(response.json())

###### 模拟评估

def evaluate_system():

    response = requests.get("http://localhost:5000/evaluate")

    print(response.json())

if __name__ == "__main__":

    send_feedback()

    evaluate_system()

反馈写入成功会返回对应消息。

[PRE11]json

{

    "message": "反馈记录成功"

}

更新后评估指标如下：推荐总数 4，累计点击 3，点击率 0.75，已评分条目 3，平均分 4.67。

[PRE12]json

{

  "total_recommendations": 4,

  "total_clicks": 3,

  "click_through_rate": 0.75,

  "rated_items": 3,

  "average_rating": 4.67

}

日志层面可以看到反馈记录与评估完成的时序。

[PRE13]text

2024-11-28 15:00:00-反馈记录成功

2024-11-28 15:01:00-推荐系统评估完成

最后给出一个基于 Transformers 的嵌入服务骨架，采用 FastAPI 暴露接口，模型默认使用 bert-base-uncased，可根据设备自动选择 CUDA 或 CPU。

[PRE14]python

###### Step 1: 导入必要的库

from fastapi import FastAPI, HTTPException

from transformers import AutoTokenizer, AutoModel

import torch

import uvicorn

import numpy as np

###### Step 2: 配置推荐系统参数与模型路径

MODEL_NAME="bert-base-uncased"  # 可替换为任意大语言模型

DEVICE="cuda" if torch.cuda.is_available() else "cpu"

###### Step 3: 初始化模型与分词器

tokenizer=AutoTokenizer.from_pretrained(MODEL_NAME)

model=AutoModel.from_pretrained(MODEL_NAME).to(DEVICE)

###### Step 4: 定义嵌入生成函数

def generate_embedding(text):

    """

    使用大语言模型生成文本嵌入。

    :param text: 输入文本

    :return: 嵌入向量

    """

    inputs=tokenizer(text, return_tensors="pt", truncation=True,

                     padding=True, max_length=128).to(DEVICE)

    outputs=model(**inputs)