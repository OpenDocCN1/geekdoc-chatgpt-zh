LangChain 快速搭建会记忆的多语言对话机器人

想让大模型真的“记得”你是谁、你之前聊了什么，LangChain 提供了一套从无状态调用到带会话记忆、再到消息裁剪的完整工具链。下面通过一段可直接运行的流程，演示从基础交互到长对话管理的演进。

先搭一个最简的聊天链路，指定本地模型地址和参数，然后用 StrOutputParser 把输出整理成纯文本。单条提问时模型没有上下文，每次都是全新开始。

[PRE0]

python

//Chapter9/Chatbot.ipynb

from langchain_openai import ChatOpenAI

model = ChatOpenAI(

    streaming=False,

    openai_api_key="EMPTY",

    openai_api_base='http://172.21.131.185:8000/v1',

    model_name='Your_model_path/Meta- Llama- 3.1- 8B- Instruct',

    temperature=0.5,

    max_tokens=300

)

from langchain_core.messages import HumanMessage

from langchain_core.output_parsers import StrOutputParser

parser = StrOutputParser()

chain = model | parser

chain.invoke([HumanMessage(content="你好,我是小明。")])

如果这时单独追问名字，因为没有历史，模型无法给出正确回应。

[PRE1]

python

chain.invoke([HumanMessage(content="你知道我叫什么名字吗?")])

要让模型记住前情，需要把历史消息一起传进去。把 HumanMessage 和 AIMessage 按顺序组成列表，直接喂给 model.invoke，就能在一次调用里看到上下文效果。

[PRE2]

python

from langchain_core.messages import AIMessage

messages=[

    HumanMessage(content="你好,我是小明。"),

    AIMessage(content="你好，小明!很高兴认识你! 从哪里来?"),

    HumanMessage(content="你知道我叫什么名字吗?")

]

model.invoke(messages)

上面做法每次都要自己管理历史，实际对话里更优雅的方案是交给会话历史组件。先安装社区包。

[PRE3]

pip install langchain-community

用 InMemoryChatMessageHistory 做内存存储，配合 RunnableWithMessageHistory 按 session_id 隔离不同用户的会话。get_session_history 负责按需创建并返回对应历史对象。

[PRE4]

from langchain_core.chat_history import (

    BaseChatMessageHistory,

    InMemoryChatMessageHistory,

)

from langchain_core.runnables.history import RunnableWithMessageHistory

store = {}

def get_session_history(session_id: str) -> BaseChatMessageHistory:

    if session_id not in store:

        store[session_id] = InMemoryChatMessageHistory()

    return store[session_id]

with_message_history = RunnableWithMessageHistory(model, get_session_history)

同一 session_id 下第一次打招呼，模型会回复并把对话写入历史。

[PRE5]

config = {"configurable": {"session_id": "123"}}

response = with_message_history.invoke(

    [HumanMessage(content="你好，我是小明。")],

    config=config,

)

response.content

[PRE6]

'你好，小明!你今天过得好吗？'

紧接着在同一个 session_id 里提问名字，历史被自动带入，模型能够回溯之前的自我介绍。

[PRE7]

response = with_message_history.invoke(

    [HumanMessage(content="你知道我叫什么名字吗?")],

    config=config,

)

response.content

[PRE8]

'当然了!你刚才告诉我的，名字是小明!'

接下来让对话具备多语言能力。用 ChatPromptTemplate 设定系统角色，并通过 MessagesPlaceholder 占位历史消息，language 作为可变的模板参数。

[PRE9]python

from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

prompt = ChatPromptTemplate.from_messages(

    [

        (

            "system",

            "你是一个会多种语言的聊天机器人，请用{language}和我对话。",

        ),

        MessagesPlaceholder(variable_name="messages"),

    ]

)

chain = prompt | model

传入 messages 和 language，就能得到指定语言的回复。

[PRE10]python

response = chain.invoke({"messages": [HumanMessage(content="你好，我是小明。")], "language":"英语"})

response.content

把带 Prompt 的链路再包一层 RunnableWithMessageHistory，并指定 input_messages_key 为 messages，就能在不同会话里保持多语言记忆。

[PRE11]python

with_message_history = RunnableWithMessageHistory(

    chain,

    get_session_history,

    input_messages_key="messages"

)

