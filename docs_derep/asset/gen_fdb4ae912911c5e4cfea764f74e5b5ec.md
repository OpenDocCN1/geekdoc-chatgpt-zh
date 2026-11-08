低成本训大模型：4bit LoRA + NEFTune 实战笔记

想在单张卡上把 Llama 类指令模型微调到自己的数据上，关键就是量化省显存、LoRA 只训小参数、损失只算回复部分。下面这套做法先用最原始的训练环路讲清楚原理，再给出 TRL 的标准封装，方便直接复现。

先把分词器和模型按 4bit 载入，右填充、pad 用 eos，保证后续拼接不乱。量化采用 nf4 + 双量化，计算用 bfloat16，LoRA 注入 q_proj/v_proj/k_proj/o_proj/gate_proj/down_proj/up_proj 七个模块，r=8、alpha=16、dropout=0.05。打印可训练参数、迁到 cuda 后用 AdamW 开始训练。

自定义 collate 的核心是找到响应开头标记 `<|start_header_id|>assistant<|end_header_id|>\n\n`，在该标记之前的位置全屏蔽 loss，只让模型为回答部分负责。batch 2、shuffle 开启、序列截断 500。训练环路里先对齐 logits 和 labels 的偏移，展平后再用 cross_entropy 计算，按 mask 取均值，反向传播。

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

在手动环路上加入嵌入噪声，可以增强泛化。这就是 NEFTune 的手工实现：把 input_ids 换成嵌入，在嵌入上加均匀噪声。噪声幅度按 `alpha / sqrt(dims)` 控制，alpha 设 10。

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

数据准备很简单，把原始 json 里 query → prompt，answer → completion 转成 HuggingFace Dataset，便于后续 collator 使用。

[PRE1]python
items = []

with open("./data/sft_data.json", "r", encoding="utf8") as f:

    for line in f:

        item = json.loads(line)

        items.append({"prompt": item["query"], "completion": item["answer"]})

dataset = Dataset.from_list(items)

如果不想自己写环路，TRL 的 SFTConfig 已经内置了 NEFTune。只需要把 noise alpha、batch size、序列长度、epoch 数等写进配置即可。

[PRE2]python
sft_config = SFTConfig(output_dir="/tmp",

                      neftune_noise_alpha=10,

                      per_device_train_batch_size=1,

                      max_seq_length=100,

                      num_train_epochs=10,

                      logging_steps=10,

                      logging_strategy="steps")

只计算 completion 部分的损失，官方推荐用 DataCollatorForCompletionOnlyLM，指定响应模板即可自动屏蔽 prompt。

[PRE3]python
response_template = "<|start_header_id|>assistant<|end_header_id|>\n\n"

collator = DataCollatorForCompletionOnlyLM(response_template, tokenizer=tokenizer)

完整工程化写法把数据读取、4bit 量化、LoRA 配置、SFTTrainer 全部串起来。注意这里量化计算 dtype 换成 float16，模型加载时显式指定 torch_dtype。

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