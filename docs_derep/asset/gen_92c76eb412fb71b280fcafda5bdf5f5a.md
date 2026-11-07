# 多头拼接与线性投影

output = output.transpose（1, 2）.contiguous（）.view（batch_size, -1, self.embed_dim）

# 末尾线性映射

output = self.fc_out（output） #输出形式仍为[batch_size, seq_len, embed_dim]

return output

**2.2.6 前馈神经网络模块**

FFN 是一种在深度学习里常见的基础单元，数据在其中只做单向推进，从入口经若干隐层到出口，没有任何回流回路。放到 Transformer 这类现代架构里，它是不可或缺的子结构。

在 Transformer 内部，FFN 紧跟自注意力之后出现。结构上就是两段线性变换，中间夹一层 ReLU。整个操作逐位置独立执行，序列长度保持不变，但能显著提升单点信息的表达力。可以理解为给每个位置多一次非线性重塑，在保留位置顺序的前提下增强语义刻画。

FFN 的核心价值体现在以下几个方面。

1）特征映射与非线性。靠全连接配合激活函数把低维输入拉到高维空间，能挖掘出原始数据里不易察觉的复杂模式，从而获得更丰富的表征。
2）表达能力提升。层层堆叠后模型可以变得很深，学习多层次抽象特征，对图像识别、文本理解等复杂任务尤为关键。
3）局部提取与全局融合。FFN 能先捕捉局部细节，再把不同隐层的信号汇总，为后续分类或回归提供更完整的依据。
4）训练更稳、效果更好。合理的设计有助于梯度下降更顺畅，降低陷入局部最优的概率，也能在一定程度上防止过拟合。

PyTorch 实现示例：

Python

import torch
import torch.nn as nn
class FeedForward（nn.Module）:
 def __init__（self, embed_dim, ff_dim）:
 super（FeedForward, self）.__init__（）
 #定义第一个全连接层，将输入维度从 embed_dim 映射到 ff_dim（前馈神经网络的隐藏层维度）
 self.fc1 = nn.Linear（embed_dim, ff_dim）
 #输入形式为[任意, embed_dim]，输出形式为[任意, ff_dim]
 #定义第二个全连接层，将维度从 ff_dim 映射回 embed_dim
 self.fc2 = nn.Linear（ff_dim, embed_dim）
 #输入形式为[任意, ff_dim]，输出形式为[任意, embed_dim]
 #定义 ReLU 激活函数
 self.relu = nn.ReLU（） #将输入张量应用于 ReLU 激活函数，保持形式不变
 def forward（self, x）:
 #x 通常是来自上一层的输出，形式为[batch_size, seq_len, embed_dim]
 #第一个线性变换，将 embed_dim 维的特征映射到 ff_dim 维
 fc1_output = self.fc1（x） # 形式变为[batch_size, seq_len, ff_dim]
 #应用 ReLU 激活函数，增强模型的非线性表达能力，形式保持不变
 relu_output = self.relu（fc1_output） #形式为[batch_size, seq_len, ff_dim]
 #第二个线性变换，将 ff_dim 维的特征映射回 embed_dim 维，以便与后续层兼容
 output = self.fc2（relu_output） # 形式变回[batch_size, seq_len, embed_dim]
 return output #最终输出形式为[batch_size, seq_len, embed_dim]

代码要点说明。

（1）构造阶段
● self.fc1 把输入通道从 embed_dim 升到 ff_dim，所谓“任意”指批量、长度等可变维度。
● self.fc2 把 ff_dim 再压回 embed_dim，保证输出维度与上一层对齐。
● self.relu 提供非线性，提升表示能力。

（2）前向阶段
输入 x 一般是 [batch_size, seq_len, embed_dim]。先经 fc1 变成 [batch_size, seq_len, ff_dim]，再过 ReLU 保持形状，最后由 fc2 回到 [batch_size, seq_len, embed_dim]。入出形状一致，保证模块可灵活串联。

**2.2.7 残差连接和层归一化模块**

**1.残差连接**

残差连接又称跳跃连接，是加深网络的同时维持有序信息流的手段。它把原始输入直接加到若干层变换的结果上，形成捷径。

残差连接数学原理如下:**[图][2][-][5][残][差][连][接][结][构]**

[PRE0]

***y***=*F*（***x***）+***x***

残差连接结构如图 2-5 所示。

如图 2-5 所示，核心思想是把模块输入 x 原封不动地叠加到模块输出上。其带来的好处有：

● 缓解梯度消失：反向传播时多了一条直达前层的通路，梯度不易衰减。图中 Identity 旁的曲线即代表这条通路，信息无损回传。
● 收敛更快：网络更容易学到恒等映射，训练步调更顺。
● 支持更深结构：上述两点共同让超深模型成为可能，有助于捕捉更复杂的规律。

在 Transformer 里，残差连接通常布置在注意力层和前馈层之后，让原始信息与新特征融合后一起传递。

**2.层归一化**

层归一化是一种稳定训练的正则手段，目标是减小内部协变量偏移。它对每个样本的每个层做归一化，不依赖同批其他样本。

● 训练更稳：降低层间分布漂移，加快收敛，对变长序列任务友好。
● 不受批量限制：不像批归一化依赖批量统计，层归一化在小批量或单样本在线场景下同样可靠。
● 泛化增强：有助于学到更通用的特征，降低过拟合风险。

