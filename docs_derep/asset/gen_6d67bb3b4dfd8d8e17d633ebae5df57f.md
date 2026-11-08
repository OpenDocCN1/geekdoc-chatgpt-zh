# Open WebUI 本地部署与 DeepSeek API 调用实战

想把 Open WebUI 快速跑在本地并接入 DeepSeek，整体流程可以拆成环境搭建、服务启动和 Python 调用的三步。下面是经过验证的最小可运行路径，直接照抄即可。

**第一步：用 Conda 建一个隔离的 Python 3.11 环境**
先建好环境并激活，避免依赖冲突。下面这段会创建环境 open-webui、初始化 Conda 并进入环境。
[PRE0]bash

# 创建一个名为 open-webui 的 Conda 虚拟环境，并指定 Python 版本为 3.11

conda create -n open-webui python=3.11

# 初始化 Conda

conda init

# 重新加载 shell 配置

source ~/.bashrc

# 激活刚刚创建的 open-webui 环境，进入该环境

conda activate open-webui

环境激活后就可以安装 Open WebUI 了。为了提速，使用清华源安装。
[PRE1]bash

pip install open-webui -i https://pypi.tuna.tsinghua.edu.cn/simple

安装完成直接启动服务，默认会在本地起一个 Web 界面。
[PRE2]bash

open-webui serve

**第二步：准备 OpenAI 兼容的 Python SDK**
Open WebUI 之外，我们还演示如何用官方 SDK 直接调用 DeepSeek。首先装包。
[PRE3]bash

pip install openai

下面这段代码展示了如何用 API Key 初始化客户端、调用 deepseek-chat 模型并打印结果。记得把占位符换成自己的 Key。
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

一次简单的问候调用，返回效果如下：
[PRE5]

你好！好久不见！最近过得怎么样？有什么想聊的吗？

ChatCompletion(id='1fdbc70b-acbf-4c9a-a895-d26bd69ad531',

              choices=[Choice(finish_reason='stop',

                              index=0, logprobs=None,

                              message=ChatCompletionMessage(content='你好！好久不见！最近过得怎么样？有什么想聊的吗？',

                                                            refusal=None,

                                                            role='assistant',

                                                            audio=None,

                                                            function_call=None,

                                                            tool_calls=None))],

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

**第三步：常用扩展用法**
查询当前账号可用的模型列表，方便确认模型名是否正确。
[PRE6]python

models_list = client.models.list()

print(models_list.data)

返回的模型信息示例：
[PRE7]

[Model(id='deepseek-chat', created=None, object='model', owned_by='deepseek'),

 Model(id='deepseek-coder', created=None, object='model', owned_by='deepseek')]

实际使用中常会先设定系统角色，再发用户提问。下面是系统消息的写法。
[PRE8]python

system_message = {

    "role": "system",

    "content": "你是一位擅长编写 python 代码的助手。"

}

对应的普通用户消息示例：
[PRE9]python

user_message = {

    "role": "user",

    "content": "你好，请介绍下你自己。"

}

如果需要做多模态输入，可以把文本和图片 url 一起传给模型。
[PRE10]python

user_message_with_image = {

    "role": "user",

    "content": [

        {"type": "text", "text": "你能帮我介绍下这张图片里面的内容吗？"},

        {"type": "image_url",

         "image_url": {

             "url": "https://ml2022.oss-cn-hangzhou.aliyuncs.com/img/image-20241009205714055.png"}}

    ]

}

把 system 和 user 消息组合后调用，完整调用示例如下：
[PRE11]python

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

按照以上步骤，先用 Conda 隔离环境、启动 Open WebUI，再用 OpenAI SDK 以兼容方式接入 DeepSeek，就能在本地完成从界面到代码的完整验证。