从日志到告警：推荐系统可观测性搭建全流程

想让推荐系统的表现可量化、可追溯，第一步是把用户行为和请求延迟沉淀下来并形成闭环监控。下面的示例展示了从采集、传输、存储到异常检测的完整链路，兼顾日常运维和自动化告警。

监控指标样例
request_latency_seconds_sum{endpoint="/user_action"} 0.123456

request_latency_seconds_count{endpoint="/user_action"} 10

###### HELP user_action_count 用户行为计数

###### TYPE user_action_count counter

user_action_count{user_id="user_123",action="click"} 1.0

user_action_count{user_id="user_45",action="purchase"} 1.0

指标先给出请求延迟的累计与次数，以及以 counter 类型记录的用户行为计数，为后续分析提供基准。

采集与写入
本地先用 Flask 提供一个轻量的日志接收接口，配合轮转日志保证文件不无限增长。日志格式采用结构化 JSON，便于后续解析。

[PRE2]python

###### 文件名: log_collector.py

import logging

from logging.handlers import RotatingFileHandler

from flask import Flask, request, jsonify

import time

app = Flask(__name__)

###### 配置日志文件

LOG_FILE = "/path/to/logs/recommendation_system.log"

handler = RotatingFileHandler(LOG_FILE, maxBytes=5*1024*1024, backupCount=3)

formatter = logging.Formatter(

    '{"timestamp": "%(asctime)s", "level": "%(levelname)s", "message": "%(message)s"}')

handler.setFormatter(formatter)

logger = logging.getLogger("recommendation_logger")

logger.setLevel(logging.INFO)

logger.addHandler(handler)

@app.route('/log', methods=['POST'])

def log_action():

    """接收用户行为日志并记录"""

    data = request.json

    user_id = data.get('user_id')

    action = data.get('action')

    recommendation = data.get('recommendation')

    timestamp = time.time()

    if not user_id or not action or not recommendation:

        return jsonify({'error': '缺少必要字段'}), 400

    log_message = f'用户:{user_id}, 行为:{action}, 推荐:{recommendation}, 时间戳:{timestamp}'

    logger.info(log_message)

    return jsonify({'message': '日志记录成功', 'log': log_message})

if __name__ == '__main__':

    app.run(host='0.0.0.0', port=5000)

为了验证采集可用性，用一个简单的模拟脚本向接口批量投递行为数据。

[PRE3]python

###### 文件名: simulate_logs.py

import requests

import random

USER_BEHAVIORS = ['click', 'view', 'add_to_cart', 'purchase']

RECOMMENDATIONS = ['item_1', 'item_2', 'item_3']

def simulate_logs():

    user_id = f"user_{random.randint(1, 100)}"

    action = random.choice(USER_BEHAVIORS)

    recommendation = random.choice(RECOMMENDATIONS)

    response = requests.post(

        "http://localhost:5000/log",

        json={"user_id": user_id, "action": action, "recommendation": recommendation},

    )

    print(response.json())

if __name__ == "__main__":

    for _ in range(10):

        simulate_logs()

传输与管道
Filebeat 负责读取本地日志文件并推送到 Logstash，Logstash 解析 JSON 并写入 Elasticsearch，同时在控制台打印便于调试。

[PRE1]yaml

