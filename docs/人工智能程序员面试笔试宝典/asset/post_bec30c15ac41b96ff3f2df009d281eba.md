
本节主要介绍第二种图卷积神经网络，即谱图卷积。简要来讲就是利用图的拉普拉斯矩阵的特征值和特征向量来研究图的性质。

由于图卷积的基础原理涉及图上的傅里叶变换，拉普拉斯矩阵的谱分解等理论问题，需要大量的公式推导，过于理论，所以这里只介绍图卷积的应用。谱图卷积也有多种，此处主要介绍两种。

### （1）利用切比雪夫多项式近似和一阶近似的GCN

由于交通网络就是很明显的一个非欧式距离的例子，因为在每个路口可能连接着三条路、四条路或者更多的路。所以就利用IJCAI 2018的论文《Spatio-Temporal Graph Convolutional Networks:A Deep Learning Framework for Traffic》来介绍交通网络中的图卷积网络结构。

v_t ∈ R^n 是 n 个路段在时间 t 时观察到的交通流向量，图6-7展示了路网结构交通流的示意图。这篇论文就是要在给定前 M 个样本后来预测后面 H 个时间段的交通流。

![](img/63904e7df2325ca111d7e3f06153a9e2_183_0.png)

图6-7 路网结构交通流示意图

将 *G 作为图卷积的符号，并将核 Θ 和信号 x ∈ R^n 的乘法定义为：

Θ *G x = Θ(L) x = Θ( I_n - D^{-1/2} W D^{-1/2} ) x = Θ( U Λ U^T ) x = U Θ( Λ ) U^T x

U ∈ R^{n×n} 是归一化的拉普拉斯矩阵 L ∈ R^{n×n} 的特征向量对应的矩阵，也被称为图的傅里叶基。 D ∈ R^{n×n} 是度矩阵，即 D_{ii}= Σ_j W_{ij}。 Λ ∈ R^{n×n} 是 L 的特征值矩阵，卷积核 Θ( Λ ) 是一个对角矩阵。但此时卷积核 Θ( Λ ) 需要 n 个参数，乘积 U Θ( Λ ) U^T x 的计算复杂度也较高，所以这个公式存在一些弊端。

为了解决算法复杂度的问题，论文中采用了两种近似方法，分别是切比雪夫多项式趋近和一阶近似。首先介绍切比雪夫近似。

用一个关于 Λ 的多项式将核 Θ 限制起来，即：

$$\Theta(\Lambda)=\sum_{k=0}^{K-1}\theta_k\Lambda^k=\sum_{k=0}^{K-1}\theta_k T^k(\tilde{\Lambda})$$

K是卷积从中心开始的最大半径，是图卷积核的大小。$T_k(x)$是切比雪夫多项式，用于近似核。$\tilde{\Lambda}=2\frac{\Lambda}{\lambda_{\max}}-I_n$，$\lambda_{\max}$是$L$的最大特征值。

则图卷积即为：

$$\Theta *_G x = \Theta(L)x = \sum_{k=0}^{K-1}\theta_k T_k(\tilde{L})x$$

而一阶近似中假设 $\lambda_{\max}=2$，则有

$$\Theta *_G x \approx \theta_0 x + \theta_1\left(2\frac{L}{\lambda_{\max}}-I_n\right)x \approx \theta(L)x = \theta_0 x + \theta_1\left(D^{-\frac12}WD^{-\frac12}\right)x$$

为了约束参数并为稳定数值计算，不妨令 $\theta' \approx \theta_0 = -\theta_1$，$\tilde{W}=W+I_n$，$\widetilde{D}_{ii}=\sum_j\tilde{W}_{ij}$，则可得新的表达式：

$$\Theta *_G x \approx \theta_0 x + \theta_1\left(D^{-\frac12}WD^{-\frac12}\right)x = \theta\left(\tilde{D}^{-\frac12}\tilde{W}\tilde{D}^{-\frac12}\right)x$$

此时卷积核只有$K$个参数，一般$K$远小于$n$。同时也降低了计算复杂度。

### （2）High-Order Adaptive GCN

这里介绍的High-Order Adaptive GCN来自于2017年一篇论文《Convolution on Graph A High-Order and Adaptive Approach》。

