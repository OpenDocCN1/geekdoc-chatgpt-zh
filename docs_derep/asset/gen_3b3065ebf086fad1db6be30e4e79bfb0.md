下面是一套基于 Xiaohua 封装的 ChatGLM-6B 信息抽取微调实操拆解，从模型初始化、输入构造到数据管道和加速训练，步骤清晰可直接复现。

首先是模型加载与显存优化。采用 XiaohuaModel 包装本地权重，关闭严格校验后做 8 位量化，再转半精度并放到 GPU 上运行，降低显存占用同时保持推理速度。
[PRE0]python

model = xiaohua_model.XiaohuaModel(model_path="../huggingface_saver/chatglm6b.pth", config=config, strict=False)

model.glm_model.quantize(8, False)

model = model.half().cuda()

输入序列的组织遵循提示+原文+[gMASK]<sop>目标+<eop>的模板，先拼好 token 列表再统一转 id，后续掩码计算都基于此结构。
[PRE1]python

tokens = prompt_tokens + src_tokens + ["[gMASK]", "<sop>"] + tgt_tokens + ["<eop>"]

实际 token 化后的样子大致如下，可见提示词被切分、原文后接掩码符号，目标部分以 “原因:” 开头并以 <n> 分隔多条三元组。
[PRE2]python

['_', '你现在', '是一个', '信息', '抽取', '模型', ',', '请你', '帮我', '抽取', '出', '关系', '内容为', '"', '性能', '故障', '"', '_', '部件', '故障', '"', '_', '组成', '"', '和', '"', '检测', '工具', '"', '的相关', '三元', '组', ',', '三元', '组', '内部', '用', '"', '_', '"', '连接', ',', '三元', '组', '之间', '用', '\\', 'n', '分隔', '。', '文本', ':', '_', '故障', '现象', ':', '冷', '车', '起动', '困难', ',', '起动', '后', '发动机', '抖动', '严重', ',', '[gMASK]', '<sop>', '_', '<n>', '原因', ':', '车', '_', '部件', '故障', '_', '起动', '困难', '<n>', '发动机', '_', '部件', '故障', '_', '抖动', '<eop>']

数据构造的核心函数负责截断、拼接、掩码和填充。源文本和目标答案分别按最大长度裁剪，计算上下文结束位置后把前半段标签置为 -100，仅让模型学习掩码之后的目标生成部分，最后统一补齐到固定长度。
[PRE3]python

def get_train_data(data_path,tokenizer,max_len, max_src_len, prompt_text):

    max_tgt_len = max_len - max_src_len - 3

    all_data = []

    with open(data_path, "r", encoding="utf-8") as fh:

        for i, line in enumerate(fh):

            sample = json.loads(line.strip())

            src_tokens = tokenizer.tokenize(sample["text"])

            prompt_tokens = tokenizer.tokenize(prompt_text)

            if len(src_tokens) > max_src_len - len(prompt_tokens):

                src_tokens = src_tokens[:max_src_len - len(prompt_tokens)]

            tgt_tokens = tokenizer.tokenize("\n原因:"+sample["answer"])

            if len(tgt_tokens) > max_tgt_len:

                tgt_tokens = tgt_tokens[:max_tgt_len]

            tokens = prompt_tokens + src_tokens + ["[gMASK]", "<sop>"] + tgt_tokens + ["<eop>"]

            input_ids = tokenizer.convert_tokens_to_ids(tokens)

            context_length = input_ids.index(tokenizer.bos_token_id)

            mask_position = context_length - 1

            labels = [-100] * context_length + input_ids[mask_position + 1:]

            pad_len = max_len - len(input_ids)

            input_ids = input_ids + [tokenizer.pad_token_id] * pad_len

            labels = labels + [-100] * pad_len

            all_data.append(

                {"text": sample["text"], "answer": sample["answer"], "input_ids": input_ids, "labels": labels})

    return all_data

完整的工程片段包含分词器初始化、数据集封装与批量整理。Seq2SeqDataSet 直接透传预处理好的记录，collate 函数用 pad_sequence 按 batch_first 填充，填充值使用 3。示例入口展示了如何用 768 总长度、450 源长度的配置读取 spo 数据并构建 DataLoader。
[PRE4]python

