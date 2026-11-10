pos 用来标记词在句子里的出现顺序，2i 和 2i+1 分别对应特征向量的偶数维和奇数维，$d_{\text{model}}$ 就是向量的总维度。以维度为 4 为例，句子第一个词的位置编码计算如下：

$\mathrm{PE}_{0,0}=\sin \left(\frac{0}{10000^{0 / 4}}\right)=\sin (0)=0$

[PRE0]

总的来说，偶数维由正弦函数生成，奇数维由余弦函数生成。图 3.17 展示了当 $d_{\text{model}}=64$ 时，前 10 个位置的编码图案。

[PRE1]

有人会产生疑问：这种编码本质上是和绝对位置绑定的，如果把句子里的子句顺序对调但语义不变，绝对位置编码会不会失效？比如：

> 开饭啦，妈妈在楼下喊道。
> 妈妈在楼下喊道，开饭啦。

语义相同，顺序调换后每个词的位置编码却完全不同。下面来说明三角函数的绝对编码其实能体现相对位置。

证明：

记 $c_i = \frac{1}{10000^{2i/d_{\text{model}}}}$，第 $t$ 个位置的编码为：

[PRE2]

令 $k$ 为固定的偏移量，计算两个位置编码的点积 $\text{PT}_t^T \text{PE}_{t+k}$：

[PRE3]

[PRE4]

[PRE5]

推导里最关键的一步是三角恒等变换：

[PRE6]

由此可见，用三角函数搭建的绝对位置编码暗含了相对位置信息，所以前面提到的子句调换并不会带来干扰。仔细看会发现 $\cos$ 是轴对称函数，$k$ 为正或负都不会改变 $\text{PT}_t^T \text{PE}_{t+k}$ 的结果，也就是：

> 由三角函数构造的绝对位置编码所引入的位置关系只关心词与词的距离，不关心先后顺序。

这同时也是该方案的局限，学术界提出的相对位置编码 Relative Positional Embedding 就是针对这一点的改进，有兴趣可以自行查阅。

接下来看三角位置编码的代码实现：

[PRE7]

函数 `_encode` 的输入 `positions` 是形如 $[1,2,\cdots,N]$ 的向量，$N$ 为句子长度，`depth` 为特征维度。先把维度强制成偶数，保证 sin 与 cos 成对出现，再生成 $\frac{1}{10000^{2i/d_{model}}}$ 序列。代码看起来有些绕，抓住核心即可，第 10 行的关键在于：

[PRE8]

第 9 行和第 10 行本质上都在算 $\frac{1}{10000^{2i/d_{model}}}$ 这个序列，而它是由 $i=1,2,\cdots,n$ 变换而来，所以先造一个递增的 $i$ 序列，再整体做一次指数变换。代码对应的数学表达为：

$e^{\frac{\text{list}(i)-\log(10000)}{\frac{d_{model}}{2}}} = \frac{1}{10000^{2\text{list}(i)/(d_{model})}}$

左边是代码的实现方式，经过简单推导可得右边理论公式。第 11 行负责复制编码，Batch 内各句长度相同且短句用 Padding 补齐，因此位置编码在 Batch 内是共享的，用 `tf.tile` 直接扩展。第 12 行加上 positions 得到 $\frac{\text{pos}}{10000^{2 i / d_{\text{model}}}}$，第 13 行分别做 sin、cos 变换后拼接即可。

## 3.4 单向掩码：让解码器只看见过去

在 Self-Attention 的实现里提到过 Mask，其初衷是去掉因句子长度不一致带来的 Padding 噪声对特征提取的干扰。本节介绍的另一种 Mask 机制，代码与 Self-Attention 保持一致，既是 Batch 训练的产物，也是 Decoder 训练时必备的模块。

