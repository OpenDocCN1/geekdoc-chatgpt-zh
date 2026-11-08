# 拼音到汉字：用轻量 Transformer 编码器做序列映射

想把拼音序列还原成汉字，其实就是一个序列到序列的映射问题。下面这份实现用一个纯编码器结构的 Transformer 把拼音 token 映射到汉字词表，结构紧凑、可读性高，适合做中文输入、拼音纠错等自媒体演示项目。

整体思路是先做嵌入加位置编码，再经过多轮自注意力与前馈变换，最后接一个线性分类头预测每个位置的汉字。训练时采用标准的交叉熵，只计算有效位置的损失，并用 Adam 小步学习率慢慢收敛。

网络骨干由四块组成：前馈网络、带多头的自注意力、sin/cos 位置编码、以及把它们堆叠起来的编码器。

前馈部分采用 312 维输入，展宽 4 倍到 1248 再压缩回 312，中间两次 ReLU，最后做 LayerNorm 平稳输出。注意力使用 6 个头，Q/K/V 都是 312 维线性投影，通过 einops 做头拆分与重排，先把 key 转成 b h d l 再与 query 做点积，mask 用 -1e5 遮蔽 padding 位置，softmax 归一化后加权 value，最后把多头拼接回 b l h d。

位置编码采用经典的正弦余弦方案，最大长度 80，dropout 0.05。编码表在构造时就固定为 buffer，不参与梯度更新，前向时直接加到词嵌入上。

编码器在初始化时创建拼音词嵌入表和位置编码，默认 3 层堆叠。每层先做自注意力，再 0.1 dropout，再过前馈。mask 由输入是否等于 0 生成，形状扩展为 b 1 1 l，方便广播。

[PRE0]python

import torch

import math

import einops.layers.torch as elt

class FeedForWard(torch.nn.Module):

    def __init__(self,embedding_dim = 312,scale = 4):

        super().__init__()

        self.linear1 = torch.nn.Linear(embedding_dim,embedding_dim*scale)

        self.relu_1 = torch.nn.ReLU()

        self.linear2 = torch.nn.Linear(embedding_dim*scale,embedding_dim)

        self.relu_2 = torch.nn.ReLU()

        self.layer_norm = torch.nn.LayerNorm(normalized_shape=embedding_dim)

    def forward(self,tensor):

        embedding = self.linear1(tensor)

        embedding = self.relu_1(embedding)

        embedding = self.linear2(embedding)

        embedding = self.relu_2(embedding)

        embedding = self.layer_norm(embedding)

        return embedding

class Attention(torch.nn.Module):

    def __init__(self, embedding_dim = 312, hidden_dim = 312, n_head = 6):

        super().__init__()

        self.n_head = n_head

        self.query_layer = torch.nn.Linear(embedding_dim, hidden_dim)

        self.key_layer = torch.nn.Linear(embedding_dim, hidden_dim)

        self.value_layer = torch.nn.Linear(embedding_dim, hidden_dim)

    def forward(self, embedding, mask):

        input_embedding = embedding

        query = self.query_layer(input_embedding)

        key = self.key_layer(input_embedding)

        value = self.value_layer(input_embedding)

        query_splited = self.splite_tensor(query, self.n_head)

        key_splited = self.splite_tensor(key, self.n_head)

        value_splited = self.splite_tensor(value, self.n_head)

        key_splited = elt.Rearrange("b h l d -> b h d l")(key_splited)

        # 计算query与key之间的权重系数

        attention_prob = torch.matmul(query_splited, key_splited)

        # 使用softmax对权重系数进行归一化计算

        attention_prob += mask * -1e5  # 在自注意力权重的基础上加上掩码值

        attention_prob = torch.softmax(attention_prob, dim=-1)

        # 计算权重系数与value的值，从而获取注意力值

        attention_score = torch.matmul(attention_prob, value_splited)

        attention_score = elt.Rearrange("b h l d -> b l (h d)")(attention_score)

        return (attention_score)

    def splite_tensor(self, tensor, h_head):

        embedding = elt.Rearrange("b l (h d) -> b l h d", h = h_head)(tensor)

        embedding = elt.Rearrange("b l h d -> b h l d", h=h_head)(embedding)

        return embedding

