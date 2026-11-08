大模型垂直微调与智能填空两类实操

想把通用大模型用到具体业务里，常见做法有两种：用少量领域对话对模型做定向训练，让它在专业场景下更准；或者在不改动权重的前提下，通过工具化调用实现上下文补全。下面用两段可直接运行的示例来说明思路。

第一类是把 GPT-2 这类通用预训练模型迁移到医疗问答。为了让模型学会问答格式，准备了三条示例对话，分别覆盖头晕原因、糖尿病饮食和高血压管理。语料经分词器编码后生成 input_ids、attention_mask 并复制一份作为 labels，随后封装成 PyTorch Dataset。训练配置采用 3 个 epoch、每卡 batch size 2、每 10 步保存一次且最多保留 2 个 checkpoint，日志写入 ./logs。训练完成后，用 generate 接口给定一个未见过的提问 “问：如何缓解疲劳？”，模型会基于微调后的知识给出带答案的补全。

[PRE0]python

import torch

from transformers import AutoModelForCausalLM, AutoTokenizer, TrainingArguments, Trainer, Dataset

# 加载通用预训练模型

model_name="gpt2"

tokenizer=AutoTokenizer.from_pretrained(model_name)

model=AutoModelForCausalLM.from_pretrained(model_name)

# 医疗领域微调语料

medical_data=[

    {"input_text": "问：头晕的常见原因是什么？ 答：头晕可能与疲劳、低血糖、颈椎问题或内耳疾病相关。"},

    {"input_text": "问：糖尿病患者的饮食注意事项？ 答：应避免高糖、高脂肪食品，控制总热量摄入，选择低升糖指数食物。"},

    {"input_text": "问：高血压的日常管理方法？ 答：保持低盐饮食，适量运动，避免过度精神紧张。"}

]

# 数据预处理

def preprocess_data(data, tokenizer):

    inputs=tokenizer([d["input_text"] for d in data], return_tensors="pt", padding=True, truncation=True)

    labels=inputs.input_ids.clone()  # 标签与输入一致

    return {"input_ids": inputs.input_ids, "attention_mask": inputs.attention_mask, "labels": labels}

processed_data=preprocess_data(medical_data, tokenizer)

# 创建Dataset

class MedicalDataset(Dataset):

    def __init__(self, data):

        self.data=data

    def __len__(self):

        return len(self.data["input_ids"])

    def __getitem__(self, idx):

        return {key: self.data[key][idx] for key in self.data}

dataset=MedicalDataset(processed_data)

# 设置训练参数

training_args=TrainingArguments(

    output_dir="./medical_model",

    num_train_epochs=3,

    per_device_train_batch_size=2,

    save_steps=10,

    save_total_limit=2,

    logging_dir="./logs"

)

# 创建Trainer

trainer=Trainer(

    model=model,

    args=training_args,

    train_dataset=dataset

)

# 模型微调

trainer.train()

# 测试领域适配后的模型

def generate_response(prompt, model, tokenizer, max_length=50):

    inputs=tokenizer(prompt, return_tensors="pt")

    outputs=model.generate(inputs.input_ids, max_length=max_length, num_return_sequences=1)

    return tokenizer.decode(outputs[0], skip_special_tokens=True)

# 测试模型

prompt="问：如何缓解疲劳？"

response=generate_response(prompt, model, tokenizer)

print("模型响应：", response)

第二类是不训练模型，而是调用 DeepSeek API 构建一个可填空的文本编辑助手。核心是先声明一个填空功能，描述为在前后文之间生成合适内容，并要求提供 context_before 与 context_after 两个字符串参数。API 地址与密钥作为全局配置，后续所有请求都基于此。

[PRE1]python

# -*- coding: utf-8 -*-

"""

填空生成技术实现示例

使用DeepSeek-V3 API构建一个文本编辑助手

"""

import requests

import json

import time

import re

from typing import List, Dict

# DeepSeek API配置

API_BASE_URL="https://api.deepseek.com/v1"

API_KEY="your_deepseek_api_key"  # 替换为用户的API密钥

# 填空生成功能定义

FILL_IN_THE_MIDDLE_FUNCTION={

    "name": "fill_in_the_middle",

    "description": "在给定文本的中间部分生成合适的内容",

    "parameters": {

        "type": "object",

        "properties": {

            "context_before": {

                "type": "string",

                "description": "填空前的文本上下文"

            },

            "context_after": {

                "type": "string",

                "description": "填空后的文本上下文"

            }

        },

        "required": ["context_before", "context_after"]

    }

}

在封装层里，TextEditingAssistant 在初始化时拉取余额并列出可用模型，同时维护对话历史。余额查询走 /get-user-balance，模型列表走 /list-models，均带 Bearer 鉴权。历史记录通过 add_to_history 与 get_history 增删查。生成填空时，create_completion 向 /create-completion 发送请求，指定 deepseek-chat、max_tokens、temperature，并把先前声明的函数一起带上以触发结构化调用。fill_in_the_middle 方法负责组装前后文参数并触发上述流程。

[PRE2]python

class TextEditingAssistant:

    def __init__(self):

        # 初始化对话历史

        self.conversation_history = []

        self.user_balance = self.get_user_balance()

        self.model_list = self.list_models()

        print("可用模型列表:", self.model_list)

    def get_user_balance(self) -> float:

        """

        获取用户余额

        """

        url = f"{API_BASE_URL}/get-user-balance"

        headers = {"Authorization": f"Bearer {API_KEY}"}

        try:

            response = requests.get(url, headers=headers)

            response.raise_for_status()

            balance = response.json().get("balance", 0)

            print(f"当前用户余额: {balance}")

            return balance

        except requests.exceptions.RequestException as e:

            print(f"获取用户余额失败: {e}")

            return 0.0

    def list_models(self) -> List[str]:

        """

        列出可用的模型

        """

        url = f"{API_BASE_URL}/list-models"

        headers = {"Authorization": f"Bearer {API_KEY}"}

        try:

            response = requests.get(url, headers=headers)

            response.raise_for_status()

            models = response.json().get("models", [])

            model_names = [model["name"] for model in models]

            return model_names

        except requests.exceptions.RequestException as e:

            print(f"获取模型列表失败: {e}")

            return []

    def add_to_history(self, role: str, content: str):

        """

        添加消息到对话历史

        """

        self.conversation_history.append({"role": role, "content": content})

    def get_history(self) -> List[Dict[str, str]]:

        """

        获取当前对话历史

        """

        return self.conversation_history

    def create_completion(self, function_name: str, parameters: Dict[str, str],

                          max_tokens: int = 150, temperature: float = 0.7) -> Dict:

        """

        调用DeepSeek API生成填空内容

        """

        url = f"{API_BASE_URL}/create-completion"

        headers = {

            "Authorization": f"Bearer {API_KEY}",

            "Content-Type": "application/json"

        }

        data = {

            "model": "deepseek-chat",

            "prompt": f"功能: {function_name}\n参数: {json.dumps(parameters, ensure_ascii=False)}\n请在上下文中生成适合的填空内容。",

            "max_tokens": max_tokens,

            "temperature": temperature,

            "functions": [FILL_IN_THE_MIDDLE_FUNCTION],

            "function_call": {"name": function_name}

        }

        try:

            response = requests.post(url, headers=headers, data=json.dumps(data))

            response.raise_for_status()

            return response.json()

        except requests.exceptions.RequestException as e:

            print(f"生成填空内容失败: {e}")

            return {}

    def fill_in_the_middle(self, context_before: str, context_after: str) -> str:

        """

        执行填空生成

        """

        parameters = {

            "context_before": context_before,