回顾机器翻译任务中 Transformer Decoder 的输入输出形式。Decoder 在预测第 $n+1$ 个词时，输入由两部分构成：一部分是 Encoder 的输出，即源句的语义向量；另一部分是已生成的目标前 $n$ 个词 $y_{i(i=0,1, \cdots n)}$。设源长为 $L_{\mathrm{s}}$，目标长为 $L_{\mathrm{t}}$，Encoder 输出为 $z_{i(i=1,2, \cdots L_{\mathrm{s}})}$，目标译文为 $y_{j(j=1,2, \cdots L_{\mathrm{t}})}$。训练时用参考译文的前 $n$ 个词作为 Decoder 输入，把 Decoder 抽象为 $F$，则预测第 $n+1$ 个词的 Loss 为

[PRE9]

一个训练样本对的总 Loss 为

[PRE10]

实际训练以 Batch 为单位，Decoder 接收的是含 Padding 且长度固定的目标矩阵 $y_{j(j=1,2, \cdots, L_{\mathrm{t}})}$。加入 Mask 矩阵的目的是在喂入完整目标序列时，实现目标侧的部分可见，让推理保持因果性，即

[PRE11]

下面给出 Mask 矩阵的生成代码：

[PRE12]

先说明三个关键的 TensorFlow 操作：

- `tf.sequence_mask`：按每个序列的有效长度生成掩码张量。
- `tf.expand_dims`：在指定轴上增加一个维度。
- `tf.linalg.band_part`：保留主对角线及下方元素，其余置零。

下面用实例演示构造过程。设 Batch 里有两句话，长度分别为 2 和 3，则 `future_mask` 的输入为 `sequence_length = [2,3]`，`maximum_length = 3`，图 3.18 为构造示例。

[PRE13]

图 3.18 Mask 矩阵的构造示例

(1) 用 `tf.sequence_mask` 根据 `sequence_length` 生成二维矩阵。
(2) 用 `tf.expand_dims` 把它扩展为三维。
(3) 用自定义函数 `_lower_triangle_mask`，核心为 `tf.linalg.band_part`，生成三角掩码。
(4) 将三维的 `sequence_length` 矩阵与 Mask 相乘。`sequence_length` 的第二维会自动广播到第三维，形状与 Mask 一致，结果如图所示右侧。

得到 Mask 后来看它如何影响 Decoder。回忆 Self-Attention 中 Mask 的位置，它直接作用于 Dot 分值矩阵，如图 3.19 所示。

[PRE14]

如图 3.20，对掩码后的 Dot 矩阵按行做 Softmax，False 位置会变成负无穷，在 fp32 下等价于 `tf.float32.min`，Softmax 后权重为 0。再用 Attention 矩阵乘 V 矩阵，得到各位置的词表示 $z$。可以看出 $z_1$ 仅来自第一个词，$z_2$ 仅来自前两个词，$z_3$ 综合前三个词。一次 Batch 操作就得到句子每一位置的表示，且每个表示只依赖其前置信息，符合 Decoder 的因果要求。

[PRE15]

## 3.5 训练提速：两个实用技巧

前面已经讲清了 Transformer 的核心结构与原理，下面介绍两个能提升训练速度与收敛稳定性的做法：归一化层前置和梯度累积。它们不仅适用于 Transformer，也适用于其他结构相似的模型。

### 3.5.1 技巧一：把归一化提前

先说归一化层。在计算机视觉里常以 BN 形式出现，在自然语言处理里多以 LN 形式出现，二者目的相同：让 Batch 内样本的均值方差逼近整体，使每个维度的输入被独立规范化。这样浅层计算的偏差不会过度传导到深层，梯度保持在合理区间，深度网络收敛更稳。

再说学习率预热 Learning Rate Warmup。该思想最早见于论文 *Deep Residual Learning for Image Recognition*，核心是在训练初期使用较小的学习率，等模型进入稳定区域后再切到常规学习率。其本质也是保证前期梯度变化可控，避免过早陷入局部最优而影响后续训练。