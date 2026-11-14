+   5) 其他神经元的状态保持不变，即 $\forall i \neq j, v_i(t+1) = v_i(t)$。
+   6) 根据输出 $x_i$ 和 $x_j$ 的值，调整连接权重 $w_{ij}$ 和阈值 $b_j$。
+   7) 返回步骤 1)，不断迭代，直到网络不发生变化或满足终止条件。

# 6.3.2 受限玻尔兹曼机

受限玻尔兹曼机（Restricted Boltzmann Machine，RBM）是玻尔兹曼机的受限版本[6]，由 Ruslan Salakhutdinov 等人提出。玻尔兹曼机中的神经元之间具有双向连接，这种模型的特点是可以基于模型的抽样从未知的概率分布中学习样本的重要特征。然而，这种学习的过程是非常困难和耗时的，因此在求解实际问题时使用的并不多，RBM 的提出就是通过在玻尔兹曼机网络拓扑结构上加上一些限制来缓解这一问题。

具体来说，RBM 是一个马尔可夫随机场（Markov Random Field，MRF），也是一个双边无向图模型，如图 6-4 所示。图 6-4 中有 $m$ 个可见神经元 $\boldsymbol{v}=(v_1, v_2, \cdots, v_m)$，表示可见数据，还有 $n$ 个隐藏神经元 $\boldsymbol{h}=(h_1, h_2, \cdots, h_n)$，用来表示可见变量之间的依赖。这里主要关注二元 RBM，即 $(\boldsymbol{v}, \boldsymbol{h})$ 随机变量值的值域为 $\{0, 1\}$。模型 $(\boldsymbol{v}, \boldsymbol{h})$ 的概率分布为

![](bbox=[0.58, 0.33, 0.92, 0.48])

$$p(\boldsymbol{v}, \boldsymbol{h}) = \frac{1}{Z} e^{-E(\boldsymbol{v}, \boldsymbol{h})}$$ (6-7)

那么可见变量 $\boldsymbol{v}$ 的概率分布即为联合分布的边缘分布：

$$p(\boldsymbol{v}) = \sum_h p(\boldsymbol{v}, \boldsymbol{h}) = \frac{1}{Z} \sum_h e^{-E(\boldsymbol{v}, \boldsymbol{h})}$$ (6-8)

其中，$Z = \sum_{v,h} e^{-E(\boldsymbol{v}, \boldsymbol{h})}$，称作剖分函数，其计算是非常困难的。$E(\boldsymbol{v}, \boldsymbol{h})$ 称作能量函数，计算公式为

$$E(\boldsymbol{v}, \boldsymbol{h}) = - \sum_{i=1}^m \sum_{j=1}^n w_{ij} v_i h_j - \sum_{i=1}^m b_i v_i - \sum_{j=1}^n c_j h_j$$ (6-9)

其中，$w_{ij}$ 是神经元 $v_i$ 和 $h_j$ 之间的实值权重，$b_i$ 和 $c_j$ 分别是第 $i$ 个可见神经元和第 $j$ 个隐藏神经元上的实值偏置项。

在 RBM 中，只有同一层的变量之间有连接，而不同层的变量之间无连接，所以给定可见变量、隐藏变量是条件独立的，反过来也成立，即

$$p(\boldsymbol{h} \mid \boldsymbol{v}) = \prod_{j=1}^n p(h_j \mid \boldsymbol{v}), \quad p(\boldsymbol{v} \mid \boldsymbol{h}) = \prod_{i=1}^m p(v_i \mid \boldsymbol{h})$$ (6-10)

单独一个变量为 1 的条件概率计算方法如下：

$$p(h_j=1 \mid \boldsymbol{v}) = \sigma(\sum_{i=1}^n w_{ij} v_i + c_j), \quad p(v_i=1 \mid \boldsymbol{h}) = \sigma(\sum_{j=1}^m w_{ij} h_j + b_i)$$ (6-11)

其中 $\sigma(x) = 1/(1+e^{-x})$ 是 Sigmoid 激活函数。

# 1. 受限玻尔兹曼机的参数学习

RBM 的训练采用最大似然估计思想，具体来说就是 log 似然的梯度上升法，设 RBM 的参