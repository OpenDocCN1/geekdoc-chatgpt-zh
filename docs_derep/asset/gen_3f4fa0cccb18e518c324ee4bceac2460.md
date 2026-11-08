# 手搓一个可运行的 Transformer 编码解码器

[PRE0]

scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.d_head)

attention_weights = torch.nn.functional.softmax(scores, dim=-1)

attention_output = torch.matmul(attention_weights, V).transpose(1, 2).contiguous().view(batch_size, seq_len, d_model)

return self.out(attention_output)

# 前馈网络实现

class FeedForward(nn.Module):

    def __init__(self, d_model, dim_feedforward, dropout=0.1):

        super(FeedForward, self).__init__()

        self.linear1 = nn.Linear(d_model, dim_feedforward)

        self.dropout = nn.Dropout(dropout)

        self.linear2 = nn.Linear(dim_feedforward, d_model)

    def forward(self, x):

        return self.linear2(self.dropout(

            torch.nn.functional.relu(self.linear1(x))))

# 编码器层构造

class TransformerEncoderLayer(nn.Module):

    def __init__(self, d_model, num_heads, dim_feedforward, dropout=0.1):

        super(TransformerEncoderLayer, self).__init__()

        self.self_attn = MultiHeadSelfAttention(d_model, num_heads)

        self.feed_forward = FeedForward(d_model, dim_feedforward, dropout)

        self.norm1 = nn.LayerNorm(d_model)

        self.norm2 = nn.LayerNorm(d_model)

        self.dropout1 = nn.Dropout(dropout)

        self.dropout2 = nn.Dropout(dropout)

    def forward(self, src):

        src2 = self.self_attn(src)

        src = src + self.dropout1(src2)

        src = self.norm1(src)

        src2 = self.feed_forward(src)

        src = src + self.dropout2(src2)

        src = self.norm2(src)

        return src

# 解码器层构造

class TransformerDecoderLayer(nn.Module):

    def __init__(self, d_model, num_heads, dim_feedforward, dropout=0.1):

        super(TransformerDecoderLayer, self).__init__()

        self.self_attn = MultiHeadSelfAttention(d_model, num_heads)

        self.encoder_attn = MultiHeadSelfAttention(d_model, num_heads)

        self.feed_forward = FeedForward(d_model, dim_feedforward, dropout)

        self.norm1 = nn.LayerNorm(d_model)

        self.norm2 = nn.LayerNorm(d_model)

        self.norm3 = nn.LayerNorm(d_model)

        self.dropout1 = nn.Dropout(dropout)

        self.dropout2 = nn.Dropout(dropout)

        self.dropout3 = nn.Dropout(dropout)

    def forward(self, tgt, memory):

        tgt2 = self.self_attn(tgt)

        tgt = tgt + self.dropout1(tgt2)

        tgt = self.norm1(tgt)

        tgt2 = self.encoder_attn(tgt, memory)

        tgt = tgt + self.dropout2(tgt2)

        tgt = self.norm2(tgt)

        tgt2 = self.feed_forward(tgt)

        tgt = tgt + self.dropout3(tgt2)

        tgt = self.norm3(tgt)

        return tgt

# 整体模型定义

class Transformer(nn.Module):

    def __init__(self, d_model, num_heads, num_layers, dim_feedforward,

                 dropout=0.1):

        super(Transformer, self).__init__()

        self.encoder = nn.ModuleList([TransformerEncoderLayer(d_model,

            num_heads, dim_feedforward, dropout) for _ in range(num_layers)])

        self.decoder = nn.ModuleList([TransformerDecoderLayer(d_model,

            num_heads, dim_feedforward, dropout) for _ in range(num_layers)])

        self.pos_encoder = PositionalEncoding(d_model)

        self.pos_decoder = PositionalEncoding(d_model)

    def forward(self, src, tgt):

        src = self.pos_encoder(src)

        tgt = self.pos_decoder(tgt)

        memory = src

        for layer in self.encoder:

            memory = layer(memory)

        output = tgt

        for layer in self.decoder:

            output = layer(output, memory)

        return output

# 模型实测

d_model = 512

num_heads = 8

num_layers = 6

dim_feedforward = 2048

dropout = 0.1

model = Transformer(d_model, num_heads, num_layers, dim_feedforward,

                  dropout).to('cuda' if torch.cuda.is_available() else 'cpu')

# 模拟输入和目标张量

src = torch.rand((20, 32, d_model)).to('cuda' if torch.cuda.is_available() else 'cpu')

tgt = torch.rand((20, 32, d_model)).to('cuda' if torch.cuda.is_available() else 'cpu')

# 前向传播测试

output = model(src, tgt)

print("Transformer模型输出形状:", output.shape)

print("Transformer模型输出示例:", output[0, 0, :10])

代码要点解读：

(1) 位置编码 PositionalEncoding：通过正弦余弦函数生成位置信号，并叠加到输入张量上。
(2) 多头自注意力 MultiHeadSelfAttention：实现多头自注意力机制，用来捕捉序列内不同位置间的关联。
(3) 前馈神经网络 FeedForward：包含 ReLU 激活与 Dropout 的两层线性结构，提升模型的非线性表达能力。
(4) 编码器层 TransformerEncoderLayer：由多头自注意力和前馈网络组成，配合残差与层归一化处理特征。
(5) 解码器层 TransformerDecoderLayer：串联自注意力、编码器-解码器注意力和前馈网络，使用残差连接完成复杂序列建模。
(6) 完整 Transformer 模型：将多层编码器与解码器堆叠，并接入位置编码，形成端到端的架构。

运行后会打印输出张量的形状以及前若干个数值，用以验证前向流程是否正常：

[PRE1]

整段实现从输入到输出形成了一个完整的编码-解码流水线，位置编码提供顺序信息，多层注意力完成深层建模，最终证明模型可以稳定接收序列并生成对应的输出。

#### 2.1.2 多头注意力与前馈层的协作模式

在 Transformer 内部，编码器和解码器都由多头注意力与前馈网络构成，形成层次递进的深度结构。多头注意力负责从不同维度挖掘词间依赖，前馈网络则对提取到的特征做非线性变换，两者配合让模型具备更强的表示能力，多头注意力的经典结构如图2-2所示。

多头注意力可以理解为同一层内并行的多视角分析。它把输入拆成若干头，分别计算注意力权重，再把结果融合起来，这样模型就能同时关注句子的不同侧面。

举个例子，句子“小明今天在公园踢足球”，模型需要理清“踢足球”和“小明”的关联。

[PRE2]

图2-2 经典的多头注意力机制

单头注意力像一位观察者，只能盯住一对关系，比如“足球”与“踢”。多头注意力则像一组观察者，各自负责不同视角：

- 第一位关注“足球”和“踢”的动作与目标关系。
- 第二位关注“小明”和“踢”的主语与动作关系。
- 第三位关注“今天”和“公园”的时间与地点关系。

下面给出的代码片段正是展示注意力与前馈网络如何在层内配合，并借助残差连接和层归一化构建模块化的编码器单元。