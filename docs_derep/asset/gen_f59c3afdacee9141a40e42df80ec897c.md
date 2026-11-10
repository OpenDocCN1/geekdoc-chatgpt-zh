- 1） 在图上截取一段长度不变的节点序列；
- 2） 针对序列里的每个节点，采集同一规模的邻居集合；
- 3） 把节点本身与对应邻居构成的子图做规范化处理，作为卷积模块的输入。

完成上述三步后即可得到全部的卷积切片，再用卷积结构逐个对这些切片进行运算。如图6-6所示。

[PRE0]

### 6.4.2 谱域图卷积

本小节要介绍的第二类图卷积神经网络是谱图卷积。核心思路是借助图的拉普拉斯矩阵的特征值与特征向量来刻画图的结构特性。

图卷积的理论根基涉及图上傅里叶变换与拉普拉斯谱分解，需要较多的推导，这里只聚焦应用层面。谱图卷积的方案并不唯一，下面重点讲两种常见做法。

### （1）基于切比雪夫多项式逼近和一阶近似的GCN

交通网络就是典型的非欧式结构，一个路口可能连着三条、四条甚至更多道路。正因如此，这里用 IJCAI 2018 论文《Spatio-Temporal Graph Convolutional Networks:A Deep Learning Framework for Traffic》来说明交通场景下的图卷积建模。

v_t ∈ R^n 表示时间 t 下 n 条路段的交通流向量，图6-7给出了路网交通流的示意。该工作在已知前 M 个样本的基础上，预测随后 H 个时段的交通流。

[PRE1]

图6-7 路网结构交通流示意图

记 *G 为图卷积符号，核 Θ 与信号 x ∈ R^n 的乘积定义为：

Θ *G x = Θ(L) x = Θ( I_n - D^{-1/2} W D^{-1/2} ) x = Θ( U Λ U^T ) x = U Θ( Λ ) U^T x

U ∈ R^{n×n} 是归一化拉普拉斯矩阵 L ∈ R^{n×n} 的特征向量矩阵，即图的傅里叶基。 D ∈ R^{n×n} 为度矩阵， D_{ii}= Σ_j W_{ij}。 Λ ∈ R^{n×n} 为 L 的特征值矩阵，卷积核 Θ( Λ ) 为对角阵。但此时 Θ( Λ ) 需要 n 个参数，U Θ( Λ ) U^T x 的计算代价也偏高，因而该式实用性受限。

为降低复杂度，论文采用了切比雪夫多项式逼近与一阶近似两种策略，先讲切比雪夫近似。

把核 Θ 用 Λ 的多项式限制，即：

[PRE2]

K 表示卷积核半径，即从中心向外最大范围，$T_k(x)$ 为切比雪夫多项式。$\tilde{\Lambda}=2\frac{\Lambda}{\lambda_{\max}}-I_n$，$\lambda_{\max}$ 为 L 的最大特征值。

对应的图卷积为：

[PRE3]

在一阶近似里取 $\lambda_{\max}=2$，则有

[PRE4]

为约束参数并保证数值稳定，令 $\theta' \approx \theta_0 = -\theta_1$，$\tilde{W}=W+I_n$，$\widetilde{D}_{ii}=\sum_j\tilde{W}_{ij}$，得到新的形式：

[PRE5]

此时卷积核仅含 K 个参数，且 K 通常远小于 n，计算复杂度也随之下降。

### （2）高阶自适应GCN

这里的高阶自适应 GCN 来源于 2017 年论文《Convolution on Graph A High-Order and Adaptive Approach》。

先定义顶点 v_i 的 k 阶邻居 $NB_i=\{v_j\in V\mid d(v_i,v_j)\le k\}$。$A\in R^{N\times N}$ 为邻接矩阵，$A^k$ 的第 i 行第 j 列统计的是 i 到 j 的 k 步路径数。

k 阶卷积定义为：

$GC^k = (W_{gc\_k} \odot \tilde{A}^k) X_t + B_k$

其中：

$\tilde{A}^k = \min\{A^k + I,1\}$

对 $A^k$ 加单位阵是为了让每个顶点自连接。$B_k$ 为偏置矩阵。由式可知，该操作相当于取出顶点的 k 阶邻居特征作为输入，输出其加权均值。

