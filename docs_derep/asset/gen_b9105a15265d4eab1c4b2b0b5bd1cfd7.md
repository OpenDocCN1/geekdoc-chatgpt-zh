Python 调用大模型：三种常用写法快速上手

想在本地脚本里直接和大模型对话，Python 已经把门槛压得很低。下面用三个最常见的场景演示如何发起请求，从原生 SDK 到国产模型，再到 LangChain 封装，一看就会。

先说最直接的 OpenAI 原生调用。把 API Key 放在环境变量里，通过 OpenAI 客户端创建对话，system 消息设定角色，user 消息传入问题，最后打印返回内容。适合想完全掌控请求参数的开发者。

[PRE0]python

import os

from openai import OpenAI

# 在.env文件中填写自己的APIKey

client = OpenAI(api_key=os.getenv("OPENAI_API_KEY"))

completion = client.chat.completions.create(

    model=os.getenv("model"),

    messages=[

        {"role": "system", "content": "You are a helpful assistant."},

        {"role": "user", "content": "Hello!"}

    ]

)

print(completion.choices[0].message.content)

再看国产模型的使用，智谱 GLM 的写法逻辑几乎一样。只换包名和 Key，model 固定为 glm-4-plus，messages 里用中文系统提示，问一个经典逻辑题即可得到回答。

[PRE1]python

import os

from zhipuai import ZhipuAI

client = ZhipuAI(api_key=os.getenv("GLM_API_KEY"))

response = client.chat.completions.create(

    model="glm-4-plus",

    messages=[

        {"role": "system", "content": "你是一个乐于解答各种问题的助手。"},

        {"role": "user", "content": "农夫如何带狼、羊和白菜过河？"}

    ],

)

print(response.choices[0].message)

如果项目里已经在用 LangChain，想要更统一的接口管理，可以用 ChatOpenAI 封装。实例化 LLM 时传入 Key 和模型名，messages 用元组形式传递，invoke 后直接取 content。官方文档里有完整集成说明，适合构建多步骤工作流。

[PRE2]python

import os

from langchain_openai import ChatOpenAI

# LangChain Docs:https://python.langchain.com/docs/integrations/chat/openai/

llm = ChatOpenAI(

    api_key=os.getenv("OPENAI_API_KEY"),

    model="gpt-4o",

)

messages = [

    ("system", "You are a helpful assistant that translates English to Chinese. Translate the user sentence."),

    ("human", "I love programming."),

]

ai_msg = llm.invoke(messages)

print(ai_msg.content)

三种方式各有侧重：原生调用最透明，换模型最灵活；直接接入 GLM 更符合中文场景；LangChain 则方便做链式调用和工具集成。选对一套，先跑通最小示例，再逐步扩展。