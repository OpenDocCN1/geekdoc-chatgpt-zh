# ChatGLM6B + LoRA 实战：SPO 信息抽取微调全流程拆解

想让大模型学会按固定格式抽取主谓宾，关键在于把自回归建模的损失对齐到序列预测，同时用参数高效微调降低成本。下面这套做法以 ChatGLM-6B 为例，演示如何用 LoRA 做 SPO 抽取任务的端到端训练。

整体思路很直接：先改写模型的 forward，让它在训练时对齐下一个 token 的预测；再用 LoRA 只训练少量适配参数，配合 Accelerate、AdamW 和余弦学习率调度完成稳定训练。数据侧使用带提示词的序列到序列格式，训练时半精度加速，定期保存权重。

**模型前向改造的核心**  
在做因果语言建模时，需要把 logits 右移一位与输入右移一位的标签对齐，再把二维张量展平后计算交叉熵。这样既保留了原模型的 hidden_states 输出，也在有标签时返回损失。

**LoRA 配置要点**  
针对 ChatGLM 的 query_key_value 模块注入低秩适配，秩 r=8、alpha=32、dropout 0.1，推理模式关闭。配置可通过代码创建，也可保存为 json 复用，方便不同实验快速切换。

**训练脚本骨架**  
环境固定到单张 GPU，模型加载后套上 PEFT，再转半精度。数据通过 tokenizer 处理并构造 Seq2Seq 数据集，DataLoader 批大小设为 2 并丢弃不完整批。Accelerate 统一封装模型、优化器、数据加载器和调度器。

优化器选用 AdamW，基础学习率 2e-5，beta 0.9/0.999，eps 1e-5。学习率调度用 CosineAnnealing，T_max 2400，尾部不低于 2e-6。训练 20 个 epoch，每轮用 tqdm 实时展示 loss 和学习率，每 3 个 epoch 保存一次 state_dict。

下面是完整可复用的代码片段。

[PRE0]python

import os

os.environ['CUDA_VISIBLE_DEVICES'] = "0"

import torch

from tqdm import tqdm

from torch.utils.data import RandomSampler, DataLoader

from peft import get_peft_model, LoraConfig, TaskType, prepare_model_for_int8_training, get_peft_model_state_dict

from transformers import AutoModelForSeq2SeqLM, AutoTokenizer, AutoConfig

device = "cuda"

from 第十八章.chatGLM_spo.fintunning_peft_xiaohua import modeling_chatglm

model = modeling_chatglm.XiaohuaModel(model_path="../huggingface_saver/chatglm6b.pth")

peft_config = LoraConfig.from_pretrained("./peft")

model = get_peft_model(model, peft_config)

model.print_trainable_parameters()

model = model.half().to(device)  # .to(device)

prompt_text = "按给定的格式抽取文本信息。\n文本:"

from 第十八章.chatGLM_spo import get_data

tokenizer = AutoTokenizer.from_pretrained("THUDM/chatglm-6b", trust_remote_code=True)

all_train_data = get_data.get_train_data("../data/spo_0.json", tokenizer, 48, 48, prompt_text)

train_dataset = get_data.Seq2SeqDataSet(all_train_data)

train_loader = DataLoader(train_dataset, batch_size=2, drop_last=True, collate_fn=get_data.collate_fn, num_workers=0)

from accelerate import Accelerator

accelerator = Accelerator()

optimizer = torch.optim.AdamW(model.parameters(), lr=2e-5, betas=(0.9, 0.999), eps=1e-5)

lr_scheduler = torch.optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=2400, eta_min=2e-6, last_epoch=-1)

model, optim, train_loader, lr_scheduler = accelerator.prepare(model, optimizer, train_loader, lr_scheduler)

for epoch in range(20):

    pbar = tqdm(train_loader, total=len(train_loader))

    for batch in (pbar):

        input_ids = batch["input_ids"].cuda()

        labels = batch["labels"].cuda()

        _,_,loss = model.forward(input_ids,labels=labels)

        accelerator.backward(loss)

        #torch.nn.utils.clip_grad_norm_(model.parameters(), 1.)

        optimizer.step()

        lr_scheduler.step()  # 执行优化器

        optimizer.zero_grad()

        pbar.set_description(

            f"epoch:{epoch + 1}, train_loss:{loss.item():.5f}, lr:{lr_scheduler.get_last_lr()[0] * 1000:.5f}")

    if (epoch +1) %3 == 0:

        torch.save(model.state_dict(), "./glm6b_peft.pth")
[PRE1]

from transformers import AutoModelForSeq2SeqLM

from peft import get_peft_config, get_peft_model, LoraConfig, TaskType

peft_config = LoraConfig(

    task_type=TaskType.MODEL_TYPE,

    inference_mode=False, r=8, lora_alpha=32, lora_dropout=0.1,

    target_modules = ["query_key_value"]

)

model = "加载的模型"

model = get_peft_model(model, peft_config)

model.print_trainable_parameters()
[PRE2]

{
    "base_model_name_or_path": null,
    "bias": "none",
    "enable_lora": [
        true,
        false,
        true
    ],
    "fan_in_fan_out": true,
    "inference_mode": false,
    "lora_alpha": 32,
    "lora_dropout": 0.1,
    "merge_weights": false,
    "modules_to_save": null,
    "peft_type": "LORA", #PEFT类型
    "r": 8,
    "target_modules": [
        "query_key_value"
    ],
    "task_type": "CAUSAL_LM"
}
[PRE3]

import os

os.environ['CUDA_VISIBLE_DEVICES'] = "0"

import torch

from tqdm import tqdm

from torch.utils.data import RandomSampler, DataLoader

from peft import get_peft_model, LoraConfig, TaskType, prepare_model_for_int8_training, get_peft_model_state_dict

from transformers import AutoModelForSeq2SeqLM, AutoTokenizer, AutoConfig

device = "cuda"

from 第十八章.chatGLM_spo.fintunning_peft_xiaohua import modeling_chatglm

model = modeling_chatglm.XiaohuaModel(model_path="../huggingface_saver/chatglm6b.pth")

peft_config = LoraConfig.from_pretrained("./peft")

model = get_peft_model(model, peft_config)

model.print_trainable_parameters()

model = model.half().to(device) # .to(device)
[PRE4]

def forward(self,input_ids,labels = None,position_ids = None,attention_mask = None):

    logits,hidden_states = self.glm_model.forward(input_ids=input_ids,position_ids = None,attention_mask = None)

    loss = None

    if labels != None:

        shift_logits = logits[:, :-1, :].contiguous()

        shift_labels = input_ids[:, 1:].contiguous()

        # Flatten the tokens

        logits_1 = shift_logits.view(-1, shift_logits.size(-1))

        logits_2 = shift_labels.view(-1)

        loss = self.loss_fct(logits_1, logits_2)

    return logits,hidden_states,loss
[PRE5]

def forward(self,input_ids,position_ids = None,attention_mask = None,

            inputs_embeds = None,labels = None,output_attentions = None,output_hidden_states = None,return_dict = None,**kwargs):

    logits,hidden_states = self.glm_model.forward(input_ids=input_ids, **kwargs)

    shift_logits = logits[:, :-1, :].contiguous()

    shift_labels = input_ids[:, 1:].contiguous()

    # Flatten the tokens

    logits_1 = shift_logits.view(-1, shift_logits.size(-1))

    logits_2 = shift_labels.view(-1)

    loss = self.loss_fct(logits_1, logits_2)

    return logits,hidden_states,loss