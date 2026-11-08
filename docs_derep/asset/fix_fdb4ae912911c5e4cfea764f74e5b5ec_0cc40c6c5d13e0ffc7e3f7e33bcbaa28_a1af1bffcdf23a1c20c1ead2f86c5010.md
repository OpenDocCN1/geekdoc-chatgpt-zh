单卡微调 Llama：4bit + LoRA + NEFTune 实战笔记

想让 Llama 这类指令模型快速吸收自己的对话数据，用一张消费显卡也能跑通，关键在于显存压缩、参数冻结和损失定位。先用裸训练环路把底层逻辑拆开，再给出 TRL 的封装方案，照着改路径就能复现。

加载与参数高效化
分词器采用非快速模式，填充靠右边，pad 符号指向 eos，避免拼接错位。主干用 4bit 加载，启用双量化、nf4 量化类型，计算用 bfloat16。LoRA 只动七个线性投影 q_proj/v_proj/k_proj/o_proj/gate_proj/down_proj/up_proj，秩 8、alpha 16、dropout 0.05，打印可训练规模后搬到 GPU，用 AdamW 推进。

tokenizer = AutoTokenizer.from_pretrained(model_path, use_fast=False)

tokenizer.padding_side = "right"

tokenizer.pad_token = tokenizer.eos_token

bnb_config = BitsAndBytesConfig(

    load_in_4bit=True,

    bnb_4bit_use_double_quant=True,

    bnb_4bit_quant_type="nf4",

    bnb_4bit_compute_dtype=torch.bfloat16

)

model = AutoModelForCausalLM.from_pretrained(model_path, quantization_config=bnb_config)

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

    task_type=TaskType.CAUSAL_LM,

    lora_alpha=16,

    lora_dropout=0.05

)

model = get_peft_model(model, peft_config)

model.print_trainable_parameters()

model.to("cuda")

optimizer = torch.optim.AdamW(model.parameters())

def sft_collate(batch, tokenizer, end_str, max_length):

    inputs = tokenizer(batch, max_length=max_length, padding=True, truncation=True)

    input_ids = inputs["input_ids"]

    input_len = len(input_ids[0])

    end_ids = tokenizer(end_str)["input_ids"]

    end_id_len = len(end_ids)

    loss_mask = []

    for input_id in input_ids:

        for i in range(len(input_id) - end_id_len, -1, -1):

            if input_id[i:i + end_id_len] == end_ids:

                mask = [1] * (input_len - 1)

                mask[:i + end_id_len - 1] = [0] * (i + end_id_len - 1)

                loss_mask.append(mask)

                break

        if i == 0:        #所有回答部分都被截断

            loss_mask.append([0] * (input_len - 1))

    inputs = {k: torch.tensor(v) for k, v in inputs.items()}

    loss_mask = torch.tensor(loss_mask)

    return inputs, loss_mask

collate_fn = functools.partial(sft_collate,

                                tokenizer=tokenizer,

                                end_str="<|start_header_id|>assistant<|end_header_id|>\n\n",

                                max_length=500)

sft_dataset = SFTDataset("/data/sft_data.json", tokenizer)

data_loader = DataLoader(sft_dataset, batch_size=2, collate_fn=collate_fn, shuffle=True)

epoch = 10

for i in range(epoch):

    for inputs, loss_mask in data_loader:

        inputs = {k: v.to("cuda") for k, v in inputs.items()}

        loss_mask = loss_mask.to("cuda")

        logits = model(**inputs).logits[:, :-1, :]

        labels = inputs["input_ids"][:, 1:]

        logits = logits.reshape(-1, logits.size(-1))

        labels = labels.reshape(-1)

        loss_mask = loss_mask.reshape(-1)

        loss = torch.nn.functional.cross_entropy(logits, labels, reduction="none")

        loss = loss * loss_mask

        loss = torch.mean(loss)

        loss.backward()

        optimizer.step()

        optimizer.zero_grad()

        print(loss.item())

自定义 collate 用来找回复起始标记 `<|start_header_id|>assistant<|end_header_id|>\n\n`，标记之前的位置全部掩掉，只让模型为生成部分负责梯度。批次 2 条、打乱、最大长度 500。训练时做 logits 与 label 的位移，展平后算交叉熵，用 mask 加权取均值，再反向更新。

