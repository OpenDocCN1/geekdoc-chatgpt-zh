# GPT-2 实战拆解：从中文语料切分到增量推理

想自己跑一个中文 GPT-2 的微调与生成，先得把原始评论文本变成模型能吃进去的 token 序列。做法是用一个对中文友好的分词器把 ChnSentiCorp.txt 逐行读入，按逗号切分后把句子部分拼起来做编码，把每个 token 的 id 全部收集到列表里，最后把这个列表重复五遍并转成 torch tensor，方便后续做语言建模的采样训练。

from transformers import BertTokenizer

tokenizer = BertTokenizer.from_pretrained("uer/gpt2-chinese-cluecorpussmall")

token_list = []

with open("./dataset/ChnSentiCorp.txt", mode="r", encoding="utf-8") as emotion_file:

    for line in emotion_file.readlines():

        line = line.strip().split(",")

        text = "".join(line[1:])

        inputs = tokenizer(text,return_tensors='pt')

        token = input_ids = inputs["input_ids"]

        attention_mask = inputs["attention_mask"]

        for id in token[0]:

            token_list.append(id.item())

token_list = torch.tensor(token_list * 5)

## 随机窗口采样的数据集

训练时不想固定切片，需要一个能随机抽取连续片段的 Dataset。下面这个类接收好 tensor 和期望的序列长度，每次取样都随机挑一个起点，切出长度+1 的窗口，前半段当输入，后半段当标签，长度按整除计算。

[PRE0]python

class TextSamplerDataset(torch.utils.data.Dataset):

    def __init__(self, data, seq_len):

        super().__init__()

        self.data = data

        self.seq_len = seq_len

    def __getitem__(self, index):

        rand_start = torch.randint(0, self.data.size(0) - self.seq_len, (1,))

        full_seq = self.data[rand_start : rand_start + self.seq_len + 1].long()

        return full_seq[:-1],full_seq[1:]

    def __len__(self):

        return self.data.size(0) // self.seq_len

## 带缓存的逐词生成演示

在推理端，GPT-2 的生成可以利用 past_key_values 来避免重复计算。先用英文 tokenizer 把首句编码，包成 tensor 作为初始上下文，past 为空。循环 30 步里，每次把当前 context 和上轮的 past 喂给模型，拿到 CausalLMOutputWithPastAndCrossAttentions，更新 past，再从 logits 最后一个位置取 argmax 得到下一个 token，把它压回 context 并累加到 generated 列表里，最后解码并按句号截断打印。

[PRE1]python

#!/usr/bin/env Python

# coding=utf-8

from transformers import GPT2LMHeadModel, GPT2Tokenizer

import torch

# 初始化GPT-2模型的Tokenizer类

tokenizer = GPT2Tokenizer.from_pretrained("gpt2")

# 初始化GPT-2模型，此处以初始化GPT2LMHeadModel()类的方式调用GPT-2模型

model = GPT2LMHeadModel.from_pretrained('gpt2')

# model.config.use_return_dict = None

# print(model.config.use_return_dict)

# GPT模型第一次迭代输入的上下文内容，将其编码以序列化

# 同时，generated也用来存储GPT2模型所有迭代生成的Token索引

generated = tokenizer.encode("The Manhattan bridge")

# 将序列化后的第一次迭代的上下文内容转化为PyTorch中的tensor形式

context = torch.tensor([generated])

# 第一次迭代时还没有past_key_values元组

past_key_values = None

