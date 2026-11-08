先从一个最常见的天气问答做个开场，看看模型会怎么回应。

用户开口问：“明天会下雨吗？” 模型结合预报回复说，明天降雨的可能性不小，很可能出现小阵雨，提醒出门最好随身带伞。

用户追问：“那我到底要不要带伞呢？” 模型确认，由于明天天公不作美、变化偏大，提前备伞会更稳妥。

用户又好奇下一周的情况：“下周怎么样？” 模型表示，时间拉得更长目前还缺乏明确依据，建议持续关注最新的气象更新。

下面进入实操部分，演示如何用 GPT-2 做情绪可控生成、强度调节、基础生成以及批量加速。

[PRE0]

from transformers import AutoTokenizer, AutoModelForCausalLM

import torch

# 加载预训练模型和分词器

model_name = "gpt2"

tokenizer = AutoTokenizer.from_pretrained(model_name)

model = AutoModelForCausalLM.from_pretrained(model_name)

# 定义情绪提示函数

def generate_emotion_text(prompt, emotion, max_length=100,

                          top_p=0.9, top_k=50):

    # 添加情绪提示，控制生成的情绪表达

    emotion_prompt = f"<{emotion}> {prompt}"  # 将情绪标签嵌入输入

    input_ids = tokenizer(emotion_prompt, return_tensors="pt").input_ids

    # 使用Top-P和Top-K采样生成具有特定情绪的文本

    output = model.generate(

        input_ids,

        max_length=max_length,  # 设置生成文本的最大长度

        top_p=top_p,  # 设置Top-P值控制多样性

        top_k=top_k,  # 设置Top-K值控制稀疏性

        do_sample=True,  # 开启采样

    )

    # 解码生成的文本

    generated_text = tokenizer.decode(output[0], skip_special_tokens=True)

    return generated_text

# 示例：生成带有正面情绪的文本

prompt = "今天的天气真好，"

emotion = "positive"  # 指定情绪为正面

positive_text = generate_emotion_text(prompt, emotion)

print("生成的正面情绪文本:", positive_text)

# 示例：生成带有负面情绪的文本

emotion = "negative"  # 指定情绪为负面

negative_text = generate_emotion_text(prompt, emotion)

print("生成的负面情绪文本:", negative_text)

[PRE1]

def generate_emotion_text_with_intensity(prompt, emotion, intensity=1,

                                         max_length=100, top_p=0.9, top_k=50, temperature=0.8):

    # 根据情绪强度重复添加情绪标签

    emotion_prompt = f"{'<' + emotion + '>' * intensity} {prompt}"

    input_ids = tokenizer(emotion_prompt, return_tensors="pt").input_ids

    # 设置生成参数并调节温度

    output = model.generate(

        input_ids,

        max_length=max_length,

        top_p=top_p,

        top_k=top_k,

        temperature=temperature,  # 控制情绪强度

        do_sample=True, )

    # 解码生成的文本

    generated_text = tokenizer.decode(output[0], skip_special_tokens=True)

    return generated_text

# 示例：生成带有强烈正面情绪的文本

prompt = "今天的天气真好，"

emotion = "positive"

positive_text_strong = generate_emotion_text_with_intensity(

                                        prompt, emotion, intensity=3, temperature=1.0)

print("强烈的正面情绪文本:", positive_text_strong)

# 示例：生成带有强烈负面情绪的文本

emotion = "negative"

negative_text_strong = generate_emotion_text_with_intensity(

                                        prompt, emotion, intensity=3, temperature=1.0)

print("强烈的负面情绪文本:", negative_text_strong)

[PRE2]

from transformers import AutoTokenizer, AutoModelForCausalLM

import torch

# 加载GPT-2模型和分词器

model_name = "gpt2"

tokenizer = AutoTokenizer.from_pretrained(model_name)

model = AutoModelForCausalLM.from_pretrained(model_name)

# 定义生成函数

def generate_text(prompt, max_length=100, top_k=50, top_p=0.9,

                  temperature=0.7, repetition_penalty=1.1):

    """

    使用GPT-2生成文本，设置生成参数以控制生成的内容多样性和质量。

    参数:

        prompt (str): 输入文本

        max_length (int): 生成文本的最大长度

        top_k (int): Top-K采样的K值

        top_p (float): Top-P采样的阈值

        temperature (float): 控制生成随机性

        repetition_penalty (float): 重复惩罚，减少重复词生成

    返回:

        str: 生成的文本

    """

    # 编码输入文本

    input_ids = tokenizer(prompt, return_tensors="pt").input_ids

    # 生成文本

    output = model.generate(

        input_ids=input_ids,

        max_length=max_length,

        top_k=top_k,

        top_p=top_p,

        temperature=temperature,

        repetition_penalty=repetition_penalty,

        do_sample=True,

        eos_token_id=tokenizer.eos_token_id, )

    # 解码生成的文本

    generated_text = tokenizer.decode(output[0], skip_special_tokens=True)

    return generated_text

# 输入提示文本

prompt = "在人工智能的未来发展中，"

generated_text = generate_text(prompt)

print("生成的文本:", generated_text)

[PRE3]python

from transformers import AutoTokenizer, AutoModelForCausalLM

import torch

# 加载预训练的GPT-2模型和分词器

model_name = "gpt2"

tokenizer = AutoTokenizer.from_pretrained(model_name)

model = AutoModelForCausalLM.from_pretrained(model_name)

device = "cuda" if torch.cuda.is_available() else "cpu"

model.to(device)

# 定义批量生成函数

def batch_generate_texts(prompts, max_length=100, top_k=50, top_p=0.9,

                        temperature=0.7, batch_size=2):

    """

    批量生成文本，通过将多个输入同时推理，实现并行化加速

    参数:

        prompts (list): 输入文本列表

        max_length (int): 生成文本的最大长度

        top_k (int): Top-K采样的K值

        top_p (float): Top-P采样的阈值

        temperature (float): 控制生成的随机性

        batch_size (int): 每批次的处理数量

    返回:

        list: 生成的文本列表

    """

    generated_texts = []

    # 按照batch_size分割prompts

    for i in range(0, len(prompts), batch_size):

        batch_prompts = prompts[i:i+batch_size]

        # 将批量的输入文本编码为张量

        input_ids = tokenizer(batch_prompts, return_tensors="pt",

                            padding=True, truncation=True).input_ids.to(device)

        # 并行生成文本

        output = model.generate(

            input_ids=input_ids,

            max_length=max_length,

            top_k=top_k,

            top_p=top_p,

            temperature=temperature,

            do_sample=True,

            eos_token_id=tokenizer.eos_token_id,

        )

        # 解码生成的文本

        batch_generated_texts = [tokenizer.decode(text,

                                skip_special_tokens=True) for text in output]

        generated_texts.extend(batch_generated_texts)

    return generated_texts

# 输入提示列表

prompts = ["未来的人工智能技术将会如何发展？", "机器学习在医疗行业的应用", "智能城市如何提高生活质量？"]

# 批量生成结果

generated_texts = batch_generate_texts(prompts, batch_size=2)

# 输出生成结果

for prompt, generated_text in zip(prompts, generated_texts):

    print(f"提示: {prompt}")

    print(f"生成的文本: {generated_text}")

    print("-" * 60)