首先定义顶点$v_i$的$k$阶邻居$NB_i=\{v_j\in V\mid d(v_i,v_j)\le k\}$。$A\in R^{N\times N}$是邻接矩阵，$A^k$的第$i$行第$j$列表示的是顶点$i$到顶点$j$的$k$步路径的个数。

定义$k$阶卷积：

$GC^k = (W_{gc\_k} \odot \tilde{A}^k) X_t + B_k$

其中：

$\tilde{A}^k = \min\{A^k + I,1\}$

$A^k$加单位阵是为了让图上的每个顶点都能实现自连接。$B_k$是偏置矩阵。根据公式可以看出卷积操作就是取一个顶点的$k$阶邻居的特征向量作为输入，输出它们的加权平均值。

High-Order Adaptive GCN的另一点改进之处就是引入了Adaptive Filtering Module（自适应滤波器模块），它根据一个顶点的特征以及邻居的连接过滤卷积的权重，使得网络自适应地找到卷积目标并且更好地捕获局部的不一致。从描述中可以看出这种改进机制类似于注意力机制。自适应滤波器是一个在权重矩阵$W_{gc\_k}$上的非线性操作器$g$：

$\tilde{W}_{gc\_k} = g \odot W_{gc\_k}$

其中$\odot$表示element-wise矩阵乘法。操作器$g$反映了顶点特征与图的连接。

$g = f_{adp}(\tilde{A}^k, X) = sigmoid(Q \cdot [A^k, X])$

其中$[\cdot, \cdot]$表示矩阵拼接。此时图卷积的定义如下：

$GC^k = (\tilde{W}_{gc\_k} \odot \tilde{A}^k) X + B_k$

图6-8左边展示了对于一个顶点，$k=2$时的$GC^k$：自适应滤波器$g$的底层加到权重矩阵$W_{gc\_1}$和$W_{gc\_2}$上，得到了自适应权重$\tilde{W}_{gc\_1}$和$\tilde{W}_{gc\_2}$；第二层把自适应权重和对应的邻接矩阵拼到一起为了卷积使用。图右边强调了卷积是在图上每个顶点都做的，并且是一层一层的。

![](img/63904e7df2325ca111d7e3f06153a9e2_186_0.png)

1st & 2nd order convolution

adaptive filtering

图6-8 High-Order Adaptive GCN结构示意图

在所有的卷积层之后将那些来自不同阶的卷积层的输出特征拼接起来：

$GC^{(k)}=[GC^1, GC^2, \dots, GC^k]$

下面就可以将$GC^{(k)}$应用于解决不同的问题，例如2018年一篇论文《 High-Order Graph Convolutional Recurrent Neural Network:A Deep Learning Framework for Network-Scale Traffic Learning and Forecasting》利用High-Order GCN提取路网的图信息，从而提升了交通流预测的效果，需要注意的是在该工作中没有用到adaptive filter，同时又引入了新的Free Flow Matrix用来更好地刻画交通的性质。

由于图卷积神经网络的特殊结构可以解决图结构中非欧式距离的问题，所以在金融、交通、生物、知识图谱、对话系统等涉及网络结构的问题中都可以大放光彩。为了能在面试中脱颖而出，了解并熟悉图卷积神经网络的编程非常重要。本节只是简要介绍了交通领域中的交通流预测的应用，省略了较多理论推导步骤，如果感兴趣，可以根据参考文献详细钻研。

## 6.5 深度学习在运筹优化中的应用

本章最后一节将展示深度学习在运筹优化中的应用，因为这个问题非常新颖，同时又涉及强化学习、指针网络、注意力机制、LSTM、encoder-decoder框架等人工智能前沿领域，那么通过熟悉这个问题就可以对当下很多前沿研究都有所了解，所以本节将详细展示介绍该问题的每个结构。

需要注意的是虽然强化学习可以用来解决运筹学中的TSP、VRP问题，但是真实场景要远比TSP问题复杂得多，所以工业界目前还未用强化学习代替原先的运筹学经典算法。本章介绍这类问题的主要原因是它涉及人工智能前沿研究的很多问题，这些问题可以应用在各个领域中。

强化学习广泛用于求解序列决策问题，目前在游戏AI、棋类等领域取得了显著的成果，最著名的当属是DeepMind的AlphaGo。其实，让计算机学会下围棋其实本质上是求解组合优化问题。那么除了围棋以外，强化学习框架是否能够求解其他经典组合优化问题呢？答案是肯定的，本章主要介绍强化学习运用于求解经典组合优化问题，如Traveling Salesman Problem（TSP）、Vehicle Routing Problem（VRP）等方法。

