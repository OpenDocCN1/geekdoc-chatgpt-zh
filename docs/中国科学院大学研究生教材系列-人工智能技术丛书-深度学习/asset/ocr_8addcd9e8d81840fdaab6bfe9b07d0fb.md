当。DETR 使用端到端（End-to-End）的方式实现目标检测，如图 4-30 所示。

![](bbox=[0.039, 0.125, 0.915, 0.26])

图 4-30 结合 CNN 和 Transformer 的 DETR 结构

DETR 模型的主要组成是 CNN 和 Transformer，Transformer 借助自注意力（Self-Attention）机制，可以显式地对一个序列中的所有元素两两之间的关系进行建模，使得这类 Transformer 结构非常适合带约束的集合预测问题。

DETR 分为 4 个部分：骨干网络、编码器、解码器和预测头。DETR 的结构如图 4-31 所示。

![](bbox=[0.039, 0.425, 0.915, 0.6])

图 4-31 DETR 的结构

首先，DETR 采用了 CNN 作为骨干网络（Backbone）来处理 \(x_{img} \in \mathbb{R}^{B \times 3 \times H_0 \times W_0}\) 的图像，将它转换为 \(f \in \mathbb{R}^{B \times C \times H \times W}\) 的特征图。

其次，得到图像特征 \(f \in \mathbb{R}^{B \times C \times H \times W}\) 后，DETR 使用 Transformer 编码器对图像特征 \(f\) 进行编码与建模。

然后，编码器使用了原始的多头自注意力机制来进行序列之间的关系建模，公式为

\(\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V \quad (4-35)\)

原始 Transformer 中的位置编码为

\(\text{PE}_{(pos, 2i)} = \sin\left(\frac{pos}{10000^{2i/d}}\right) \quad (4-36)\)