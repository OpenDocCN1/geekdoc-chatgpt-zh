ChatGLM6B + LoRA 微调与推理全流程代码实录

想用最少显存把 ChatGLM6B 快速微调起来？下面这套实操片段把参数筛选、优化器构造、LoRA 注入、权重载入和自回归采样串成了一条线，直接拿来就能跑。

先从参数的挑选开始。只保留 LoRA 产生的可训练权重，其他全部冻结，这一步决定了后续优化器只会动到低秩矩阵。

[PRE0]python

def get_params_by_name(model, print_shapes=False, name_filter=None):

    for n, p in model.named_parameters():

        if name_filter is None or name_filter(n):

            if print_shapes:

                print(n, p.shape)

            yield p

def get_lora_params(model, print_shapes=False):

    return get_params_by_name(model, print_shapes=print_shapes,

        name_filter=name_is_lora)

搞定筛选后，用这批参数组建 AdamW。学习率 2e-5，动量 0.9 / 0.999，eps 1e-5，是常见的稳定配置。

[PRE1]python

lora_parameters = [{"params": list(get_lora_params(model))}]

optimizer = torch.optim.AdamW(lora_parameters.parameters(), lr=2e-5, betas=(0.9, 0.999), eps=1e-5)

模型加载时要放宽严格校验，strict 设为 False。随后遍历模块，在包含 query_key_value 的层上调用 add_lora，给原权重注入低秩适配器。冻结全量参数后，再把完整微调存档载入。

[PRE2]python

#注意此时的strict设置成False

model = xiaohua_model.XiaohuaModel(model_path="../huggingface_saver/chatglm6b.pth",

config=config, strict=False)

# 下面就是LoRA的部分

from minlora.model import *

from minlora.utils import *

for key, _layer in model.named_modules():

    if "query_key_value" in key:

        add_lora(_layer)

for name, param in model.named_parameters():

    param.requires_grad = False

model.load_state_dict(torch.load("./glm6b_lora.pth"))

推理时通常只用 LoRA 的增量权重。同样先在 query_key_value 上挂载适配器，冻结基座参数，用 strict=False 只加载 LoRA 分支，最后转半精度上 GPU。

[PRE3]python

for key, _layer in model.named_modules():

    if "query_key_value" in key:

        add_lora(_layer)

for name, param in model.named_parameters():

    param.requires_grad = False

model.load_state_dict(torch.load("./glm6b_lora_only.pth"), strict=False)

model = model.half().cuda()

采样部分用到了 nucleus 采样。先对概率降序排序并累加，用阈值 p 屏蔽尾部质量，再归一化后多项式抽样。主流程里用 tokenizer 把提示和文本拼接，控制最大源长度，循环生成直到遇到结束符。

[PRE4]python

def sample_top_p(probs, p):

    probs_sort, probs_idx = torch.sort(probs, dim=-1, descending=True)

    probs_sum = torch.cumsum(probs_sort, dim=-1)

    mask = probs_sum - probs_sort > p

    probs_sort[mask] = 0.0

    probs_sort.div_(probs_sort.sum(dim=-1, keepdim=True))

    next_token = torch.multinomial(probs_sort, num_samples=1)

    next_token = torch.gather(probs_idx, -1, next_token)

    return next_token

import os

os.environ["CUDA_VISIBLE_DEVICES"] = "0"

import torch

from transformers import AutoTokenizer

from torch.utils.data import RandomSampler, DataLoader

from 第十八章.chatglm_spo.huggingface_saver import xiaohua_model, configuration_chatglm, modeling_chatglm

from tqdm import tqdm

config = configuration_chatglm.ChatGLMConfig()

#这里设置config中的pre_seq_len与 prefix_projection，只有这2项设置好了才行

model = xiaohua_model.XiaohuaModel(model_path="../huggingface_saver/chatglm6b.pth", config=config, strict=False)

# 下面就是LoRA的部分

from minlora.model import *

from minlora.utils import *

for key, _layer in model.named_modules():

    if "query_key_value" in key:

        add_lora(_layer)

for name, param in model.named_parameters():

    param.requires_grad = False

#model.load_state_dict(torch.load("./glm6b_lora.pth"))        #加载保存的全部数据存档

#加载LoRA存档

model.load_state_dict(torch.load("./glm6b_lora_only.pth"), strict=False)

model = model.half().cuda()

xiaohua_model.print_trainable_parameters(model)

model.eval()

max_len = 288; max_src_len = 256

prompt_text = "按给定的格式抽取文本信息。\n文本:"

save_data = []

f1 = 0.0

max_tgt_len = max_len - max_src_len - 3

tokenizer = AutoTokenizer.from_pretrained("THUDM/chatglm-6b", trust_remote_code=True)

import time, json

s_time = time.time()

with open("../data/spo_l.json", "r", encoding="utf-8") as fh:

    for i, line in enumerate(tqdm(fh, desc="iter")):

        with torch.no_grad():

            sample = json.loads(line.strip())

            src_tokens = tokenizer.tokenize(sample["text"])

            prompt_tokens = tokenizer.tokenize(prompt_text)

            if len(src_tokens) > max_src_len - len(prompt_tokens):

                src_tokens = src_tokens[:max_src_len - len(prompt_tokens)]

            tokens = prompt_tokens + src_tokens + ["[gMASK]", "<sop>"]

            input_ids = tokenizer.convert_tokens_to_ids(tokens)

            for _ in range(max_src_len):

                input_ids_tensor = torch.tensor([input_ids]).to("cuda")

                logits, _, _ = model.forward(input_ids_tensor)

                logits = logits[:, -3]

                probs = torch.softmax(logits / 0.95, dim=-1)

                next_token = sample_top_p(probs, 0.9)  # 预设的top_p = 0.95

                # next_token = next_token.reshape(-1)

                # next_token = result_token[-3:-2]

                input_ids = input_ids[:-2] + [next_token.item()] + input_ids[-2:]

                if next_token.item() == 130005:

                    print("break")

                    break

            result = tokenizer.decode(input_ids)

            print(result)

            print("--------------------------------------------------")

底层实现上，LoRA 的参数化模块接受 fan_in、fan_out、是否翻转布局、秩 rank、dropout 和 alpha 等超参，用两个低秩矩阵近似原权重更新。

[PRE5]python

class LoRAParametrization(nn.Module):

    def __init__(self, fan_in, fan_out, fan_in_fan_out=False, rank=4, lora_dropout_p=0.0, lora_alpha=1):

        super().__init__()

        # if weight is stored as (fan_out, fan_in), the memory layout of A & B follows (W + BA)x

        # otherwise, it's x(W + AB). This allows us to tie the weights between linear layers and embeddings