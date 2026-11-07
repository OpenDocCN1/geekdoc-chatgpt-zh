LangSmith 实战：追踪、提速、容错与批量评测全流程

想要把 LLM 应用从 demo 变成可用的生产系统，关键在于看得见、跑得快、扛得住、评得准。下面的几段代码演示了如何用 LangSmith 把这四件事串起来：先用低成本策略提速，再用主备模型保证可用性，接着用数据集做批量评估，最后把三者整合成一个可追踪的测试闭环。

### 提速型双步流水线
先让模型快速给出初稿，再用精简指令做压缩总结。把温度压低、输出长度封顶，整体响应会更稳定、更省时。函数返回初始回复和摘要两份结果，方便对比。

[PRE0]python

@traceable

def optimized_pipeline(user_input: str):

    # 优化步骤 1：设置较低的温度以减少生成时间

    step1 = client.chat.completions.create(

        model="gpt-3.5-turbo",

        temperature=0.3,  # 降低温度

        messages=[{"role": "user", "content": user_input}]

    )

    initial_response = step1.choices[0].message.content

    # 优化步骤 2：使用模型裁剪或压缩方法（例如减少 token 长度）来加速

    step2 = client.chat.completions.create(

        model="gpt-3.5-turbo",

        max_tokens=50,  # 限制生成的 token 数

        messages=[{"role": "user",

                    "content": f"总结以下内容：{initial_response}"}]

    )

    summary_response = step2.choices[0].message.content

    return {"initial_response": initial_response,

            "summary_response": summary_response}

###### 执行优化后的任务流

optimized_results = optimized_pipeline("什么是机器学习？")

print("优化任务执行结果:", optimized_results)

### 主备模型无缝回退
生产环境最怕单点故障。把 ChatOpenAI 设为主模型且不重试，ChatAnthropic 作为兜底，异常时自动切换并打印提示，整个调用仍被 @traceable 记录。

[PRE1]python

from langchain_openai import ChatOpenAI

from langchain_community.chat_models import ChatAnthropic

###### 设置主模型和备用模型

primary_model = ChatOpenAI(max_retries=0)

fallback_model = ChatAnthropic()

####### 定义支持回退的任务流

@traceable

def pipeline_with_fallback(user_input: str):

    try:

        response = primary_model.invoke({"role": "user",

                                         "content": user_input})

    except Exception as e:

        print("主模型失败，切换至备用模型")

        response = fallback_model.invoke({"role": "user",

                                          "content": user_input})

    return response

###### 执行任务并记录追踪

fallback_results = pipeline_with_fallback("描述人工智能的应用场景")

print("回退任务执行结果:", fallback_results)

### 批量评估与数据驱动
用 LangSmith 的 Client 创建评测集，写入输入后缀和期望输出。exact_match 作为最基础的精确匹配指标，evaluate 会跑完所有样例并输出实验结果，方便做版本对比。

[PRE2]python

from langsmith import Client

from langsmith.evaluation import evaluate

####### 初始化 LangSmith 客户端

client = Client()

###### 创建数据集，包含批量输入和预期输出

dataset_name = "机器学习批量评估"

dataset = client.create_dataset(dataset_name, description="LangSmith 批量任务评估示例")

client.create_examples(

    inputs=[{"postfix": "到 LangSmith"}, {"postfix": "到 LangSmith 评估"}],

    outputs=[{"output": "欢迎来到 LangSmith"},

             {"output": "欢迎来到 LangSmith 评估"}],

    dataset_id=dataset.id,

)

###### 定义批量评估方法

def exact_match(run, example):

    return {"score": run.outputs["output"] == example.outputs["output"]}

###### 运行批量评估

experiment_results = evaluate(

    lambda input: "欢迎" + input['postfix'],

    data=dataset_name,

    evaluators=[exact_match],

    experiment_prefix="批量任务评估",

    metadata={"version": "1.0.0", "revision_id": "beta"})

print("批量评估结果:", experiment_results)

### 一站式集成演示
把 OpenAI 客户端用 wrap_openai 包装以便追踪，配置主备模型链路，复用双步流程和回退流程，再加上批量评测。test_langsmith_integration 一次跑通追踪、容错、评估三环节。

[PRE3]python

import openai

from langsmith import traceable, wrap_openai, Client

from langsmith.evaluation import evaluate

from langchain_openai import ChatOpenAI

from langchain_community.chat_models import ChatAnthropic

from langchain_core.prompts import ChatPromptTemplate

from langchain_core.output_parsers import StrOutputParser

from openai import RateLimitError

from unittest.mock import patch

####### 包装 OpenAI 客户端以支持 LangSmith 追踪

client = wrap_openai(openai.Client())

####### 初始化 LangSmith 客户端

langsmith_client = Client()

####### 配置主模型和备用模型

primary_model = ChatOpenAI(max_retries=0)

fallback_model = ChatAnthropic()

model_with_fallback = primary_model.with_fallbacks([fallback_model])

####### 创建数据集用于批量评估

dataset_name = "机器学习批量评估"

dataset = langsmith_client.create_dataset(dataset_name,

                                          description="LangSmith 批量任务评估示例")

langsmith_client.create_examples(

    inputs=[{"postfix": "到 LangSmith"}, {"postfix": "到 LangSmith 评估"}],

    outputs=[{"output": "欢迎来到 LangSmith"},

             {"output": "欢迎来到 LangSmith 评估"}],

    dataset_id=dataset.id,

)

####### 定义多步骤任务流并支持 LangSmith 追踪

@traceable

def process_pipeline(user_input: str):

    # 步骤 1：生成初始回复

    step1 = client.chat.completions.create(

        model="gpt-3.5-turbo",

        messages=[{"role": "user", "content": user_input}]

    )

    initial_response = step1.choices[0].message.content

    # 步骤 2：对生成内容进行总结

    step2 = client.chat.completions.create(

        model="gpt-3.5-turbo",

        messages=[{"role": "user", "content": f"总结以下内容：{initial_response}"}]

    )

    summary_response = step2.choices[0].message.content

    return {"initial_response": initial_response,

            "summary_response": summary_response}

####### 定义支持回退的任务流

@traceable

def pipeline_with_fallback(user_input: str):

    try:

        response = primary_model.invoke({"role": "user", "content": user_input})

    except Exception as e:

        print("主模型失败，切换至备用模型")

        response = fallback_model.invoke({"role": "user", "content": user_input})

    return response

####### 批量评估方法

def exact_match(run, example):

    return {"score": run.outputs["output"] == example.outputs["output"]}

####### 测试函数，包含追踪、回退和批量评估

def test_langsmith_integration():

    # 执行追踪任务

    print("执行追踪任务:")

    results = process_pipeline("什么是机器学习？")

    print("追踪任务结果:", results)

    # 执行回退任务

    print("\n执行回退任务:")

    fallback_results = pipeline_with_fallback("描述人工智能的应用场景")

    print("回退任务结果:", fallback_results)

    # 执行批量评估

    print("\n执行批量评估:")

    experiment_results = evaluate(

        lambda input: "欢迎" + input['postfix'],

        data=dataset_name,

        evaluators=[exact_match],

        experiment_prefix="批量任务评估",

        metadata={"version": "1.0.0", "revision_id": "beta"}

    )

    print("批量评估结果:", experiment_results)

####### 运行测试函数

test_langsmith_integration()