以 x 为输入，x[i] 为切片值，归一化过程如下。

首先计算样本均值：

[PRE1]

然后计算样本方差：

[PRE2]

最后根据样本均值和方差进行归一化后的样本：

[PRE3]

● *μ*是特征向量的均值。
● *σ*²是特征向量的方差。
● [PRE4]是归一化后的特征值。
● [PRE5]是一个很小的常数，用于避免分母为零。

本质上是减均值除方差，统一量纲，便于不同样本比较。

为何 NLP 常用层归一化而 CV 常用批归一化？

（1）NLP 用层归一化的原因
假设激活形式为[*B*，*L*，*H*]，此时输入层归一化的 [PRE6]的形式为[*H*]，也就是在进行归一化时***x***转换为了*B***L* 个形式为 [PRE7]样本。NLP 的特点是：
1）序列长度可变，批归一化受长度限制，层归一化按样本独立处理更灵活。
2）样本差异大，独立归一化更贴合各句特性。
3）批量常小，层归一化不受批量大小影响更稳定。
4）位置信息重要，层归一化保留序列顺序。

（2）CV 用批归一化的原因
假设激活形式为[*B*，*H*，*W*，*C*],此时输入批归一化的 [PRE8]的形式为[*B*，*H*，*W*],也就是在进行归一化时***x***转换为了*C*个[*B*,*H*,*W*]形式的 [PRE9]样本。批归一化的优势是：
1）空间统计共享，同一通道不同位置统计特性相近，全批统计更有效。
2）批量通常大且稳定，估计的均值方差更可靠。
3）对平移旋转等几何变化更鲁棒。

批归一化和层归一化的比较如表 2-1 所示。**[表][2][-][1][批][归][一][化][和][层][归][一][化][的][比][较]**

[PRE10]

图 2-6 展示了层归一化和批归一化的差别。**[图][2][-][6][层][归][一][化][和][批][归][一][化][的][差][别]**

[PRE11]

从图 2-6 可知 CV 中特征矩阵为[*B*,*H*,*W*,*C* ]，批归一化按通道归一，单样本视作[*B*,*H*,*W* ]，适合跨通道对比。NLP 中特征矩阵为[*B*,*S*,*H* ]，层归一化按 Token 粒度归一，单样本为[*D*]，共*B***S*个，更贴合 Token 级对比需求。

**3.残差连接与层归一化代码**

Python

import torch
import torch.nn as nn
class TransformerBlock（nn.Module）:
 def __init__（self, embed_dim, num_heads, ff_dim, dropout=0.1）:
 super（TransformerBlock, self）.__init__（）
 #多头注意力模块
 self.attention = MultiHeadAttention（embed_dim, num_heads）
 #输入和输出形式为[batch_size, seq_len, embed_dim]
 #层归一化模块
 self.norm1 = nn.LayerNorm（embed_dim）#归一化维度为 embed_dim，保持输入和输出形式不变
 self.norm2 = nn.LayerNorm（embed_dim）# 同上
 #前馈神经网络模块
 self.feed_forward = FeedForward（embed_dim, ff_dim）
 #输入和输出形式为[batch_size, seq_len, embed_dim]
 #Dropout 层，用于防止过拟合
 self.dropout = nn.Dropout（dropout）
 def forward（self, x, mask=None）:
 """
 :param x: 输入张量，形式为[batch_size, seq_len, embed_dim]
 :param mask:注意力掩码，形式可变，通常为[batch_size, 1, seq_len, seq_len]
 :return:输出张量，形式为[batch_size, seq_len, embed_dim]
 """
 #残差连接和层归一化
 #执行多头自注意力计算，输出形式为[batch_size, seq_len, embed_dim]
 attention_output = self.attention（x, x, x, mask）
 #残差连接（x + attention_output），然后应用 dropout 函数
 x = x + self.dropout（attention_output）
 #形式保持不变：[batch_size, seq_len, embed_dim]
 #应用层归一化，保持形式不变
 x = self.norm1（x） #形式为[batch_size, seq_len, embed_dim]
 #前馈神经网络，输出形式为[batch_size, seq_len, embed_dim]
 ff_output = self.feed_forward（x）
 #再次进行残差连接（x + ff_output），然后应用 Dropout
 x = x + self.dropout（ff_output） #形式为[batch_size, seq_len, embed_dim]
 #应用层归一化，保持形式不变
 x = self.norm2（x） #形式为[batch_size, seq_len, embed_dim]
 return x

解读如下。

（1）初始化阶段
● self.attention 完成多头自注意力，入出均为 [batch_size, seq_len, embed_dim]。
● self.norm1、self.norm2 对 embed_dim 维度做归一化，形状不变。
● self.feed_forward 为前馈子层，入出形状一致。
● self.dropout 用于防止过拟合。

（2）前向阶段
输入 x 为 [batch_size, seq_len, embed_dim]。先做自注意力得到 attention_output，再与原始 x 做残差相加并 Dropout，利于梯度稳定。随后经 norm1 归一化；再进入前馈得到 ff_output，再次残差相加 Dropout，最后经 norm2 归一化输出，形状始终保持 [batch_size, seq_len, embed_dim]。