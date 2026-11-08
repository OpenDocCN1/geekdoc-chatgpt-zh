多轮对话：让模型真的“记得”你说了什么

生成式模型最基本的能力，是在海量文本的训练后学会理解并产出自然语言。光有单次回复是不够的，真正好用的对话需要跨回合的连贯。

什么是多轮对话
简单说，就是模型在连续的问答往返中保持语义上的连贯，并以前面轮次的语境为前提来生成新内容。不是每次都从零开始，而是带着上下文往下聊。

实现它需要哪些技术
核心是两类能力叠加：一是自然语言处理的基础能力，让模型读懂用户意图；二是上下文管理机制，让信息在整段交互里能够延续传递。只有两者配合，对话才不会断片。

系统是怎么“记住”之前的
做法很直接：把每一次的提问和回答都累加进上下文窗口。模型在生成当前回复时，看到的不是孤立的句子，而是从开头到现在的一段完整记录。窗口过长时，通常会做截断或保留最近几轮的策略，保证关键信息不丢失。

哪里会用到
最典型的场景是客服。用户往往不会一次性把问题说全，而是分几次补充细节。靠多轮对话，系统能把零散的描述拼成完整的问题画像，给出一次性的有效解答。

下面是两个可落地的实现思路，以及一个多步骤生成的示例输出。

[PRE0]python

# 导入所需的库

from transformers import AutoTokenizer, AutoModelForCausalLM, pipeline

import torch

# 设置设备

device="cuda" if torch.cuda.is_available() else "cpu"

# Step 1: 初始化模型和分词器

model_name="gpt2" # 使用GPT-2模型

tokenizer=AutoTokenizer.from_pretrained(model_name)

model=AutoModelForCausalLM.from_pretrained(model_name).to(device)

# Step 2: 定义对话生成函数

def generate_response(model, tokenizer, input_text, max_length=100):

    """

    生成对话回复

    model: 生成模型

    tokenizer: 分词器

    input_text: 当前输入文本

    max_length: 最大生成长度

    """

    inputs=tokenizer.encode(input_text, return_tensors="pt").to(device)

    outputs=model.generate(inputs, max_length=max_length, pad_token_id=tokenizer.eos_token_id)

    response=tokenizer.decode(outputs[:, inputs.shape[-1]:][0], skip_special_tokens=True)

    return response

# Step 3: 初始化上下文管理函数-提取关键内容

def manage_context(context, max_tokens=300):

    """

    对长对话进行上下文管理，保留关键内容

    context: 当前上下文内容

    max_tokens: 上下文的最大长度

    """

    tokens=tokenizer.encode(context)

    if len(tokens) > max_tokens:

        # 如果上下文过长，使用总结策略

        context_segments=context.split("\n")

        context="\n".join(context_segments[-5:])

    # 保留最近的5条对话记录

    return context

# Step 4: 初始查询和上下文

initial_query="生成模型如何在长对话中保持上下文记忆？"

context=f"用户: {initial_query}\n模型: "

# Step 5: 开始多轮长对话

num_rounds=8 # 设置对话轮数

for round_num in range(1, num_rounds+1):

    print(f"对话轮次 {round_num}: ")

    # 管理上下文,提取并更新关键内容

    context=manage_context(context)

    # 生成模型的回复

    response=generate_response(model, tokenizer, context)

    print("模型回复: ", response)

    # 更新上下文,加入当前模型生成的回复

    context += f"{response}\n用户: "

    # 模拟用户的输入(示例用户输入)

    user_input=f"请解释在第{round_num}轮对话中生成模型如何记住内容。"

    print("用户输入: ", user_input)

    # 将用户的输入添加到上下文

    context += f"{user_input}\n模型: "

print("\n最终的对话上下文: \n", context)
[PRE1]python

# 导入所需的库

from transformers import AutoTokenizer, AutoModelForCausalLM, pipeline

import torch

# 设置设备

device="cuda" if torch.cuda.is_available() else "cpu"

# Step 1: 初始化模型和分词器

model_name="gpt2"  # 使用GPT-2模型

tokenizer=AutoTokenizer.from_pretrained(model_name)

model=AutoModelForCausalLM.from_pretrained(model_name).to(device)

# Step 2: 定义生成函数，用于多步骤生成任务

def generate_step_response(model, tokenizer, input_text, max_length=100):

    """

    生成步骤任务的回复

    model: 生成模型

    tokenizer: 分词器

    input_text: 当前输入文本

    max_length: 最大生成长度

    """

    inputs=tokenizer.encode(input_text, return_tensors="pt").to(device)

    outputs=model.generate(inputs, max_length=max_length, pad_token_id=tokenizer.eos_token_id)

    response=tokenizer.decode(outputs[:, inputs.shape[-1]:][0], skip_special_tokens=True)

    return response

# Step 3: 定义多步骤生成函数，构建复杂任务的分解流程

def multi_step_generation(initial_task, steps):

    """

    多步骤生成函数，逐步生成复杂任务

    initial_task: 初始任务描述

    steps: 任务的分步骤描述列表

    """

    # 初始化上下文

    context=f"任务: {initial_task}\n"

    print("初始任务描述: ", initial_task)

    # 逐步生成任务内容

    for i, step in enumerate(steps, start=1):

        print(f"\n步骤 {i}: {step}")

        # 在上下文中加入当前步骤描述

        context += f"\n步骤 {i}: {step}\n生成内容: "

        # 生成当前步骤内容

        response=generate_step_response(model, tokenizer, context)

        print("生成内容: ", response)

        # 更新上下文，加入生成的内容

        context += response

    print("\n最终任务完成内容: \n", context)

# Step 4: 定义初始任务和分解步骤

initial_task="编写一份详细的项目提案"

steps=[

    "步骤 1: 介绍项目背景及目标",

    "步骤 2: 详细说明项目的技术实现方法",

    "步骤 3: 分析项目的预期成果和效果",

    "步骤 4: 列出项目的时间进度安排和预算",

    "步骤 5: 总结项目的关键点和创新之处"

]

# 执行多步骤生成

multi_step_generation(initial_task, steps)
[PRE2]text

>> 任务：编写一份详细的项目提案

>>

>> 步骤 1：介绍项目背景及目标

>> 生成内容： 本项目旨在解决当前市场需求，为用户提供创新性的解决方案。通过引入先进的技术手段，实现高效、安全的服务。

>>

>> 步骤 2：详细说明项目的技术实现方法

>> 生成内容： 本项目将利用大数据分析和AI技术，以优化用户体验并提升系统性能。主要技术包括数据挖掘、机器学习模型训练及部署等。

>>

>> 步骤 3：分析项目的预期成果和效果

>> 生成内容： 通过该项目的实施，预期可显著提升用户满意度，降低成本，提高市场竞争力。预计用户转换率将显著提高。

>>

>> 步骤 4：列出项目的时间进度安排和预算

>> 生成内容： 项目周期预计为六个月，分为需求分析、开发、测试和部署四个阶段。预算包括人力成本、设备采购、云服务开支等。

>>

>> 步骤 5：总结项目的关键点和创新之处

>> 生成内容： 本项目的关键创新在于其采用了先进的AI算法，提升了市场响应速度，并结合用户需求量身定制解决方案，具有较高的创新性和实用性。