import json

import torch

from torch.nn.utils.rnn import pad_sequence

from torch.utils.data import Dataset

from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("THUDM/chatglm-6b", trust_remote_code=True)

def get_train_data(data_path,tokenizer,max_len, max_src_len, prompt_text):

    max_tgt_len = max_len - max_src_len - 3

    all_data = []

    with open(data_path, "r", encoding="utf-8") as fh:

        for i, line in enumerate(fh):

            sample = json.loads(line.strip())

            src_tokens = tokenizer.tokenize(sample["text"])

            prompt_tokens = tokenizer.tokenize(prompt_text)

            if len(src_tokens) > max_src_len - len(prompt_tokens):

                src_tokens = src_tokens[:max_src_len - len(prompt_tokens)]

            tgt_tokens = tokenizer.tokenize("\n原因:"+sample["answer"])

            if len(tgt_tokens) > max_tgt_len:

                tgt_tokens = tgt_tokens[:max_tgt_len]

            tokens = prompt_tokens + src_tokens + ["[gMASK]", "<sop>"] + tgt_tokens + ["<eop>"]

            input_ids = tokenizer.convert_tokens_to_ids(tokens)

            context_length = input_ids.index(tokenizer.bos_token_id)

            mask_position = context_length - 1

            labels = [-100] * context_length + input_ids[mask_position + 1:]

            pad_len = max_len - len(input_ids)

            input_ids = input_ids + [tokenizer.pad_token_id] * pad_len

            labels = labels + [-100] * pad_len

            all_data.append(

                {"text": sample["text"], "answer": sample["answer"], "input_ids": input_ids, "labels": labels})

    return all_data

#服务PyTorch计算的数据输入类

class Seq2SeqDataSet(Dataset):

    """数据处理函数"""

    def __init__(self, all_data):

        self.all_data = all_data

    def __len__(self):

        return len(self.all_data)

    def __getitem__(self, item):

        instance = self.all_data[item]

        return instance

def coll_fn(batch):

    input_ids_list, labels_list = [], []

    for instance in batch:

        input_ids_list.append(torch.tensor(instance["input_ids"], dtype=torch.long))

        labels_list.append(torch.tensor(instance["labels"], dtype=torch.long))

    return {"input_ids": pad_sequence(input_ids_list, batch_first=True, padding_value=3),  #这里原来是20003, vocab改成了3

            "labels": pad_sequence(labels_list, batch_first=True, padding_value=3)}

if __name__ == '__main__':

    from transformers import AutoTokenizer

    tokenizer = AutoTokenizer.from_pretrained("THUDM/chatglm-6b", trust_remote_code=True)

    all_data = get_train_data("./data/spo_0.json", tokenizer, 768, 450, "你现在是一个信息抽取模型，请你帮我抽取出关系内容为\"性能故障\", \"部件故障\", \"组成\"和 \"检测工具\"的相关三元组，三元组内部用\"_\"连接，三元组之间用\\n分隔。文本：")

    train_dataset = Seq2SeqDataSet(all_data)

    instance = train_dataset.__getitem__(0)

    text, ans, input_ids, lab = instance

    print(len(instance["input_ids"]))

    print(len(instance["labels"]))

    from torch.utils.data import RandomSampler, DataLoader

    train_loader = DataLoader(train_dataset, batch_size=4, drop_last=True, num_workers=0)

训练阶段使用 Accelerate 统一设备与梯度同步。优化器选用 AdamW，学习率 2e-5，beta 0.9/0.999，eps 1e-5；学习率调度采用余弦退火，周期 2400 步，最低学习率 2e-6。准备完成后进入训练上下文，反向传播通过 accelerator.backward 完成。
[PRE5]python

from accelerate import Accelerator

accelerator = Accelerator()

optimizer = torch.optim.AdamW(model.parameters(), lr=2e-5, betas=(0.9, 0.999),

                              eps=1e-5)

lr_scheduler = torch.optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=2400,

                                                         eta_min=2e-6, last_epoch=-1)

model, optim, train_loader, lr_scheduler = accelerator.prepare(model, optimizer,

                                                                train_loader, lr_scheduler)

...

with train:

    accelerator.backward(loss)