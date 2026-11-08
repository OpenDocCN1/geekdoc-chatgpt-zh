LoRA 精简微调 ChatGLM-6B 完整流程

本文把一次极低成本的 ChatGLM-6B 微调拆成可复用的步骤：先冻结原模型、只给需要的位置注入低秩适配器、构造仅包含适配器参数的优化器、跑训练并分别保存完整权重与仅 LoRA 权重。整个过程参数量大幅下降，显存和训练时间都更友好，代码保持最小化实现。

在适配器定义层面，针对线性层和嵌入层分别提供构造入口，支持 rank、dropout、alpha 等超参的灵活注入。

            fan_in, fan_out, fan_in_fan_out=False, rank=rank,

            lora_dropout_p=lora_dropout_p, lora_alpha=lora_alpha

        )

    @classmethod

    def from_embedding(cls, layer, rank=4, lora_dropout_p=0.0, lora_alpha=1):

        fan_in, fan_out = layer.weight.shape

        return cls(

            fan_in, fan_out, fan_in_fan_out=True, rank=rank,

            lora_dropout_p=lora_dropout_p, lora_alpha=lora_alpha

        )

训练主循环使用进度条展示损失与学习率，借助 accelerator 完成反向传播，步进调度器与优化器，最后同时导出完整模型和仅含适配器的权重。

[PRE0]python

python

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

torch.save(model.state_dict(), "./glm6b_lora_all.pth")

lora_state_dict = get_lora_state_dict(model)

torch.save(lora_state_dict, "./glm6b_lora_only.pth")

参数筛选工具先按名字过滤，再单独抽出适配器参数，方便只优化小部分权重。

[PRE1]python

def get_params_by_name(model, print_shapes=False, name_filter=None):

    for n, p in model.named_parameters():

        if name_filter is None or name_filter(n):

            if print_shapes:

                print(n, p.shape)

            yield p

def get_lora_params(model, print_shapes=False):

    return get_params_by_name(model, print_shapes=print_shapes,

                              name_filter=name_is_lora)

优化器只接收适配器参数组，学习率设为 2e-5，使用 AdamW 标准动量。

[PRE2]python

lora_parameters = [{"params": list(get_lora_params(model))}]

optimizer = torch.optim.AdamW(lora_parameters.parameters(), lr=2e-5, betas=(0.9, 0.999), eps=1e-5)

注册与管理适配器的通用逻辑通过配置驱动实现，支持添加、合并、移除操作，并提供参数数量统计。

[PRE3]python

default_lora_config = {

    # specify which layers to add lora to, by default only add to linear layers

    nn.Linear: {

        "weight": partial(LoRAParametrization.from_linear, rank=4),

    },

}

def apply_lora(layer, register=True, merge=False, lora_config=default_lora_config):

    """add lora parametrization to a layer, designed to be used with model.apply"""

    if register:

        if type(layer) in lora_config:

            for attr_name, parametrization in lora_config[type(layer)].items():

                parametrize.register_parametrization(layer, attr_name,

parametrization(layer))

        else: # this will remove all parametrizations, use with caution

            if hasattr(layer, "parametrizations"):

                for attr_name in layer.parametrizations.keys():

                    parametrize.remove_parametrizations(layer, attr_name,

leave_parametrized=merge)

def add_lora(model, lora_config=default_lora_config):

    """add lora parametrization to all layers in a model. Calling it twice will add lora

twice"""

    model.apply(partial(apply_lora, lora_config=lora_config))

def merge_lora(model):

    """merge lora parametrization to all layers in a model. This will remove all

parametrization"""

    model.apply(partial(apply_lora, register=False, merge=True))

def remove_lora(model):

    """remove lora parametrization to all layers in a model. This will remove all

parametrization"""

    model.apply(partial(apply_lora, register=False, merge=False))

def get_parameter_number(model):

    total_num = sum(p.numel() for p in model.parameters())

    trainable_num = sum(p.numel() for p in model.parameters() if p.requires_grad )

    return {"total_para_num:",total_num,"trainable_para_num:",trainable_num}

可视化可训练比例的辅助函数，兼容 DeepSpeed 的特殊计数。

[PRE4]python

def print_trainable_parameters(model):

    trainable_params = 0

    all_param = 0

    for _, param in model.named_parameters():

        num_params = param.numel()

        if num_params == 0 and hasattr(param, "ds_numel"):

            num_params = param.ds_numel

        all_param += num_params

        if param.requires_grad:

            trainable_params += num_params

    print(

        f"trainable params: {trainable_params} || all params: {all_param} || trainable%: {100 * trainable_params / all_param}")

实际加载模型时先半精度上显存并冻结全部参数，随后仅对包含 query_key_value 的模块注入 LoRA，最后打印可训练参数占比。

[PRE5]python

model = xiaohua_model.XiaohuaModel(model_path="../huggingface_saver/chatglm6b.pth",

config=config, strict=False)

model = model.half().cuda()

for name,param in model.named_parameters():

    param.requires_grad = False

#下面就是LoRA的部分

from minlora.model import *

from minlora.utils import *

for key,_layer in model.named_modules():

    if "query_key_value" in key:

        add_lora(_layer)

xiaohua_model.print_trainable_parameters(model)

用于识别适配器权重名称的判断与导出仅 LoRA 状态字典的函数。

[PRE6]python

def name_is_lora(name):

    return (

        len(name.split(".")) >= 4

        and (name.split(".")[-4]) == "parametrizations"

        and name.split(".")[-1] in ["lora_A", "lora_B"]

    )

def get_lora_state_dict(model):

    return {k: v for k, v in model.state_dict().items() if name_is_lora(k)}

完整的初始化片段，包含配置、半精度加载、全量冻结以及按模块选择性添加 LoRA。

[PRE7]python

import torch

from transformers import AutoTokenizer

from torch.utils.data import RandomSampler, DataLoader

from 第十八章.chatGLM_spo.huggingface_saver import xiaohua_model,configuration_chatglm,modeling_chatglm

from tqdm import tqdm

config = configuration_chatglm.ChatGLMConfig()

#这里设置config中的pre_seq_len与prefix_projection，只有这2项设置好了才行

model = xiaohua_model.XiaohuaModel(model_path="../huggingface_saver/chatglm6b.pth",config=config,strict=False)

model = model.half().cuda()

for name,param in model.named_parameters():

    param.requires_grad = False

# 下面就是LoRA的部分

from minlora.model import *

from minlora.utils import *

for key,_layer in model.named_modules():

    if "query_key_value" in key:

        add_lora(_layer)