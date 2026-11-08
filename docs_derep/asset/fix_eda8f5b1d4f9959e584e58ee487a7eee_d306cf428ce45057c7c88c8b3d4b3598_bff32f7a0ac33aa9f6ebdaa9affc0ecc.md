# LoRA 轻量调优，五轮可见趋势

在算力受限的场景里想快速落地个性化排序，LoRA 是一种性价比较高的微调思路。整体以二元交叉熵衡量匹配好坏，让参数在五次完整遍历中逐步收敛。

训练脚本先把损失函数设为二元交叉熵，随后开启五轮迭代。每轮遍历数据加载器，将使用者侧向量和条目侧向量各自分离维度后再搬到当前设备，标签同步搬运。调用封装好的训练步更新 LoRA 参数并累加当轮损失，轮末输出平均值。迭代结束后先执行一次评估，随后把参数以 state_dict 写进 lora_recommendation_model.pth 并提示收尾。主入口由 train_and_evaluate_lora_model 触发。

实际运行时可观察到损失单调下降，评估指标稳定在约 0.67 左右：
[PRE0]text
Epoch 1, Loss: 0.6875

Epoch 2, Loss: 0.6000

Epoch 3, Loss: 0.5200

Epoch 4, Loss: 0.4400

Epoch 5, Loss: 0.3700

评估准确率: 0.6667

模型训练完成并已保存

依赖很简洁，先执行安装命令：
[PRE1]python
pip install transformers torch datasets

基础导入如下：
[PRE2]python
import torch

from transformers import GPT2LMHeadModel, GPT2Tokenizer

from torch.utils.data import DataLoader

import random

推理部分采用 GPT-2，首先载入权重与分词器并切换为推理态：
[PRE3]python
###### 加载 GPT-2 模型和 Tokenizer

model_name='gpt2'

tokenizer=GPT2Tokenizer.from_pretrained(model_name)

model=GPT2LMHeadModel.from_pretrained(model_name)

###### 设置模型为评估模式

model.eval()

指令模板的组装支持补充信息，可透传使用者往绩：
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

产出建议时采用无梯度模式，控制长度与多样性：
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

对外接口把模板构建与文本生成串联：
[PRE6]python
def recommend(user_input, item_description, context_info=None):
    # 生成适合的提示词
    prompt=generate_prompt(user_input, item_description, context_info)
    # 使用生成的提示词获取推荐结果
    recommendation=generate_recommendation(prompt)
    return recommendation

最小使用示例：
[PRE7]python
###### 示例用户输入与物品描述

user_input="我想要找一本关于人工智能的书"

item_description="《人工智能基础》：介绍人工智能的基本概念和应用"

context_info="用户曾浏览过相关的科技书籍"

###### 获取推荐

recommendation=recommend(user_input, item_description, context_info)

print("推荐内容：", recommendation)

[PRE8]text