高阶自适应 GCN 的另一改进是引入自适应滤波模块，它依据节点的特征与邻接关系对卷积权重进行过滤，使网络能自适应地聚焦目标并捕捉局部差异，机制与注意力类似。自适应滤波器是在权重矩阵 $W_{gc\_k}$ 上作用的非线性算子 g：

$\tilde{W}_{gc\_k} = g \odot W_{gc\_k}$

其中 $\odot$ 为逐元素乘法。算子 g 体现了节点特征与图连接的关系。

$g = f_{adp}(\tilde{A}^k, X) = sigmoid(Q \cdot [A^k, X])$

$[\cdot, \cdot]$ 表示矩阵拼接。此时图卷积写作：

$GC^k = (\tilde{W}_{gc\_k} \odot \tilde{A}^k) X + B_k$

图6-8 左侧展示了对某顶点 k=2 时的 $GC^k$：自适应滤波器 g 的输出加到权重矩阵 $W_{gc\_1}$ 与 $W_{gc\_2}$ 上得到自适应权重 $\tilde{W}_{gc\_1}$、$\tilde{W}_{gc\_2}$；第二层再把自适应权重与对应邻接矩阵结合用于卷积。右侧说明卷积是在图上每个顶点逐层进行的。

[PRE6]

1st & 2nd order convolution

adaptive filtering

图6-8 High-Order Adaptive GCN结构示意图

所有卷积层完成后，将不同阶的输出拼接：

$GC^{(k)}=[GC^1, GC^2, \dots, GC^k]$

随后 $GC^{(k)}$ 可用于各种任务，例如 2018 年论文《 High-Order Graph Convolutional Recurrent Neural Network:A Deep Learning Framework for Network-Scale Traffic Learning and Forecasting》用高阶 GCN 提取路网图信息提升交通流预测，该工作未使用自适应滤波，同时引入 Free Flow Matrix 以更好刻画交通特性。

由于图卷积能够处理非欧式距离，金融、交通、生物、知识图谱、对话系统等涉及网络结构的问题都能受益。想在面试中脱颖而出，掌握图卷积的编程很关键。本节仅简要介绍了交通流预测的应用，理论推导被省略，感兴趣可查阅参考文献深入研究。

## 6.5 深度学习在运筹优化中的应用

本章最后一节展示深度学习在运筹优化中的运用。该方向很新，同时融合了强化学习、指针网络、注意力机制、LSTM、编码器-解码器等前沿技术，熟悉它有助于把握当下诸多研究。

需要说明的是，虽然强化学习可用于求解 TSP、VRP 等运筹问题，但真实场景远比 TSP 复杂，工业界目前仍以经典运筹算法为主。本节介绍该类问题的主要价值在于覆盖了大量可迁移的前沿方法。

强化学习擅长序列决策，在游戏 AI、棋类等领域已取得突出成果，最典型的是 DeepMind 的 AlphaGo。让机器学会下围棋本质上是组合优化问题。除围棋外，强化学习框架能否解决其他经典组合优化问题？答案是可以，本章主要介绍其在 TSP、VRP 等问题上的应用方法。

### 6.5.1 指针网络

指针网络是用来解决 TSP、VRP、MST 等经典组合优化问题的基础模型，由 Oriol Vinyals 等提出。其结构源自 Google 的 Sequence-to-Sequence 网络，如图6-9。浅蓝色为编码器 Encoder，紫色为解码器 Decoder，思路是先对输入编码，再对编码结果解码。

指针网络与 Seq2Seq 的区别在哪？

Seq2Seq 用 RNN 等把输入序列编码成向量，再通过概率链式法则和另一个 RNN 得到输出，常用于机器翻译，结构见图6-9。例如输入“how are you？”，编码后解码输出对应中文“你好吗？”。指针网络的编码器与 Seq2Seq 类似，解码器则对编码后的向量集合采用基于内容的选择机制 Content-based Attention Mechanism，输出长度与向量集合相同的概率分布，据此获得序号，序号指向输入位置，故名指针网络，结构见图6-10。Seq2Seq 的解码结果可能不在输入中，而指针网络的解码结果始终指向输入位置。

[PRE7]

图6-9: Sequence-to-Sequence示意图

[PRE8]

图6-10 指针网络示意图