for i in range(30):

    ...

    此时模型model返回的output为CausalLMOutputWithPastAndCrossAttentions类，

    模型返回的logits和past_key_values对象为其中的属性

    CausalLMOutputWithPastAndCrossAttentions(

        loss=loss,

        logits=lm_logits,

        past_key_values=transformer_outputs.past_key_values,

        hidden_states=transformer_outputs.hidden_states,

        attentions=transformer_outputs.attentions,

        cross_attentions=transformer_outputs.cross_attentions,

    )

    ...

    output = model(context, past_key_values=past_key_values)

    past_key_values = output.past_key_values

    # 此时获取GPT2模型计算的输出结果hidden_states张量中第二维度最后一个元素的argmax值全得出的

    # 计算生成的下一个Token。注意，此时若是第一次迭代，则输出结果hidden_states张量的形状为

    (batch_size, sel_len, n_state);

    # 此时若是第二次及之后的迭代，则输出结果hidden_states张量的形状为(batch_size, 1, n_state),

    all_head_size=n_state=nx=768

    token = torch.argmax(output.logits[..., -1, :])

    # 将本次迭代生成的Token的张量变为二维张量，以作为下一次GPT2模型迭代计算的上下文context

    context = token.unsqueeze(0)

    # 将本次迭代生成的Token的序列索引变为列表存入generated

    generated += [token.tolist()]

    # 将generated中所有的Token的索引转化为Token字符

    sequence = tokenizer.decode(generated)

    sequence = sequence.split(".")[:-1]

    print(sequence)

## 模型骨架与生成准备

GPT2LMHeadModel 继承自 GPT2PreTrainedModel，内部由 Transformer 主体和一个线性 lm_head 组成，lm_head 把 768 维的 hidden states 投到词表大小。prepare_inputs_for_generation 会在已有缓存时只保留最后 token，并按需生成 position_ids，保证增量生成时的输入格式正确。forward 的签名则完整承接了文本生成所需的所有控制参数。

[PRE2]python

class GPT2LMHeadModel(GPT2PreTrainedModel):

    _keys_to_ignore_on_load_missing = [r"h\.\d+\.attn\.masked_bias",

                                        r"lm_head\.weight"]

    def __init__(self, config):

        super().__init__(config)

        # 初始化GPT2Model(config)类

        self.transformer = GPT2Model(config)

        # self.lm_head为将GPT2Model(config)计算输出的hidden_states张量的最后一个维度由768维

        (config.n_embd)投影为词典大小维度(config.vocab_size)的输出层，此时hidden_states张量的形状将会由(batch_size, 1, n_embed)投影变为lm_logits张量的(batch_size, 1, vocab_size)

        self.lm_head = nn.Linear(config.n_embd, config.vocab_size, bias=False)

        # 重新初始化权重矩阵

        self.init_weights()

    def get_output_embeddings(self):

        return self.lm_head

    def prepare_inputs_for_generation(self, input_ids, past=None, **kwargs):

        token_type_ids = kwargs.get("token_type_ids", None)

        # only last token for inputs_ids if past is defined in kwargs

        if past:

            input_ids = input_ids[:, -1].unsqueeze(-1)

            if token_type_ids is not None:

                token_type_ids = token_type_ids[:, -1].unsqueeze(-1)

        attention_mask = kwargs.get("attention_mask", None)

        position_ids = kwargs.get("position_ids", None)

        if attention_mask is not None and position_ids is None:

            # create position_ids on the fly for batch generation

            position_ids = attention_mask.long().cumsum(-1) - 1

            position_ids.masked_fill_(attention_mask == 0, 1)

            if past:

                position_ids = position_ids[:, -1].unsqueeze(-1)

        else:

            position_ids = None

        return {

            "input_ids": input_ids,

            "past_key_values": past,

            "use_cache": kwargs.get("use_cache"),

            "position_ids": position_ids,

            "attention_mask": attention_mask,

            "token_type_ids": token_type_ids,

        }

    @add_start_docstrings_to_model_forward(GPT2_INPUTS_DOCSTRING)

    @add_code_sample_docstrings(

        tokenizer_class=_TOKENIZER_FOR_DOC,

        checkpoint="gpt2",

        output_type=CausalLMOutputWithPastAndCrossAttentions,

        config_class=_CONFIG_FOR_DOC,

    )

    def forward(

        self,

        input_ids=None,

        past_key_values=None,

        attention_mask=None,

        token_type_ids=None,

        position_ids=None,

        head_mask=None,

        inputs_embeds=None,

        encoder_hidden_states=None,

        encoder_attention_mask=None,

        labels=None,