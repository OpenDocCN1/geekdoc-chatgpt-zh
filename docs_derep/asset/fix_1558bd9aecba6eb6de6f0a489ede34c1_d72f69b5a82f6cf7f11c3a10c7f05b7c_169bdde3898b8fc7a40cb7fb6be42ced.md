# LangChain 可观测编排实战

把复杂问答拆成可追踪的小段，并把用量、耗时、日志和评估都接起来，是 LLM 工程化的常见做法。下面用中文案例演示四种模式：基础调用、串行编排、文件日志、Argilla 评估。

先跑最小例子确认环境可用，观察基础用量和费用。

[PRE0]text

>> 任务链最终输出：中国的首都是北京。

>> Tokens 消耗：15

>> 花费：$0.002

打开链路追踪，为后续多段编排准备。

[PRE1]python

import os

os.environ["LANGCHAIN_TRACING"]="true"

设定两段 prompt，构成 SimpleSequentialChain：先做背景概览，再在已有背景上补充关键事件。配合 get_openai_callback 采集耗时、token 用量和费用。

[PRE2]python

from langchain.callbacks import get_openai_callback

from langchain.prompts import PromptTemplate

from langchain.llms import OpenAI

from langchain.chains import LLMChain, SimpleSequentialChain

##### 定义第一个提示词模板

template1="简要介绍{topic}的背景。"

prompt1=PromptTemplate(template=template1, input_variables=["topic"])

##### 定义第二个提示词模板

template2="{background}。请进一步描述{topic}的关键事件。"

prompt2=PromptTemplate(template=template2,

input_variables=["background", "topic"])

llm=OpenAI(temperature=0) # 初始化 OpenAI 模型

chain1=LLMChain(llm=llm, prompt=prompt1) # 创建第一个链，用于背景介绍

chain2=LLMChain(llm=llm, prompt=prompt2) # 创建第二个链，用于描述关键事件

##### 将两个链串联成一个多步任务链

sequential_chain=SimpleSequentialChain(chains=[chain1, chain2])

##### 启用实时监控和数据分析

with get_openai_callback() as cb:

    # 执行任务链，输入一个中文话题

    response=sequential_chain.run({"topic"："秦始皇的统一过程"})

    print("任务链最终输出:", response)

###### 输出实时监控数据

print("任务链消耗的总 Tokens:", cb.total_tokens)

print("任务链的总花费:", cb.total_cost)

print("任务链的各步骤时间:", cb.durations)

print("任务链的每步 Tokens 消耗:", cb.token_usages)

在秦始皇统一议题上的返回与统计。

[PRE3]text

>> 任务链最终输出：秦始皇统一六国的过程始于...最终成功建立了中国历史上第一个封建帝国。

>> 任务链消耗的总 Tokens: 243

>> 任务链的总花费：$80.012

>> 任务链的各步骤时间:

>>- 第一步：背景介绍消耗时间 0.78 秒

>>- 第二步：关键事件描述消耗时间 1.05 秒

>> 任务链的每步 Tokens 消耗:

>>- 第一步 Tokens 消耗：120

>>- 第二步 Tokens 消耗：123

若要把交互落盘，可借助文件回调。先装包。

[PRE4]bash

pip install langchain loguru

用 loguru 整理日志文件，并搭一个 FileCallbackHandler 接入链路。verbose 模式下能实时看到交互。

[PRE5]python

from loguru import logger

from langchain.callbacks import FileCallbackHandler

from langchain.prompts import PromptTemplate

from langchain.llms import OpenAI

from langchain.chains import LLMChain

import os

logfile="output.log"  #指定日志文件路径

logger.add(logfile, colorize=True, enqueue=True)   #使用 loguru 配置日志文件

#创建 FileCallbackHandler

file_callback_handler=FileCallbackHandler(logfile)

prompt 与链路搭建。

[PRE6]python

#定义提示词模板

template="简述以下事件:{event}"

prompt=PromptTemplate(template=template, input_variables=["event"])

llm=OpenAI(temperature=0)   #初始化语言模型

#创建包含文件回调的链

chain=LLMChain(llm=llm, prompt=prompt, callbacks=[file_callback_handler], verbose=True)

对中文事件进行一次调用。

[PRE7]python

#执行任务链，输入一个中文事件描述

response=chain.run({"event":"中国改革开放的历程"})

print("链的最终输出:", response)

要做 token 细分统计，先安装基础包。

[PRE8]bash

pip install langchain openai

搭一个单段链。

[PRE9]python

from langchain.callbacks import get_openai_callback

from langchain.prompts import PromptTemplate

from langchain.llms import OpenAI

from langchain.chains import LLMChain

##### 定义提示词模板

template = "请简述以下历史事件:{event}"

prompt = PromptTemplate(template=template, input_variables=["event"])

llm = OpenAI(temperature=0)  # 初始化语言模型

chain = LLMChain(llm=llm, prompt=prompt)  # 创建任务链

用 callback 包裹跑通，打印总 token、prompt token、completion token 与费用。

[PRE10]python

##### 使用 Token 计数器监控 Token 消耗

with get_openai_callback() as cb:

    # 执行任务链，输入中文问题

    response = chain.run({"event": "丝绸之路的历史发展"})

    print("链的最终输出:", response)

###### 输出 Token 计数器数据

print("总 Token 消耗:", cb.total_tokens)

print("Prompt Tokens:", cb.prompt_tokens)

print("Completion Tokens:", cb.completion_tokens)

print("总花费:", cb.total_cost)

丝绸之路例子的统计结果。

[PRE11]text

>> 链的最终输出：丝绸之路是古代中国与西方世界之间的重要贸易路线，起始于西汉，连接亚欧大陆，促进了文化交流和经济发展。

>> 总 Token 消耗:124

>> Prompt Tokens:32

>> Completion Tokens:92

>> 总花费:$0.005

最后把结果接入人工评估。先安装 Argilla 并配置密钥。

[PRE12]bash

pip install argilla

[PRE13]python

import os

os.environ["ARGILLA_API_KEY"] = "你的_API_密钥"

创建 Argilla 回调并组装链。

[PRE14]python

from argilla import ArgillaCallbackHandler

from langchain.llms import OpenAI

from langchain.prompts import PromptTemplate

from langchain.chains import LLMChain

##### 初始化 Argilla 回调处理程序

argilla_callback_handler = ArgillaCallbackHandler()

##### 定义提示词模板

template = "描述以下历史事件：{event}"

prompt = PromptTemplate(template=template, input_variables=["event"])

##### 初始化语言模型

llm = OpenAI(temperature=0)

##### 创建任务链，包含 Argilla 回调处理程序

chain = LLMChain(

    llm=llm,

    prompt=prompt,

    callbacks=[argilla_callback_handler]

)

喂入测试用例。

[PRE15]python

##### 输入中文测试用例

response = chain.run({"event": "秦始皇统一六国的过程"})

print("任务链输出:", response)

Argilla 回调后的返回示例。

[PRE16]text

>> 任务链输出：秦始皇通过一系列军事行动和政治手段，于公元前 221 年统一六国，建立了中国历史上第一个中央集权的封建国家。