### 6.5.1 指针网络

指针网络（Pointer Network）是用深度学习解决经典组合优化问题（如TSP、VRP、MST等）的基础，由Oriol Vinyals等首次提出。指针网络的结构脱胎于Google提出的Sequence-to-Sequence（以下简称Seq2Seq）的网络结构，如图6-9所示。浅蓝色部分被称作编码器（Encoder），紫色部分为解码器（Decoder），可以理解为：先把输入的数据进行编码，然后再对编码后的结果进行解码。

那么指针网络和Seq2Seq网络的区别是什么呢？

Seq2Seq把输入的一系列数据用RNN等编码成向量，然后通过概率链式规则（probability chain rule）和另一个RNN网络得到输出的结果，它常用于机器翻译，结构如图6-9所示。比如输入一句话“how are you？”，把每个字符分别编码成向量，然后在Decoder

中分别输出对应的中文，如“你好吗？”。指针网络在编码器的部分与Seq2Seq类似，在解码器部分，指针网络对编码后的多个向量组成集合，使用了一种以内容为基础的机制，叫作Content-based Attention Mechanism。这个机制的输出是一个长度和向量集合长度一样的概率分布。通过该分布可以获取到序号，序号指向向量集合（或者说输入）的位置，表示解码的输出，故名指针网络，结构如图6-10所示。Seq2Seq解码后的结果可能不在编码输入的数据中，而指针网络解码后的结果为指针指向输入数据的位置。

![](img/63904e7df2325ca111d7e3f06153a9e2_188_0.png)

图6-9: Sequence-to-Sequence示意图

![](img/63904e7df2325ca111d7e3f06153a9e2_188_1.png)

图6-10 指针网络示意图

举例说明：假设要解TSP10的问题。有10个城市位置，位置用一个二维向量表示。指针网络的编码器对输入的10个城市的二维向量分别进行编码，一次编码一个，编码的方式可以选择线性转换，如从二维到d-维，编码后的结果，称之为潜在记忆状态（latent memory states）。解码器接收这10个城市的潜在记忆状态作为初始

输入。在每一步解码时，使用之前所述的Attention Mechanism获取指针，指向输入的位置。上一步解码获取的序号所对应编码后的向量作为下一步解码的输入。比如解码的第一步获取的2，表示序号为2的城市，在编码后的向量集合中，序号为2的向量作为下一步的输入。

### 1. 编码器

如前所述，指针网络的编码和Seq2Seq一样，先对数据进行embedding的线性转换，然后输入到一个RNN网络中，通过各个RNN单元的hidden state进行传递。但是Nazari等提出：在类似于TSP和VRP的组合优化问题中，输入数据的顺序其实是没有影响的，不同的顺序包含相同的信息。比如输入数据10个城市的位置按编号1, 2, …, 10的顺序，与按编号2, 5, 1, …, 8等顺序输入到编码器中本质上是一样的。因此，他们提出了取消RNN的编码器，直接进行Embedding的网络结构。后面章节会讲述相关方法。

### 2. 解码器：指针和Attention机制

首先，定义一个向量集合 ref={enc1, enc2, …, enc_k}，这个集合是编码器对输入数据编码后的向量集合，其中 enc_i ∈ ℝ^d。另外定义一个队列向量（query vector） q=dec_t ∈ ℝ^d。原先的队列向量对应解码器中一个RNN单元输出的hidden state。但是提出了glimpse函数用于整合输入向量序列。实验表明glimpse能够在计算量增加不大的情况下，一定程度上提升模型的效果，因此glimpse函数有使用在Attention机制当中。具体的定义描述如下：

- Glimpse函数： G(ref, q)
- Attention函数： A
- Attention机制中可训练的参数：
  - Attention矩阵： W_ref^g, W_q^g ∈ ℝ^{d×d}
  - Attention向量： v^g ∈ ℝ^d
- 指针的概率分布： p ∈ ℝ^k

其中，k表示输入的数量，比如10个城市，指针概率分布p_i指该步解码选择城市i的概率。通过如下公式，完成glimpse方式的attention。

$p = A(ref, q; W_{ref}^g, W_q^g, v^g) = \sum_{i=1}^k r_i p_i,\ r_i \in ref$

