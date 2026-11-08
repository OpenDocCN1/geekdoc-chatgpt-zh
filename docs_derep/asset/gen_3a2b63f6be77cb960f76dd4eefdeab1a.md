# 4位量化 + LoRA 实战：训一个可用的奖励模型

奖励模型是把人类偏好变成可优化信号的关键。本文用 LLaMA-3.1-8B-Instruct 做基座，配合 4-bit 量化和 LoRA 低成本微调，完成从偏好数据到 RewardTrainer 的完整闭环。

整体思路很清晰：先把大模型压成 4-bit 降低显存占用，再用 LoRA 只训练少量适配参数；偏好数据按 question + chosen / question + rejected 的方式拼接并分词；最后交给 TRL 的 RewardTrainer 进行对比学习，保存为可用的奖励模型。

数据侧以 preference.json 为例，每条记录包含 question、chosen、rejected 三个字段。举例来说，关于 Pandas 加载 CSV 的提问，优质回复会明确指出用 read_csv 函数读取文件并得到 DataFrame，而不够好的回复只笼统说用 Pandas 加载。另一个关于自然语言处理定义的提问，优质回复会说明它是计算机科学和人工智能的分支，核心是机器与人类语言的互动，而简化版回复仅说它是计算机科学的一部分。这类成对样例构成了模型学习的偏好信号。

[PRE0]python
#Chapter7/train_reward.py

import torch

from datasets import Dataset

import json

from peft import LoraConfig, TaskType, get_peft_model, prepare_model_for_kbit_training

from transformers import AutoTokenizer, BitsAndBytesConfig, AutoModelForSequenceClassification

from trl import RewardTrainer, RewardConfig

model_path = r'Your_model_path\Meta-Llama-3.1-8B-Instruct'

tokenizer = AutoTokenizer.from_pretrained(model_path, use_fast=False)

tokenizer.padding_side = "right"

tokenizer.pad_token = tokenizer.eos_token

bnb_config = BitsAndBytesConfig(

    load_in_4bit=True,

    bnb_4bit_use_double_quant=True,

    bnb_4bit_quant_type="nf4",

    bnb_4bit_compute_dtype=torch.float16

)

model = AutoModelForSequenceClassification.from_pretrained(model_path,
    num_labels=1,
    quantization_config=bnb_config)
[PRE1]python
model.config.pad_token_id = tokenizer.pad_token_id

peft_config = LoraConfig(
    r=8,
    target_modules=["q_proj",
                    "v_proj",
                    "k_proj",
                    "o_proj",
                    "gate_proj",
                    "down_proj",
                    "up_proj"
                    ],
    task_type=TaskType.SEQ_CLS,
    lora_alpha=16,
    lora_dropout=0.05
)

model = prepare_model_for_kbit_training(model)

model = get_peft_model(model, peft_config)

model.print_trainable_parameters()
[PRE2]python
items = []

with open("./data/preference.json", "r", encoding="utf8") as f:
    for line in f:
        item = json.loads(line)
        items.append(item)

dataset = Dataset.from_list(items)

def process_func(example):
    chosen = example["question"] + example["chosen"]
    rejected = example["question"] + example["rejected"]
    tokenized_chosen = tokenizer(chosen)
    tokenized_rejected = tokenizer(rejected)
    new_example = {}
    new_example["input_ids_chosen"] = tokenized_chosen["input_ids"]
    new_example["attention_mask_chosen"] = tokenized_chosen["attention_mask"]
    new_example["input_ids_rejected"] = tokenized_rejected["input_ids"]
    new_example["attention_mask_rejected"] = tokenized_rejected["attention_mask"]
    return new_example

dataset = dataset.map(process_func, remove_columns=['question', 'chosen', 'rejected'])
[PRE3]python
config = RewardConfig(output_dir="./reward_model")

config.num_train_epochs = 1

config.per_device_train_batch_size = 1
[PRE4]python
trainer = RewardTrainer(
    model=model,
    tokenizer=tokenizer,
    args=config,
    train_dataset=dataset
)

trainer.train()

trainer.save_model("./reward_model")
[PRE5]json
{"query": "请给出保持健康的3种方法。"}

{"query": "三原色是什么？"}

{"query": "请描述原子的结构。"}

{"query": "如何减少空气污染？"}

{"query": "描述一次你不得不做出困难决定的经历。"}

{"query": "天空为什么是蓝色的？"}

{"query": "解释为什么4/16等同于1/4？"}

{"query": "写一个关于主人公必须做出重要职业决定的第三人称叙述的短故事。"}

{"query": "请渲染一座房子的三维模型。"}

{"query": "朱利叶斯·凯撒是如何死亡的？"}
[PRE6]python
def compute_rewards(
    self,
    scores: torch.FloatTensor,
    logprobs: torch.FloatTensor,
    ref_logprobs: torch.FloatTensor,
    masks: torch.LongTensor,
):

    """
    Compute per token rewards from scores and K-L penalty.

    Args:
        scores (`torch.FloatTensor`):
            Scores from the reward model, shape (`batch_size`)

        logprobs (`torch.FloatTensor`: