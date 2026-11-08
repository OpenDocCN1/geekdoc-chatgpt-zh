# BERT推荐 ONNX 加速与异步部署实战

下面是一套从数据准备、BERT 微调、ONNX 导出优化，到基于 FastAPI + Celery 异步在线推理的完整落地链路。重点在于模型轻量化、推理加速以及高并发服务能力的构建。

优化后的推理调用示例
optimized_result = optimized_session.run(None, onnx_inputs)
print(f"ONNX 优化后推理结果: {optimized_result}")

[PRE0]text
导出模型为ONNX格式...

模型已成功导出为 bert_recommendation.onnx

ONNX模型验证成功

使用ONNX Runtime进行推理...

ONNX推理结果: [[0.34, 0.28, 0.38]]

优化ONNX模型...

优化后的模型已保存到 bert_recommendation_optimized.onnx

使用优化后的ONNX模型运行推理...

优化后ONNX推理结果: [[0.35, 0.27, 0.38]]
[PRE1]bash
pip install fastapi uvicorn onnxruntime celery redis
[PRE2]python
pip install celery redis
[PRE3]python
from celery import Celery

import onnxruntime as ort

import numpy as np

from time import sleep

###### 创建一个Celery实例

celery_app = Celery('recommendation', broker='redis://localhost:6379/0')

###### 加载ONNX模型

onnx_model_path = 'bert_recommendation_optimized.onnx'

session = ort.InferenceSession(onnx_model_path)

###### 定义推理任务

@celery_app.task

def recommend_task(user_id, query):

    # 模拟生成输入张量

    inputs = np.array([[user_id, query]], dtype=np.string_)

    onnx_inputs = {

        'input_ids': inputs

    }

    # 进行推理

    try:

        result = session.run(None, onnx_inputs)

        return result[0].tolist()

    except Exception as e:

        return f"Error in inference: {e}"
[PRE4]python
from fastapi import FastAPI, HTTPException

from celery.result import AsyncResult

from celery_app import recommend_task

###### FastAPI应用

app = FastAPI()

@app.post("/predict_async/")

async def predict_async(user_id: str, query: str):

    # 异步调用Celery任务

    task = recommend_task.apply_async(args=[user_id, query])

    # 等待任务完成并返回任务ID

    return {"task_id": task.id}

@app.get("/result/{task_id}")

async def get_result(task_id: str):

    # 获取任务状态

    task = AsyncResult(task_id)

    if task.state == 'PENDING':

        return {"status": "Task is still pending"}

    elif task.state == 'SUCCESS':

        return {"recommendations": task.result}

    elif task.state == 'FAILURE':

        return {"status": "Task failed", "error": task.info}
[PRE5]python
###### 启动FastAPI服务器

uvicorn app_with_celery:app --host 0.0.0.0 --port 8000

###### 启动Celery工作进程

celery -A celery_app.celery_app worker --loglevel=info
[PRE6]python
import pandas as pd

import numpy as np

from sklearn.model_selection import train_test_split

from transformers import (BertTokenizer, BertForSequenceClassification,

                          Trainer, TrainingArguments)

import torch

###### 假设数据集为CSV文件, 包含user_id, item_id和interaction列

data=pd.read_csv('user_item_interaction.csv')

###### 处理数据: 将user_id和item_id映射为文本序列

data['text']=data['user_id'].astype(str)+" "+data['item_id'].astype(str)

###### 将interaction转换为标签

data['label']=data['interaction'].apply(lambda x: 1 if x > 0 else 0)

###### 拆分训练集和测试集

train_data, test_data=train_test_split(data, test_size=0.2)

###### 加载BERT tokenizer

tokenizer=BertTokenizer.from_pretrained('bert-base-uncased')

###### 定义tokenize函数

def tokenize_function(examples):

    return tokenizer(examples['text'],

                     padding="max_length", truncation=True)

###### 将数据转换为适合模型输入的格式

train_encodings=train_data['text'].apply(

                    lambda x: tokenize_function({'text': x}))

test_encodings=test_data['text'].apply(

                    lambda x: tokenize_function({'text': x}))

###### 创建PyTorch数据集类

class RecommendationDataset(torch.utils.data.Dataset):

    def __init__(self, encodings, labels):

        self.encodings=encodings

        self.labels=labels

    def __getitem__(self, idx):

        item={key: torch.tensor(val[idx]) for key,

              val in self.encodings.items()}

        item['labels']=torch.tensor(self.labels[idx])

        return item

    def __len__(self):

        return len(self.labels)

train_dataset=RecommendationDataset(train_encodings, train_data['label'])

test_dataset=RecommendationDataset(test_encodings, test_data['label'])
[PRE7]python
from transformers import Trainer, TrainingArguments

###### 加载预训练的BERT模型

model=BertForSequenceClassification.from_pretrained(

    'bert-base-uncased', num_labels=2)

###### 定义训练参数

training_args=TrainingArguments(

    output_dir='./results',      # 输出目录

    num_train_epochs=3,          # 训练epoch次数

    per_device_train_batch_size=8, # 每个设备的batch size

    per_device_eval_batch_size=8,  # 每个设备的eval batch size

    warmup_steps=500,            # 预热步数

    weight_decay=0.01,           # 权重衰减

    logging_dir='./logs',        # 日志目录

    logging_steps=10,            # 每10步记录一次

    evaluation_strategy="epoch"   # 每个epoch后进行评估

)

###### 创建Trainer对象

trainer=Trainer(

    model=model,                 # 模型

    args=training_args,          # 训练参数

    train_dataset=train_dataset, # 训练集

    eval_dataset=test_dataset    # 测试集

)

###### 开始训练

trainer.train()
[PRE8]python
###### 保存微调后的模型

model.save_pretrained('finetuned_bert_recommendation')

###### 使用ONNX优化模型

import torch

from transformers import BertForSequenceClassification

model=BertForSequenceClassification.from_pretrained(

    'finetuned_bert_recommendation')

dummy_input=torch.ones(1, 128)  # 输入一个虚拟的张量，作为示例

torch.onnx.export(model, dummy_input, 'finetuned_bert_recommendation.onnx',

    opset_version=11)
[PRE9]python
from fastapi import FastAPI

import torch