举例：求解 TSP10，有 10 个城市位置，每个位置是二维向量。编码器把 10 个城市向量依次编码，可选线性变换如二维到 d 维，得到潜在记忆状态。解码器以这 10 个潜在状态为初始输入，每一步用 Attention Mechanism 获取指针指向输入位置。上一步得到的序号对应编码向量，作为下一步解码的输入。例如第一步得到 2，则序号 2 的编码向量用于下一步。

### 1. 编码器

指针网络的编码与 Seq2Seq 一致，先做 embedding 线性转换，再送入 RNN，通过各单元 hidden state 传递。但 Nazari 等指出在 TSP、VRP 这类组合优化问题中，输入顺序不影响信息，不同顺序等价。因此他们提出去掉 RNN 编码器，仅保留 Embedding 结构，后文会详述。

### 2. 解码器：指针与Attention机制

设向量集合 ref={enc1, enc2, …, enc_k} 为编码器输出，其中 enc_i ∈ ℝ^d。设查询向量 q=dec_t ∈ ℝ^d，原先对应解码器 RNN 单元的 hidden state。为整合输入序列，提出 glimpse 函数，实验显示在计算量不大增加的前提下能提升效果，故常用于 Attention。

定义如下：

- Glimpse函数： G(ref, q)
- Attention函数： A
- Attention 可训练参数：
  - Attention矩阵： W_ref^g, W_q^g ∈ ℝ^{d×d}
  - Attention向量： v^g ∈ ℝ^d
- 指针概率分布： p ∈ ℝ^k

k 为输入数量，如 10 个城市，p_i 为本步选择城市 i 的概率。通过 glimpse 式 attention 完成：

$p = A(ref, q; W_{ref}^g, W_q^g, v^g) = \sum_{i=1}^k r_i p_i,\ r_i \in ref$

$G(ref, q; W_{ref}^g, W_q^g, v^g) = \sum_{i=1}^k r_i p_i,\ r_i \in ref$

$g_0 = q$

$g_m = G(ref, g_{m-1}; W_{ref}^g, W_q^g, v^g)$

可见 glimpse 本质是 ref 向量集合的加权和。可对同一 ref 迭代多次，取最后的 $g_m$ 作为 Attention 输入。实验表明多于一次迭代对学习帮助有限，实践中常只迭代一次。

Attention 函数如何计算？设策略函数为 π，π(j)=i 表示第 j 步解码输出指针为 i。计算如下：

$u_i = \begin{cases} v_T \cdot \tanh(W_{ref} \cdot r_i + W_q \cdot q) & \text{如果} i \neq \pi(j) \text{对于} j < i \\ -\infty & \text{其他} \end{cases} \quad i=1,2,\cdots,k$

$A(ref, q; W_{ref}, W_q, w) = softmax(u)$

其中 $u$ 为 softmax 输入，$r_i \in ref$。在 TSP 类问题中，每个城市只访问一次，已访问城市的 $u_i$ 置为负无穷，对应 softmax 为 0。相关参数为：

- Attention矩阵： $W_{ref}, W_q \in \mathbb{R}^{d \times d}$
- Attention向量： $v \in \mathbb{R}^d$

除基础形式外，还提出两种改进以提升学习与泛化。

Softmax Temperature: 

$A(ref, q; W_{ref}, W_q, w) = softmax(u / T)$

T 为温度，T>1 时分布更平滑，避免过度集中于某指针。

Logit Clipping:

A(ref, q; W_ref, W_q, w)=softmax(C tanh(u))

C 控制 logit 范围的超参数，有效 clipping 有助于探索并带来边际收益。

### 6.5.2 指针网络PyTorch代码实现

本节介绍用 PyTorch 实现指针网络与 Attention 机制。

### 1. Embedding代码

先构建 Embedding 对象对输入做 Embedding，代码如下：

[PRE9]python

class Embedding(nn.Module):

    def __init__(self, input_size, embedding_size):

        # input_size: 输入数据的维度，比如2维欧式距离的TSP城市位置

        # embedding_size: 想要embedding的维度，比如128

        super(Embedding, self).__init__()

        self.embedding_size = embedding_size

        # Embedding 参数初始化

        self.embedding = nn.Parameter(torch.FloatTensor(input_size, embedding_size))

        self.embedding.data.uniform_(-(1./math.sqrt(embedding_size)),1./math.sqrt(embedding_size))

    def forward(self, inputs):

[PRE10]