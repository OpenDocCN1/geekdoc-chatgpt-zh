# PyTorch 自注意力核心实现逐行拆解

想把Transformer里的自注意力搬到自己的项目里，先看清三件事：把词向量投成Q/K/V、按头切分维度、再用掩码做软选择。下面这段代码就是最小可运行的骨架，先保存整体脉络，再结合后面的细节片段一起看。

self.key_layer = torch.nn.Linear(embedding_dim, hidden_dim)

        self.value_layer = torch.nn.Linear(embedding_dim, hidden_dim)

    def forward(self, embedding, mask):

        input_embedding = embedding

        query = self.query_layer(input_embedding)

        key = self.key_layer(input_embedding)

        value = self.value_layer(input_embedding)

        query_splited = self.split_tensor(query, self.n_head)

        key_splited = self.split_tensor(key, self.n_head)

        value_splited = self.split_tensor(value, self.n_head)

        key_splited = elt.Rearrange("b h l d -> b h d l")(key_splited)

        # 计算query与key之间的权重系数

        attention_prob = torch.matmul(query_splited, key_splited)

        # 使用softmax对权重系数进行归一化计算

        attention_prob += mask * -1e5  # 在自注意力权重的基础上加上掩码值

        attention_prob = torch.softmax(attention_prob, dim=-1)

        # 计算权重系数与value的值，从而获取注意力值

        attention_score = torch.matmul(attention_prob, value_splited)

        attention_score = elt.Rearrange("b h l d -> b l (h d)")(attention_score)

        return attention_score

    def split_tensor(self, tensor, h_head):

        embedding = elt.Rearrange("b l (h d) -> b l h d", h=h_head)(tensor)

        embedding = elt.Rearrange("b l h d -> b h l d", h=h_head)(embedding)

        return embedding

if __name__ == '__main__':

    embedding = torch.rand(size=(5, 16, 312))

    mask = torch.ones((5, 1, 16, 1))  # 注意设计mask的位置，长度是16

    Attention()(embedding, mask)

整体流程是：输入的嵌入先经三个线性层得到查询、键、值；随后把特征在头维度上拆开，键再转置成适合做点积的形状；查询与键做矩阵乘法得到原始相关性分数，掩码以很大的负数压制不合法位置，softmax后得到权重，权重再与值相乘得到加权表示，最后把多头拼接回原始维度。

词表查找与位置编码是注意力前置的常见组合，先把token转成密集向量，再叠加位置信息。

[PRE0]
import torch

word_embedding_table = torch.nn.Embedding(num_embeddings=encoder_vocab_size, embedding_dim=312)

encoder_embedding = word_embedding_table(inputs)
[PRE1]
class PositionalEncoding(torch.nn.Module):

    def __init__(self, d_model=312, dropout=0.05, max_len=80):

        """

        :param d_model: pe编码维度，一般与Word Embedding相同，方便相加

        :param dropout: dorp out

        :param max_len: 语料库中最长句子的长度，即Word Embedding中的L

        """

        super(PositionalEncoding, self).__init__()

        # 定义drop out

        self.dropout = torch.nn.Dropout(p=dropout)

        # 计算pe编码

        pe = torch.zeros(max_len, d_model)  # 建立空表，每行代表一个词的位置，每列代表一个编码位

        position = torch.arange(0, max_len).unsqueeze(1)  # 建个arrange表示词的位置，以便使用公式计算，size=(max_len,1)

        div_term = torch.exp(torch.arange(0, d_model, 2) *  # 计算公式中的10000**(2i/d_model)
                           -(math.log(10000.0) / d_model))

        pe[:, 0::2] = torch.sin(position * div_term)  # 计算偶数维度的pe值

        pe[:, 1::2] = torch.cos(position * div_term)  # 计算奇数维度的pe值

        pe = pe.unsqueeze(0)  # size=(1, L, d_model)，为了后续与word_embedding相加，意为batch维度下的操作相同

        self.register_buffer('pe', pe)  # pe值是不参加训练的

    def forward(self, x):

        # 输入的最终编码 = word_embedding + positional_embedding

        x = x + self.pe[:, :x.size(1)].clone().detach().requires_grad_(False)

        return self.dropout(x)  # size = [batch, L, d_model]
[PRE2]
W^Q = concat([q1, q2], axis = 0)
[PRE3]
latex
Attention(Query,Source) = \sum_{i=1}^{Lx} Similarity(Query, key_i) \times Value_i
[PRE4]
import torch

import math

import einops.layers.torch as elt

# word_embedding_table = torch.nn.Embedding(num_embeddings=encoder_vocab_size,embedding_dim=312)

# encoder_embedding = word_embedding_table(inputs)

vocab_size = 1024    #字符的种类

embedding_dim = 312

hidden_dim = 256

token = torch.ones(size=(5,80),dtype=int)

#创建一个输入Embedding值

input_embedding = torch.nn.Embedding(num_embeddings=vocab_size,embedding_dim=embedding_dim)(token)

#对输入的input_embedding进行修正，这里进行了简写

query = torch.nn.Linear(embedding_dim,hidden_dim)(input_embedding)
[PRE5]
python
key = torch.nn.Linear(embedding_dim,hidden_dim)(input_embedding)

value = torch.nn.Linear(embedding_dim,hidden_dim)(input_embedding)

key = elt.Rearrange("b l d -> b d l")(key)

#计算query与key之间的权重系数

attention_prob = torch.matmul(query,key)

#使用softmax对权重系数进行归一化计算