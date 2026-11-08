Transformer编码器里词向量与位置编码的实操写法

做编码器第一步是把离散的词ID变成连续向量。借助 PyTorch 的 Embedding 层建一张查找表，维度与后续模型保持一致，这里用 312 维。拿到输入 ID 后直接查表，就得到 batch 维的词向量表示。

[PRE0]python

import torch

word_embedding_table = torch.nn.Embedding(num_embeddings=encoder_vocab_size, embedding_dim=312)

encoder_embedding = word_embedding_table(inputs)

有了词向量还不够，Transformer 没有循环结构，需要显式注入位置信息。下面的自定义模块按经典正弦余弦公式预先生成位置编码，维度默认与词向量相同以便逐元素相加，并加入可控的 dropout。

类初始化时接受三个超参：模型维度 d_model 默认 312、dropout 默认 0.05、最大序列长度 max_len 默认 80。位置编码表先用全零矩阵创建，行对应位置、列对应维度。位置索引展开后与按公式衰减的除数相乘，再对偶数位取 sin、奇数位取 cos 填充。最终在 batch 维上做 unsqueeze，方便广播相加，并用 register_buffer 注册为不参与梯度的缓冲。forward 时截取到实际长度、强制不求导后与词向量相加，再经 dropout 输出。

[PRE1]python

class PositionalEncoding(torch.nn.Module):

    def __init__(self, d_model = 312, dropout = 0.05, max_len=80):

        """

        :param d_model: pe编码维度，一般与Word Embedding相同，方便相加

        :param dropout: dorp out

        :param max_len: 语料库中最长句子的长度，即Word Embedding中的L

        """

        super(PositionalEncoding, self).__init__()

        # 定义drop out

        self.dropout = torch.nn.Dropout(p=dropout)

        # 计算pe编码

        pe=torch.zeros(max_len, d_model) # 建立空表，每行代表一个词的位置，每列代表一个编码位

        position = torch.arange(0, max_len).unsqueeze(1) # 建个arrange表示词的位置，以便使用公式计算，size=(max_len,1)

        div_term=torch.exp(torch.arange(0, d_model, 2) * #计算公式中的10000**(2i/d_model)

                           -(math.log(10000.0) / d_model))

        pe[:, 0::2] = torch.sin(position * div_term) # 计算偶数维度的pe值

        pe[:, 1::2] = torch.cos(position * div_term) # 计算奇数维度的pe值

        pe=pe.unsqueeze(0) # size=(1, L, d_model)，为了后续与word_embedding相加，意为batch维度下的操作相同

        self.register_buffer('pe', pe) # pe值是不参加训练的

    def forward(self, x):

        # 输入的最终编码 = word_embedding + positional_embedding

        x = x + self.pe[:, :x.size(1)].clone().detach().requires_grad_(False)

        return self.dropout(x) # size = [batch, L, d_model]

把词嵌入和位置编码串起来，就得到带顺序信息的最终输入，形状为 batch、序列长、312 维，可直接喂给后续的自注意力层。

[PRE2]