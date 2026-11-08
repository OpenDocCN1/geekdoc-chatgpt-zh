functions_list=[get_weather])

# 本地部署 Open WebUI 并直连 DeepSeek 实操笔记

想把 Open WebUI 跑在自己电脑上，再用官方 OpenAI SDK 直接调用 DeepSeek，整个流程其实很顺。下面把环境搭建、服务启动到代码调用的关键点整理清楚，照着步骤就能跑起来。

## 环境隔离先准备好

先用 Conda 单独建一个隔离空间，Python 锁定 3.11，避免依赖冲突。建好后做一次 Conda 初始化，刷新 shell 配置，再激活环境进入工作区。
[PRE0]bash

# 创建一个名为 open-webui 的 Conda 虚拟环境，并指定 Python 版本为 3.11

conda create -n open-webui python=3.11

# 初始化 Conda

conda init

# 重新加载 shell 配置

source ~/.bashrc

# 激活刚刚创建的 open-webui 环境，进入该环境

conda activate open-webui

## 安装 Open WebUI

环境激活后，通过清华源 pip 安装 open-webui，速度更稳定。
[PRE1]bash

pip install open-webui -i https://pypi.tuna.tsinghua.edu.cn/simple

## 启动服务

安装完成直接 serve 启动，服务起来后浏览器就能访问本地界面。
[PRE2]bash

open-webui serve

## 接入 DeepSeek API

后端调用不需要额外 SDK，pip 装 openai 即可。把 DeepSeek 的 API Key 填进去，base_url 指向 DeepSeek 官方地址，就能用标准 OpenAI 格式对话。
[PRE3]bash

pip install openai

下面是一段最小可用示例，先实例化客户端，再用 deepseek-chat 模型发起一次问候对话，并把返回内容和完整对象打印出来。
[PRE4]python

import os

from openai import OpenAI

from IPython.display import display, Code, Markdown

import requests

import json

# <YOUR_DEEPSEEK_API_KEY>替换为复制的 API key 字符串

ds_api_key = '<YOUR_DEEPSEEK_API_KEY>'

#实例化客户端

client = OpenAI(api_key=ds_api_key,

                base_url="https://api.deepseek.com")

#调用 DeepSeek-V3 模型

response = client.chat.completions.create(

    model="deepseek-chat",

    messages=[

        {"role": "user", "content": "你好，好久不见!"}

    ]

)

#输出生成的响应内容

print(response.choices[0].message.content)

#输出完整的响应

print(response)

真实返回片段大概是这样，先是人话回复，随后是完整的 ChatCompletion 结构，包含 usage、model、id 等元信息。
[PRE5]

你好！好久不见！最近过得怎么样？有什么想聊的吗？

ChatCompletion(id='1fdbc70b-acbf-4c9a-a895-d26bd69ad531',

              choices=[Choice(finish_reason='stop',

                              index=0, logprobs=None,

              system_message = {

                  message=ChatCompletionMessage(content='你好！好久不见！最近过得怎么样？有什么想聊的吗？',

                                               refusal=None,

                                               role='assistant',

                                               audio=None,

                                               function_call=None,

                                               tool_calls=None)],

              created=1736238514,

              model='deepseek-chat',

              object='chat.completion',

              service_tier=None,

              system_fingerprint='fp_3a5770e1b4',

              usage=CompletionUsage(completion_tokens=14,

                                    prompt_tokens=8,

                                    total_tokens=22,

                                    completion_tokens_details=None,

                                    prompt_tokens_details=None,

                                    prompt_cache_hit_tokens=0,

                                    prompt_cache_miss_tokens=8))

## 查看可用模型

用 client.models.list 可以列出当前账号可用的模型。
[PRE6]python

models_list = client.models.list()

print(models_list.data)

返回结果会包含 chat 和 coder 两个模型。
[PRE7]

[Model(id='deepseek-chat', created=None, object='model', owned_by='deepseek'),

 Model(id='deepseek-coder', created=None, object='model', owned_by='deepseek')]

## 消息格式示例

系统指令用于设定角色行为，格式如下：
[PRE8]

system_message = {

    "role": "system",

    "content": "你是一位擅长编写 python 代码的助手。"

}

普通用户消息结构：
[PRE9]

user_message = {

    "role": "user",

    "content": "你好，请介绍下你自己。"

}

如果要传图，可以把 text 和 image_url 拼成数组，image_url 里填可访问的链接：
[PRE10]

user_message_with_image = {

    "role": "user",

    "content": [

        {"type": "text", "text": "你能帮我介绍下这张图片里面的内容吗？"},

        {"type": "image_url",

         "image_url": {

             "url": "https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20241009205714055.png"}}

    ]

}

## 带 system 的完整调用

实际对话通常会先放 system 再放 user，示例中先创建 user_message，再把 system_message 和 user_message 一起传给模型，最后只打印模型生成的文本。
[PRE11]

#创建用户消息

user_message = {

    "role": "user",

    "content": "你好，请介绍下你自己。"

}

#调用 DeepSeek-V3 模型

response = client.chat.completions.create(

    model="deepseek-chat",

    messages=[

        system_message,

        user_message

    ]

)

#输出生成的响应内容

print(response.choices[0].message.content)

按这个链路，本地 WebUI 与 DeepSeek 的代码调用都能顺畅跑通，后续可以自由扩展工具、历史记录等功能。