$G(ref, q; W_{ref}^g, W_q^g, v^g) = \sum_{i=1}^k r_i p_i,\ r_i \in ref$

$g_0 = q$

$g_m = G(ref, g_{m-1}; W_{ref}^g, W_q^g, v^g)$

可以观察到，glimpse函数本质上是ref向量集合的权重和。可以选择多次用相同的ref进行迭代计算，比如计算m次，最后的$g_m$作为Attention函数的输入。实验表明一次以上迭代的glimpse计算几乎对模型的学习没有什么影响，因此实际代码实现的时候可以考虑只迭代一次。

公式需要首先能够得到Attention函数。那么Attention函数是如何计算的呢？假设指针网络表示的策略函数为$\pi$。$\pi(j)=i$表示解码器在第$j$步输出的指针为$i$。通过如下公式计算指针和Attention函数：

$u_i = \begin{cases} v_T \cdot \tanh(W_{ref} \cdot r_i + W_q \cdot q) & \text{如果} i \neq \pi(j) \text{对于} j < i \\ -\infty & \text{其他} \end{cases} \quad i=1,2,\cdots,k$

$A(ref, q; W_{ref}, W_q, w) = softmax(u)$

其中，上述$u$为$softmax$函数输入，$r_i \in ref$。在类似于TSP的问题中，因为每个城市只经过一次，对于前面编码步骤中输出过的城市序号，$u_i$设定为负无穷大，相应的$softmax$的结果即为0。公式注意区别于glimpse的可训练参数：

- Attention矩阵： $W_{ref}, W_q \in \mathbb{R}^{d \times d}$
- Attention向量： $v \in \mathbb{R}^d$

除了通过直接使用$u$的Attention函数外，也提出了两种改进的Attention函数，让模型能够更好地学习，并有更好的泛化性。

Softmax Temperature: Softmax温度调整如下：

$A(ref, q; W_{ref}, W_q, w) = softmax(u / T)$

T定义为温度，当T>1时，Attention函数计算的分布会更平滑，这样可以防止模型的结果过于偏重某个指针。

Logit Clipping:Logit限幅：

A(ref, q; W_ref, W_q, w)=softmax(C tanh(u))

C是控制logit范围的超参数。论文中表明有效的clipping可以帮助模型探索并且产生一定的边际效益。

### 6.5.2 指针网络PyTorch代码实现

本节主要介绍用PyTorch实现指针网络以及Attention机制。

### 1. Embedding代码

首先要构造Embedding对象，对输入进行Embedding。代码如下所示：

```
```python
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
```
```

```
# inputs: 维度 (batch_size, seq_len, input_size), seq_len 表示输入数据的长度, 比如 10 个城市
# embedded: 维度 (batch_size, seq_len, hidden_size)
batch_size = inputs.size(0)
seq_len  = inputs.size(1)
# 新的 embedding 维度: batch_size, input_size, embedding_size
embedding = self.embedding.repeat(batch_size, 1, 1)
embedded = []
# 共享 embedding 参数
for i in range(seq_len):
    # batch multiplication 需要相同 Tensor 维度大小, so, unsqueeze 1
    embedded.append(torch.bmm(inputs[:, i, :].unsqueeze(1).float(), embedding))
embedded = torch.cat(embedded, 1)
return embedded
```

### 2. Encoder（编码器）代码

完成Embedding后需要创建编码器。代码如下：

```
class Encoder(nn.Module):
    def __init__(self, embedding_size, hidden_size):
        # embedding_size: embedding 的维度
        # hidden_size: RNN 的 hidden state 维度
        super(Encoder, self).__init__()
        self.lstm = nn.LSTM(embedding_size, hidden_size)
        self.hidden_size = hidden_size

    def forward(self, embedding_x):
        # embedding_x (tensor): 维度 (sequence length, batch, input_dim)
        # hidden (tensor): 维度 (num_layer * num_direction, batch_size, hidden_dim)
        # output (tensor): 维度 (sequence length, batch_size, hidden_dim)
        # next_hidden (tensor): 维度 (num_layer * num_direction, batch_size, hidden_dim)
        output, next_hidden = self.lstm(embedding_x)
        return output, next_hidden
```

这里使用PyTorch默认的LSTM。注意：默认的LSTM接受输入的维度为（seq_len, batch_size, input_size）。

### 3. Attention函数代码
