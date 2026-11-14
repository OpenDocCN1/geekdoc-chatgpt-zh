局部注意力中语义向量的计算方式如下：
\[
c_i = \sum_{j=p_i-D}^{p_i+D} \alpha_{i,j} h_j \quad (4-9)\\
\[
\alpha_{i,j} = \frac{\exp(e_{ij})}{\sum_{k=p_i-D}^{p_i+D} \exp(e_{ik})} \exp\left(-\frac{(j-p_i)^2}{2(D/2)^2}\right) \quad (4-10)\\
\[
e_{ij} = h_i'^T \cdot W_\alpha \cdot h_j \quad (4-11)\\
\[
p_i = T_x \cdot \text{sigmoid}(V_p^T \tanh(W_p h_i')) \quad (4-12)


其中，\( p_i \) 是对齐位置，\( D \) 是窗口范围，\( \alpha_{i,j} \) 是注意力系数，\( T_x \) 是 Encoder 端的向量表示，\( W_\alpha \)、\( W_p \) 和 \( V_p \) 是可学习的参数。

自注意力是指让模型注意到整个输入中不同部分之间的相关性，自注意力机制是 Transformer 模型的核心，我们将在 4.2.3 节中对其进行详细介绍。

# 4.2 Transformer 概述

# 4.2.1 Transformer 的结构

Transformer[1] 的结构如图 4-6 所示，它由编码器（Encoder）和解码器（Decoder）两部分组成，每个部分包含若干个模块（Block）。其中，编码器负责理解输入，为每个输入构造对应的语义表示。解码器负责以自回归的方式逐个生成输出序列中的元素。

Transformer 的编码器由 6 个相同的层堆叠而成，每个层包含两个子层，分别是多头自注意力层和前馈神经网络层。其中，多头自注意力是 Transformer 的核心，将在 4.2.3 节中对其进行详细介绍。此外，在两个子层中，Transformer 使用残差连接（Residual Connection）和层归一化（Layer Normalization，LN）机制进行性能优化。这样一来，编码器中每一层的输出可以表示为

output = LayerNorm(X + SubLayer(X)) \quad (4-13)

其中，\( X \) 表示输入，SubLayer 表示多头自注意力子层或者前馈神经网络子层，LayerNorm(\(\cdot\)) 表示层归一化。

此外，Transformer 的解码器也是由 6 个相同的层堆叠而成。与编码器中每层的两个子层不同的是，解码器的每层还包含第 3 个子层，第 3 个子层对编码器的输出进行多头自注意力计算。另外，与编码器中的多头自注意力子层（第 1 个子层）不同的是，解码器中的第 1 个子层中的多头自注意力使用了掩码（Mask）操作，因此在解码过程中，解码器只可以看到已经生成的解码序列，对未来即将生成的单词，需要进行掩码。在解码器的三个子层中，同样使用残差连接和层归一化机制进行了性能优化。

在接下来的小节中，对 Transformer 架构中的具体细节进行介绍，包括 Transformer 的输入编码、自注意力机制、多头自注意力机制和带有掩码操作的自注意力机制、Transformer 中的残差连接、层归一化等。