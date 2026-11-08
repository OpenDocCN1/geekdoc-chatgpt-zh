# 让 GPT-2 生成更可控：禁词、主题引导与重复抑制实操

大模型自由生成很爽，但一失控就容易跑偏。下面用 GPT-2 演示几组常用的可控生成技巧：先用 LogitsProcessor 把敏感词挡掉并设置强制终止，再通过主题词前置引导方向，最后用重复惩罚和 N-Gram 禁止来提升多样性。所有示例均基于 transformers 的 generate 流程。

class NoBadWordsLogitsProcessor(LogitsProcessor):
    def __init__(self, bad_words_ids, eos_token_id):
        self.bad_words_ids = bad_words_ids
        self.eos_token_id = eos_token_id
    def __call__(self, input_ids, scores):
        # 遍历需屏蔽的词，对已出现的词把对应分数压到负无穷
        for bad_word_id in self.bad_words_ids:
            bad_word_len = len(bad_word_id)
            # 当模型刚生成出禁用词的尾部片段时触发惩罚
            if input_ids[0, -bad_word_len:].tolist() == bad_word_id:
                scores[:, bad_word_id] = -float("inf")
        return scores

# 载入分词器与模型
model_name = "gpt2"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)
device = "cuda" if torch.cuda.is_available() else "cpu"
model.to(device)

# 带有词表限制与结束符控制的生成封装
def generate_text_with_constraints(prompt, max_length=100,
                                  temperature=0.7, bad_words=None, stop_token=None):
    """
    在生成时屏蔽指定词汇，并可指定一个强制结束符号。
    参数:
        prompt: 初始提示
        max_length: 最长生成长度
        temperature: 随机性调节
        bad_words: 禁止出现的词列表
        stop_token: 遇到即停止的符号
    返回:
        str: 解码后的文本
    """
    bad_words_ids = [tokenizer(bad_word, add_special_tokens=False).input_ids for bad_word in bad_words] if bad_words else []
    input_ids = tokenizer(prompt, return_tensors="pt").input_ids.to(device)
    logits_processor = LogitsProcessorList([NoBadWordsLogitsProcessor(
        bad_words_ids, tokenizer.eos_token_id)])
    output = model.generate(
        input_ids=input_ids,
        max_length=max_length,
        temperature=temperature,
        logits_processor=logits_processor,
        eos_token_id=tokenizer(stop_token).input_ids[0] if stop_token else tokenizer.eos_token_id,
        do_sample=True
    )
    generated_text = tokenizer.decode(output[0], skip_special_tokens=True)
    return generated_text

# 演示：禁止出现战争相关词，以句号为终止
prompt = "在未来的科技世界中，"
bad_words = ["战争", "冲突"]
stop_token = "。"
restricted_text = generate_text_with_constraints(prompt, bad_words=bad_words, stop_token=stop_token)
print("受限生成的文本:", restricted_text)

[PRE0]python
# 主题限定生成
def generate_with_topic(prompt, topic_words,
                       max_length=100, temperature=0.7):
    topic_prompt = " ".join(topic_words) + " " + prompt
    return generate_text_with_constraints(topic_prompt,
                                         max_length=max_length, temperature=temperature)

# 以智能家居和人工智能为主题进行引导
topic_words = ["智能家居", "人工智能"]
topic_restricted_text = generate_with_topic("未来的生活将如何改变？ ", topic_words)
print("主题引导的生成文本:", topic_restricted_text)

[PRE1]python
from transformers import AutoTokenizer, AutoModelForCausalLM
import torch

model_name = "gpt2"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)
device = "cuda" if torch.cuda.is_available() else "cpu"
model.to(device)

# 基于重复惩罚与 N-Gram 避免的生成封装
def generate_with_repetition_control(prompt, max_length=100,
        repetition_penalty=1.2, no_repeat_ngram_size=3, temperature=0.7):
    """
    通过惩罚已出现词与禁止重复 N-Gram 来提升生成多样性。
    参数:
        prompt: 提示语
        max_length: 最大长度
        repetition_penalty: 重复惩罚强度
        no_repeat_ngram_size: 禁止重复的 n-gram 大小
        temperature: 采样温度
    返回:
        str: 生成结果
    """
    input_ids = tokenizer(prompt, return_tensors="pt").input_ids.to(device)
    output = model.generate(
        input_ids=input_ids,
        max_length=max_length,
        repetition_penalty=repetition_penalty,
        no_repeat_ngram_size=no_repeat_ngram_size,
        temperature=temperature,
        do_sample=True,
        eos_token_id=tokenizer.eos_token_id
    )
    generated_text = tokenizer.decode(output[0], skip_special_tokens=True)
    return generated_text

prompt = "未来的人工智能技术将会如何改变日常生活？"
controlled_text = generate_with_repetition_control(prompt)
print("去重控制后的文本:", controlled_text)

[PRE2]python
# 遍历不同惩罚与 n-gram 组合
penalties = [1.1, 1.5]
ngrams = [2, 3]
for penalty in penalties:
    for ngram_size in ngrams:
        print(f"\n重复惩罚: {penalty}, n-gram限制: {ngram_size}")
        text = generate_with_repetition_control(prompt,
            repetition_penalty=penalty, no_repeat_ngram_size=ngram_size)
        print("生成的文本:", text)
        print("-" * 60)

[PRE3]
重复惩罚：1.1，n-gram限制：2
生成的文本：未来的人工智能技术将会如何改变日常生活？智能技术将在教育、医疗和交通等领域得到广泛应用，使人们的生活更加便捷。

重复惩罚：1.1，n-gram限制：3
生成的文本：未来的人工智能技术将会如何改变日常生活？人工智能将深入影响各行各业，从教育到健康管理，再到家庭娱乐，为生活带来全新体验。

重复惩罚：1.5，n-gram限制：2
生成的文本：未来的人工智能技术将会如何改变日常生活？智能系统将为家庭、企业和政府提供高效的决策支持，使生活更加智能化。

重复惩罚：1.5，n-gram限制：3
生成的文本：未来的人工智能技术将会如何改变日常生活？AI技术将从根本上改变人们的日常生活，优化各方面的资源分配，推动生活质量的提升。

[PRE4]python
from transformers import AutoTokenizer, AutoModelForCausalLM
import torch

model_name="gpt2"
tokenizer=AutoTokenizer.from_pretrained(model_name)
model=AutoModelForCausalLM.from_pretrained(model_name)
device="cuda" if torch.cuda.is_available() else "cpu"
model.to(device)

# 生成长度范围控制
def generate_with_length_control(prompt, min_length=10,
                                max_length=50, temperature=0.7):
    """
    利用最小与最大长度限制生成句子的长短。
    参数:
        prompt: 输入提示
        min_length: 最短生成长度
        max_length: 最长生成长度