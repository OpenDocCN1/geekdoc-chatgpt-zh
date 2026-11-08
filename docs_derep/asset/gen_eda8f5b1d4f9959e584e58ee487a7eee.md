# LoRA 轻量推荐，5 轮就能见效

想在资源有限的情况下快速搭一个可用的个性化推荐，LoRA 是很务实的选择。思路很简单：用二分类交叉熵来衡量匹配好坏，让模型在五轮迭代里逐步收敛。每轮会把用户侧和物品侧的输入先挤压维度再搬到当前设备，标签同步搬运，调用训练步骤更新参数并累计损失，轮末打印本轮平均损失。训练结束后先跑一次评估，再把参数以 state_dict 的形式写到 lora_recommendation_model.pth，并提示训练完成。入口函数在主守护下触发整个训练评估流程。

训练过程的实际输出如下：

[PRE0]text
Epoch 1, Loss: 0.6875

Epoch 2, Loss: 0.6000

Epoch 3, Loss: 0.5200

Epoch 4, Loss: 0.4400

Epoch 5, Loss: 0.3700

评估准确率: 0.6667

模型训练完成并已保存

环境准备很直接，依赖先装好：

[PRE1]python
pip install transformers torch datasets

基础导入如下：

[PRE2]python
import torch

from transformers import GPT2LMHeadModel, GPT2Tokenizer

from torch.utils.data import DataLoader

import random

推理侧基于 GPT-2，先加载模型和分词器并切到评估模式：

[PRE3]python
###### 加载 GPT-2 模型和 Tokenizer

model_name='gpt2'

tokenizer=GPT2Tokenizer.from_pretrained(model_name)

model=GPT2LMHeadModel.from_pretrained(model_name)

###### 设置模型为评估模式

model.eval()

提示词的组装考虑到上下文可选：

[PRE4]python
def generate_prompt(user_input, item_description, context_info=None):
    """
    根据用户输入和物品描述生成适合模型的提示词
    user_input: 用户的查询或行为
    item_description: 商品的描述
    context_info: 可选的上下文信息（例如用户历史数据）
    """
    # 如果有上下文信息，添加到提示词中
    if context_info:
        prompt=f"基于用户的历史行为 ({context_info}) 推荐；用户输入：{user_input}，物品描述：{item_description}。"
    else:
        prompt=f"用户输入：{user_input}，物品描述：{item_description}。"
    return prompt

生成推荐时用无梯度模式，控制多样性和长度：

[PRE5]python
def generate_recommendation(prompt):
    # 编码提示词
    inputs=tokenizer(prompt, return_tensors="pt")
    # 生成推荐内容
    with torch.no_grad():
        outputs=model.generate(
            inputs['input_ids'],
            max_length=100,
            num_return_sequences=1,
            no_repeat_ngram_size=2,
            temperature=0.7,
            top_p=0.9,
            top_k=50
        )
    # 解码并返回生成的推荐内容
    recommendation=tokenizer.decode(outputs[0], skip_special_tokens=True)
    return recommendation

对外暴露的推荐接口把两步串起来：

[PRE6]python
def recommend(user_input, item_description, context_info=None):
    # 生成适合的提示词
    prompt=generate_prompt(user_input, item_description, context_info)
    # 使用生成的提示词获取推荐结果
    recommendation=generate_recommendation(prompt)
    return recommendation

一个简单的使用示例：

[PRE7]python
###### 示例用户输入与物品描述

user_input="我想要找一本关于人工智能的书"

item_description="《人工智能基础》：介绍人工智能的基本概念和应用"

context_info="用户曾浏览过相关的科技书籍"

###### 获取推荐

recommendation=recommend(user_input, item_description, context_info)

print("推荐内容：", recommendation)

[PRE8]text