class PositionalEncoding(torch.nn.Module):

    def __init__(self, d_model = 312, dropout = 0.05, max_len=80):

        """

        :param d_model: pe编码维度，一般与Word Embedding相同，方便相加

        :param dropout: dropout

        :param max_len: 语料库中最长句子的长度，即Word Embedding中的L

        """

        super(PositionalEncoding, self).__init__()

        # 定义drop out

        self.dropout = torch.nn.Dropout(p=dropout)

        # 计算pe编码

        pe = torch.zeros(max_len, d_model) #建立空表，每行代表一个词的位置，每列代表一个编码位

        position = torch.arange(0, max_len).unsqueeze(1) # 建个arrange表示词的位置，以便使用公式计算，size=(max_len,1)

        div_term = torch.exp(torch.arange(0, d_model, 2) * # 计算公式中10000**(2i/d_model)

-(math.log(10000.0) / d_model))

        pe[:, 0::2] = torch.sin(position * div_term) # 计算偶数维度的pe值

        pe[:, 1::2] = torch.cos(position * div_term) # 计算奇数维度的pe值

        pe=pe.unsqueeze(0) # size=(1, L, d_model)，为了后续与word_embedding相加，意为batch维度下的操作相同

        self.register_buffer('pe', pe) # pe值是不参加训练的

    def forward(self, x):

        # 输入的最终编码 = word_embedding + positional_embedding

        x = x + self.pe[:, :x.size(1)].clone().detach().requires_grad_(False)

        return self.dropout(x) # size = [batch, L, d_model]

class Encoder(torch.nn.Module):

    def __init__(self,vocab_size = 1024,max_length = 80,embedding_size = 312,n_head = 6,scale = 4,n_layer = 3):

        super().__init__()

        self.n_layer = n_layer

        self.embedding_table = torch.nn.Embedding(num_embeddings=vocab_size,embedding_dim=embedding_size)

        self.position_embedding = PositionalEncoding(max_len=max_length)

        self.attention = Attention(embedding_size,embedding_size,n_head)

        self.feedward = FeedForWard()

    def forward(self,token_inputs):

        token = token_inputs

        mask = self.create_mask(token)

        embedding = self.embedding_table(token)

        embedding = self.position_embedding(embedding)

        for _ in range(self.n_layer):

            embedding = self.attention(embedding,mask)

            embedding = torch.nn.Dropout(0.1)(embedding)

            embedding = self.feedward(embedding)

        return embedding

    def create_mask(self,seq):

        mask = torch.not_equal(seq, 0).float()

        mask = torch.unsqueeze(mask, dim=-1)

        mask = torch.unsqueeze(mask, dim=1)

        return mask

训练部分先加载拼音与汉字的数据集，batch 32，跑 21 个轮次。模型封装为 Sequential：编码器 + Dropout 0.1 + 线性映射到汉字词表。词表规模为拼音 1154，汉字 4462，最大序列长度设为 64。

训练循环里把拼音 id 转成张量送入模型得到预测 logits，汉字标签做 uint8 过滤，只保留大于 0 的有效位置计算 CrossEntropy。优化器用 Adam，学习率 3e-5，并开启 torch.compile 加速。每 10 个 epoch 保存一次网络与优化器状态。

[PRE1]python

pinyin_vocab,hanzi_vocab,pinyin_tokens_ids,hanzi_tokens_ids = get_data.get_dataset()

    batch_size = 32

    train_length = len(pinyin_tokens_ids)

    for epoch in range(21):

        train_num = train_length // batch_size

        train_loss, train_correct = [], []

        for i in tqdm(range((train_num))):

            ...

完整可运行的脚本如下，包含设备迁移、数据切分、有效位置掩码与定期保存。

[PRE2]python

import numpy as np

import torch

import attention_model

import get_data

max_length = 64

from tqdm import tqdm

char_vocab_size = 4462

pinyin_vocab_size = 1154

def get_model(embedding_dim = 312):

    model = torch.nn.Sequential(

        attention_model.Encoder(pinyin_vocab_size,max_length=max_length),

        torch.nn.Dropout(0.1),

        torch.nn.Linear(embedding_dim,char_vocab_size)

    )

    return model

device = "cuda"

model = get_model().to(device)

model = torch.compile(model)

optimizer = torch.optim.Adam(model.parameters(), lr=3e-5)

loss_func = torch.nn.CrossEntropyLoss()

pinyin_vocab,hanzi_vocab,pinyin_tokens_ids,hanzi_tokens_ids = get_data.get_dataset()

batch_size = 32

train_length = len(pinyin_tokens_ids)

for epoch in range(21):

    train_num = train_length // batch_size

    train_loss, train_correct = [], []

    for i in tqdm(range((train_num))):

        model.zero_grad()

        start = i * batch_size

        end = (i + 1) * batch_size

        batch_input_ids = torch.tensor(pinyin_tokens_ids[start:end]).int().to(device)

        batch_labels = torch.tensor(hanzi_tokens_ids[start:end]).to(device)

        pred = model(batch_input_ids)

        batch_labels = batch_labels.to(torch.uint8)

        active_loss = batch_labels.gt(0).view(-1) == 1

        loss = loss_func(pred.view(-1, char_vocab_size)[active_loss],

batch_labels.view(-1)[active_loss])

        optimizer.zero_grad()

        loss.backward()

        optimizer.step()

    if (epoch +1) %10 == 0:

        state = {"net":model.state_dict(), "optimizer":optimizer.state_dict(),

"epoch":epoch}

        torch.save(state, "./saver/modelpara.pt")