手工版 NEFTune
在基础环路上加嵌入扰动可提升泛化。把 input_ids 换成词嵌入，在嵌入空间里注入均匀噪声，噪声幅度由 alpha 除以维度平方根决定，这里 alpha 取 10。

[PRE0]python
//Chapter6/sft_NEFT.py

epoch = 10

neftune_noise_alpha = 10

for i in range(epoch):

    for inputs, loss_mask in data_loader:

        input_ids = inputs.pop("input_ids")

        input_embeddings = model.base_model.model.model.embed_tokens(input_ids)

        dims = torch.tensor(input_embeddings.size(1) * input_embeddings.size(2))

        mag_norm = neftune_noise_alpha / torch.sqrt(dims)

        input_embeddings = input_embeddings + torch.zeros_like(input_embeddings).uniform_(- mag_norm, mag_norm)

        inputs["inputs_embeds"] = input_embeddings

        inputs = {k: v.to("cuda") for k, v in inputs.items()}

        loss_mask = loss_mask.to("cuda")

        logits = model(**inputs).logits[:, :-1, :]

        labels = input_ids[:, 1:].to("cuda")

        logits = logits.reshape(-1, logits.size(-1))

        labels = labels.reshape(-1)

        loss_mask = loss_mask.reshape(-1)

        loss = torch.nn.functional.cross_entropy(logits, labels, reduction="none")

        loss = loss * loss_mask

        loss = torch.mean(loss)

        loss.backward()

        optimizer.step()

        optimizer.zero_grad()

        print(loss.item())

数据准备
原始 json 里 query 映射为 prompt，answer 映射为 completion，整理成 HuggingFace Dataset 即可供 collator 使用。

[PRE1]python
items = []

with open("./data/sft_data.json", "r", encoding="utf8") as f:

    for line in f:

        item = json.loads(line)

        items.append({"prompt": item["query"], "completion": item["answer"]})

dataset = Dataset.from_list(items)

TRL 封装
不想手写循环，TRL 的 SFTConfig 已内置 NEFTune，一次配置噪声强度、每设备 batch、序列长度、轮数和日志策略即可。

[PRE2]python
sft_config = SFTConfig(output_dir="/tmp",

                      neftune_noise_alpha=10,

                      per_device_train_batch_size=1,

                      max_seq_length=100,

                      num_train_epochs=10,

                      logging_steps=10,

                      logging_strategy="steps")

只算 completion 损失
官方建议只让回复段参与优化，用 DataCollatorForCompletionOnlyLM 指定响应模板即可自动屏蔽 prompt。

[PRE3]python
response_template = "<|start_header_id|>assistant<|end_header_id|>\n\n"

collator = DataCollatorForCompletionOnlyLM(response_template, tokenizer=tokenizer)

工程化示例
把数据读取、4bit 量化、LoRA 和 SFTTrainer 串起来。注意这里计算类型换成 float16，并在加载时显式指定 torch_dtype。

[PRE4]python
#Chapter6/sft_trainer.py

import json

import torch

from datasets import Dataset

from peft import LoraConfig, get_peft_model, TaskType

from transformers import AutoTokenizer, BitsAndBytesConfig, AutoModelForCausalLM

from trl import SFTTrainer, SFTConfig, DataCollatorForCompletionOnlyLM

items = []

with open("./data/sft_data.json", "r", encoding="utf8") as f:

    for line in f:

        item = json.loads(line)

        items.append({"prompt": item["query"], "completion": item["answer"]})

dataset = Dataset.from_list(items)

model_path = r'D:\work\models\Meta-Llama-3.1-8B-Instruct'

tokenizer = AutoTokenizer.from_pretrained(model_path, use_fast=False)

tokenizer.padding_side = "right"

tokenizer.pad_token = tokenizer.eos_token

bnb_config = BitsAndBytesConfig(

    load_in_4bit=True,

    bnb_4bit_use_double_quant=True,

    bnb_4bit_quant_type="nf4",

    bnb_4bit_compute_dtype=torch.float16

)

model = AutoModelForCausalLM.from_pretrained(model_path, quantization_config=bnb_config, torch_dtype=torch.float16)

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