[PRE0]

代码说明：

- (1) 多头自注意力单元 MultiHeadSelfAttention：实现多头自注意力计算，输入输出的维度保持一致，主要负责刻画输入序列内部的依赖关联。
- (2) 残差加层归一化单元 ResidualNormLayer：先将原始输入与子层结果相加保留信息通路，再用 LayerNorm 对相加结果做归一化，稳定特征分布，避免深层训练时梯度消失或爆炸。
- (3) 前馈网络 FeedForward：由两层线性变换加 ReLU 激活构成，完成特征的非线性重塑，中间的 Dropout 用于抑制过拟合，提升泛化能力。
- (4) 编码器单层 TransformerEncoderLayer：把自注意力、残差归一化与前馈网络串联成完整编码单元，两个子层均通过 ResidualNormLayer 完成残差连接和归一化。

运行后会打印该编码器层的输出张量形状，并附带部分示例值。受篇幅限制，后续章节不再展示张量的具体元素，阅读时请以张量形状为重点。

[PRE1]

以上实现完整呈现了一个编码层的搭建思路，把多头注意力、前馈网络、残差结构和层归一化融合在一起。残差与归一化被封装成独立模块后，特征在层间传递更平稳，有助于深层网络的有效训练。

### 2.3 Transformer 编码解码原理解析

Transformer 的运行分为编码与解码两个阶段：编码器先把输入序列提炼成富含上下文的表示，解码器再依据该表示逐步生成输出序列，实现序列到序列的转换。

本节将结合示例说明编码器多层堆叠后信息如何流动，解析输入是如何被转化为全局特征表示并供解码使用。也将通过具体实现演示解码器的自回归生成过程，展示生成时如何持续引用编码器的输出。

#### 2.3.1 编码器堆叠与信息流动实现

在 Transformer 中，编码器采用多层堆叠的方式逐层提炼高阶表征。每层接收来自前一层的输出，借助多头注意力和前馈网络加强序列间的依赖信息与特征表达，同时利用残差连接和层归一化保障梯度回传顺畅，使深层结构仍能保持信息的稳定流动。

下面给出构建多层编码器堆叠的代码示例。

[PRE2]

def forward(self, x, sublayer_output):

    x = x + self.dropout(sublayer_output)

    return self.layer_norm(x)

## 定义单层编码器层

class TransformerEncoderLayer(nn.Module):

    def __init__(self, d_model, num_heads, dim_feedforward, dropout=0.1):

        super(TransformerEncoderLayer, self).__init__()

        self.self_attn = MultiHeadSelfAttention(d_model, num_heads)

        self.feed_forward = FeedForward(d_model, dim_feedforward, dropout)

        self.residual_norm1 = ResidualNormLayer(d_model, dropout)

        self.residual_norm2 = ResidualNormLayer(d_model, dropout)

    def forward(self, src):

        src2 = self.self_attn(src)

        src = self.residual_norm1(src, src2)

        src2 = self.feed_forward(src)

        src = self.residual_norm2(src, src2)

        return src

## 定义多层编码器堆叠

class TransformerEncoder(nn.Module):

    def __init__(self, d_model, num_heads, num_layers,

                 dim_feedforward, dropout=0.1):

        super(TransformerEncoder, self).__init__()

        self.layers = nn.ModuleList([TransformerEncoderLayer(d_model,

            num_heads, dim_feedforward, dropout) for _ in range(num_layers)])

    def forward(self, src):

        for layer in self.layers:

            src = layer(src)

        return src

# 模型参数

d_model = 512

num_heads = 8

num_layers = 6

dim_feedforward = 2048

dropout = 0.1

# 初始化多层编码器

encoder = TransformerEncoder(d_model, num_heads, num_layers, dim_feedforward, dropout)

# 输入张量：批次大小为16，序列长度为10，嵌入维度为512

input_data = torch.rand(16, 10, d_model)

# 前向传播

output = encoder(input_data)

# 输出结果

print("多层编码器输出形状:", output.shape)