config3 = {"configurable": {"session_id": "456"}}

response = with_message_history.invoke(

    {"messages": [HumanMessage(content="你好，我是小红。")], "language": "英语"},

    config=config3,

)

response.content

同一 session 再问名字，模型仍然记得之前用英语自我介绍的用户。

[PRE12]python

response = with_message_history.invoke(

    {"messages": [HumanMessage(content="你知道我叫什么名字吗?")], "language": "英语"},

    config=config3,

)

response.content

长对话会产生 Token 超限问题。自定义 token_counter 基于 tokenizer 统计各消息的 Token 数，并用 trim_messages 按 last 策略保留最新内容，同时保留系统提示。

[PRE13]python

from langchain_core.messages import SystemMessage, trim_messages

from transformers import AutoTokenizer

from typing import List

from langchain_core.messages import BaseMessage, ToolMessage

tokenizer = AutoTokenizer.from_pretrained("Your_model_path/Yi-1.5-34B-Chat")

def str_token_counter(text: str) -> int:

    return len(tokenizer.encode(text))

def token_counter(messages: List[BaseMessage]) -> int:

    num_tokens = 3  # 每个回复都带有 <|start|>assistant<|message|>

    tokens_per_message = 3

    tokens_per_name = 1

    for msg in messages:

        if isinstance(msg, HumanMessage):

            role = "user"

        elif isinstance(msg, AIMessage):

            role = "assistant"

        elif isinstance(msg, ToolMessage):

            role = "tool"

        elif isinstance(msg, SystemMessage):

            role = "system"

        else:

            raise ValueError(f"Unsupported messages type {msg.__class__}")

        num_tokens += (

            tokens_per_message

            + str_token_counter(role)

            + str_token_counter(msg.content)

        )

        if msg.name:

            num_tokens += tokens_per_name + str_token_counter(msg.name)

    return num_tokens

trimmer = trim_messages(

    max_tokens=50,

    strategy="last",

    token_counter=token_counter,

    include_system=True,

    allow_partial=False,

    start_on="human",

)

messages = [

    SystemMessage(content="你是一个会多种语言的聊天机器人，请用{language}和我对话。"),

    HumanMessage(content="你好，我是小明。"),

    AIMessage(content="你好，小明。"),

    HumanMessage(content="今天天气不错。"),

    AIMessage(content="是啊，你今天有什么计划吗?"),

    HumanMessage(content="我想去公园散步。"),

    AIMessage(content="真是一个好主意!有什么我可以帮助你的吗?"),

    HumanMessage(content="我想知道西安有什么适合散步的公园，你知道吗?"),

    AIMessage(content="当然，可以去劳动公园或者七夕公园。"),

    HumanMessage(content="好的，那我今天去劳动公园散步。"),

    AIMessage(content="好的，祝你玩得愉快。"),

]

trimmer.invoke(messages)

裁剪后只剩下系统提示和最后两轮对话，控制在 Token 预算内。

[PRE14]python

[SystemMessage(content='你是一个会多种语言的聊天机器人，请用{language}和我对话。'),

HumanMessage(content='好的，那我今天去劳动公园散步。'),

AIMessage(content='好的，祝你玩得愉快。')]

把 trimmer 嵌入链路，使用 RunnablePassthrough.assign 先对 messages 做裁剪，再进入 Prompt 和模型。

[PRE15]python

from operator import itemgetter

from langchain_core.runnables import RunnablePassthrough

chain = (

    RunnablePassthrough.assign(messages=itemgetter("messages") | trimmer)

    | prompt

    | model

)

response = chain.invoke(

    {

        "messages": messages + [HumanMessage(content="我叫什么名字?")],

        "language": "中文",

    }

)

response.content

最后把裁剪后的链路再接入会话历史，实现带记忆、带裁剪的多语言对话。不同 session_id 可以独立运行。

[PRE16]python

with_message_history = RunnableWithMessageHistory(

    chain,

    get_session_history,

    input_messages_key="messages",

)

config = {"configurable": {"session_id": "789"}}

[PRE17]python

response = with_message_history.invoke(

    {

        "messages": messages + [HumanMessage(content="我叫什么名字?")],

        "language": "中文",

    },

    config=config3

)

response.content

[PRE18]python

response = with_message_history.invoke(
    {