[PRE0]

代码要点解析：

1. 多卡并行：模型先用nn.DataParallel包裹，从而能在多张GPU上同时计算。torch.cuda.device_count()可查询可用显卡数量，当检测到多GPU时，DataParallel会自动拆分输入并分发任务。
2. 梯度累积：通过accumulation_steps设定累积轮数。每次只喂一个小批次计算梯度，累积到指定步数后再统一更新，这样可以在显存受限的情况下模拟大批量训练。
3. 混合精度训练：依赖torch.cuda.amp模块的autocast和GradScaler。autocast在前向时自动选择合适的数据精度，GradScaler动态调整梯度尺度，防止溢出和消失。
4. 分批处理：输入被按累积步长切分为子批次，每次前向得到子批次损失并执行反向。达到accumulation_steps后调用scaler.step(optimizer)更新参数，随后optimizer.zero_grad()清空梯度。

运行后脚本会先打印当前设备信息及多GPU状态，随后输出模型的预测结果。展示格式如下：

[PRE1]

该示例完整呈现了多卡并行配合梯度累积与混合精度的训练管线。累积梯度能明显降低显存占用，混合精度在不牺牲精度的前提下提升计算速度。两者结合多设备并行，即使在较大模型与批次下，训练依然保持稳定且高效。

### 1.3 快速入门：PyTorch手搓简易Transformer

Transformer的骨架由编码器和解码器构成，内部均以自注意力和前馈网络为基本单元，多层叠加形成深度结构。本节基于PyTorch，演示如何迅速搭建一个精简版Transformer。重点在编码器侧呈现层归一化、多头自注意力和前馈网络的实现，并附带完整训练演示。

解码器则在编码器架构上加入掩码，保证生成过程具备自回归特性。通过可运行的代码，读者能够从零搭起Transformer，并经测试确认编解码器是否工作正常，从而快速掌握Transformer的实用方法。

#### 1.3.1 Transformer编码器从零搭建与训练演示

编码器的核心要素包含多头自注意力、前馈网络、层归一化以及残差连接。下面的实现用PyTorch从零拼装一层完整的Transformer编码器，并给出基础训练流程。配套说明会逐块解释各模块的运作细节，便于抓住要点。

import torch

import torch.nn as nn

import torch.optim as optim

import torch.nn.functional as F

import math

# 定义多头注意力机制

class MultiHeadSelfAttention(nn.Module):

    def __init__(self, d_model, num_heads):

        super(MultiHeadSelfAttention, self).__init__()

        assert d_model % num_heads == 0, "d_model必须能被num_heads整除"

        self.d_head = d_model // num_heads           # 每个注意力头的维度

        self.num_heads = num_heads

        # 查询、键和值的线性变换层

        self.query = nn.Linear(d_model, d_model)

        self.key = nn.Linear(d_model, d_model)

        self.value = nn.Linear(d_model, d_model)

        # 输出层

        self.out = nn.Linear(d_model, d_model)

    def forward(self, x):

        # 将查询、键和值通过线性变换并分头

        batch_size, seq_len, d_model = x.size()

        Q = self.query(x).view(batch_size, seq_len, self.num_heads,

                             self.d_head).transpose(1, 2)

        K = self.key(x).view(batch_size, seq_len, self.num_heads,

                           self.d_head).transpose(1, 2)

        V = self.value(x).view(batch_size, seq_len, self.num_heads,

                             self.d_head).transpose(1, 2)

        # 计算注意力得分

        scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.d_head)

        attention_weights = F.softmax(scores, dim=-1)

        # 加权求和生成注意力输出

        attention_output = torch.matmul(attention_weights, V).transpose(

            1, 2).contiguous().view(batch_size, seq_len, d_model)

        return self.out(attention_output)

# 定义前馈神经网络

class FeedForward(nn.Module):

    def __init__(self, d_model, dim_feedforward, dropout=0.1):

        super(FeedForward, self).__init__()

        self.linear1 = nn.Linear(d_model, dim_feedforward)

        self.dropout = nn.Dropout(dropout)

        self.linear2 = nn.Linear(dim_feedforward, d_model)

    def forward(self, x):

        return self.linear2(self.dropout(F.relu(self.linear1(x))))

# 定义Transformer编码器层

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

        # 多头注意力+残差连接+层归一化

        src2 = self.self_attn(src)

        src = src + self.dropout1(src2)

        src = self.norm1(src)

        # 前馈神经网络+残差连接+层归一化

        src2 = self.feed_forward(src)

        src = src + self.dropout2(src2)

        src = self.norm2(src)

        return src

# 定义Transformer编码器

class TransformerEncoder(nn.Module):

    def __init__(self, d_model, num_heads, num_layers, dim_feedforward,

                 dropout=0.1):

        super(TransformerEncoder, self).__init__()

        self.layers = nn.ModuleList([TransformerEncoderLayer(d_model,

            num_heads, dim_feedforward, dropout) for _ in range(num_layers)])

    def forward(self, src):

        for layer in self.layers:

            src = layer(src)

        return src

# 模型参数定义

d_model = 512

num_heads = 8

num_layers = 6

dim_feedforward = 2048

dropout = 0.1

# 实例化编码器

encoder = TransformerEncoder(d_model, num_heads, num_layers,

                        dim_feedforward, dropout).to(

                            'cuda' if torch.cuda.is_available() else 'cpu')

# 定义训练输入和目标

input_data = torch.rand(32, 10, d_model).to(

                        'cuda' if torch.cuda.is_available() else 'cpu')

                                                    # 批次大小32, 序列长度10, 嵌入维度d_model

target_data = torch.rand(32, 10, d_model).to(

                        'cuda' if torch.cuda.is_available() else 'cpu')

# 定义损失函数和优化器

criterion = nn.MSELoss()

optimizer = optim.Adam(encoder.parameters(), lr=0.0001)

# 训练流程

encoder.train()

num_epochs = 5

for epoch in range(num_epochs):

    optimizer.zero_grad()                          # 清空梯度

    output = encoder(input_data)                     # 前向传播

    loss = criterion(output, target_data)            # 计算损失

    loss.backward()                                # 反向传播

    optimizer.step()                               # 更新参数

    print(f"Epoch {epoch+1}/{num_epochs}, Loss: {loss.item():.4f}")

Transformer编码器经典训练流程如图1-2所示。

[PRE2]

图1-2 Transformer编码器经典训练流程

代码要点说明：

（1）多头自注意力层（MultiHeadSelfAttention）：
- 内部设有Q、K、V三套线性投影，把输入映射到多头空间。
- Q与K做点积并除以根号头维度，经Softmax得到注意力分布。
- 用分布加权V再拼接回原维度，最后经输出线性层返回。

（2）前馈神经网络层（FeedForward）：
- 结构为线性-ReLU-线性，中间使用dropout进行正则化以抑制过拟合。

（3）Transformer编码器层（TransformerEncoderLayer）：
- 先完成自注意力后接残差与层归一化，形成第一个子层。
- 再接入前馈网络，同样配合残差与层归一化，保证梯度与信息稳定流动。