filebeat.inputs:

  - type: log

    enabled: true

    paths:

      - /path/to/logs/*.log  # 日志路径

output.logstash:

  hosts: ["localhost:5044"]

[PRE0]conf

input {

  beats {

    port => 5044

  }

}

filter {

  json {

    source => "message"

  }

}

output {

  elasticsearch {

    hosts => ["localhost:9200"]

    index => "recommendation_logs"

  }

  stdout { codec => rubydebug }

}

启动顺序通常是先起 Elasticsearch，再起 Logstash，随后 Filebeat，最后是采集服务与模拟脚本。

[PRE4]bash

./bin/elasticsearch

[PRE5]bash

./bin/logstash -f logstash.conf

[PRE6]bash

./filebeat -e

[PRE7]bash

python log_collector.py

[PRE8]bash

python simulate_logs.py

写入结果示例
接口返回确认信息。

[PRE9]json

{

  "message": "日志记录成功",

  "log": "用户:user_45, 行为:view, 推荐:item_2, 时间戳:1691234567.123"

}

日志文件中的结构化记录。

[PRE10]json

{

  "timestamp": "2024-11-28 10:00:00.123", "level": "INFO", "message": "用户:user_45, 行为:view, 推荐:item_2, 时间戳:1691234567.123"

}

{

  "timestamp": "2024-11-28 10:00:02.456", "level": "INFO", "message": "用户:user_22, 行为:click, 推荐:item_1, 时间戳:1691234569.456"

}

Elasticsearch 中的文档形态。

[PRE11]json

{

  "_index": "recommendation_logs",

  "_source": {

    "timestamp": "2024-11-28T10:00:00Z",

    "level": "INFO",

    "message": "用户:user_45, 行为:view, 推荐:item_2, 时间戳:1691234567.123"

  }

}

智能告警
在日志落库的基础上增加异常检测与邮件通知能力。依赖 Elasticsearch 客户端查询最近 10 分钟日志，统计异常比例，超过阈值即发送告警。

[PRE12]python

pip install elasticsearch

[PRE13]python

from flask import Flask, request, jsonify

from elasticsearch import Elasticsearch

import smtplib

from email.mime.text import MIMEText

from email.mime.multipart import MIMEMultipart

import logging

###### 初始化 Flask 应用

app = Flask(__name__)

###### 配置 Elasticsearch 客户端

es = Elasticsearch("http://localhost:9200")

###### 邮件配置

SMTP_SERVER = "smtp.example.com"

SMTP_PORT = 587

EMAIL_ADDRESS = "your_email@example.com"

EMAIL_PASSWORD = "your_password"

###### 配置日志

logging.basicConfig(level=logging.INFO, format="%(asctime)s - %(message)s")

logger = logging.getLogger()

###### Elasticsearch 索引名称

INDEX_NAME = "recommendation_logs"

###### 邮件告警功能

def send_alert_email(subject, message, recipient="admin@example.com"):

    try:

        msg = MIMEMultipart()

        msg["From"] = EMAIL_ADDRESS

        msg["To"] = recipient

        msg["Subject"] = subject

        # 添加邮件正文

        msg.attach(MIMEText(message, "plain"))

        # 发送邮件

        with smtplib.SMTP(SMTP_SERVER, SMTP_PORT) as server:

            server.starttls()

            server.login(EMAIL_ADDRESS, EMAIL_PASSWORD)

            server.sendmail(EMAIL_ADDRESS, recipient, msg.as_string())

        logger.info("告警邮件发送成功")

    except Exception as e:

        logger.error(f"邮件发送失败: {e}")

###### 检测异常行为

def detect_anomalies():

    try:

        # 查询最近 10 分钟的用户行为日志

        query = {

            "query": {

                "range": {

                    "timestamp": {

                        "gte": "now-10m",

                        "lte": "now"

                    }

                }

            }

        }

        response = es.search(index=INDEX_NAME, body=query)

        # 分析日志，检测异常行为

        abnormal_count = 0

        total_logs = len(response["hits"]["hits"])

        for hit in response["hits"]["hits"]:

            message = hit["_source"]["message"]

            if "error" in message.lower() or "异常" in message:

                abnormal_count += 1

        # 如果异常比例超过 20%，触发告警

        if total_logs > 0 and (abnormal_count / total_logs) > 0.2:

            subject = "异常检测告警"

            message = f"最近 10 分钟内检测到的异常日志比例超过 20%。\n总日志数量: {total_logs}\n异常日志数量: {abnormal_count}"

            send_alert_email(subject, message)

    except Exception as e:

        logger.error(f"异常检测失败: {e}")

###### 接收用户行为日志

@app.route("/log", methods=["POST"])

def log_action():

    try:

        data = request.json

        if not data or "message" not in data:

            return jsonify({"error": "缺少必要字段"}), 400

        # 写入 Elasticsearch

        es.index(index=INDEX_NAME, body={

            "message": data["message"],

            "timestamp": data.get("timestamp", "now")

        })

        return jsonify({"message": "日志记录成功"})

    except Exception as e:

        logger.error(f"日志记录失败: {e}")

        return jsonify({"error": "日志记录失败"}), 500

###### 异常检测触发器

@app.route("/detect", methods=["GET"])

def trigger_detection():

    detect_anomalies()

    return jsonify({"message": "异常检测已触发"})

if __name__ == "__main__":

    app.run(host="0.0.0.0", port=5000)

模拟异常场景时可以投递包含关键词的日志，便于验证检测逻辑。

[PRE14]python

import requests

import random

import time

###### 模拟用户行为日志

def simulate_logs():

    messages = [

        "用户登录成功",

        "推荐结果加载正常",

        "异常：数据库连接失败",

        "点击率数据更新正常",

        "异常：缺少用户行为日志"

    ]

    for _ in range(20):

        log_message = random.choice(messages)

        response = requests.post(

            "http://localhost:5000/log",