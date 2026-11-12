![](bbox=[0.18, 0.085, 0.225, 0.115])

式中, 为节点的霍夫曼编码, 左节点为0, 右节点为1; \(\sigma (\cdot)\) 为Sigmoid函数; \(X_w\) 为中心词向量。式 (3-38) 可以转换为整体形式:

。因此, Skip-gram的似然函数如下:

```
\(\prod_{w \in D} \prod_{u \in Context(w)} \prod_{j=2}^{J^u} [[\sigma(X_w^T \theta_{j-1}^u)]]^{1-d_j^u} \cdot [[1-\sigma(X_w^T \theta_{j-1}^u)]]^{d_j^u}\)
```

似然函数的最大化, 等价于log损失函数的最小化:

```
\(- \sum_{w \in D} \sum_{u \in Context(w)} \sum_{j=2}^{J^u} |(1-d_j^u) \cdot \log[\sigma(X_w^T \theta_{j-1}^u)] + d_j^u \cdot \log[1-\sigma(X_w^T \theta_{j-1}^u)] |\)
```

由于构建霍夫曼树时对节点的路径做了优化, 对于大部分词, 我们只需要遍历少量节点就能预测词的概率, 对比原始Softmax的时间复杂度 \(O (|D|)\) , Hierarchical Softmax可以将其降低到 \(O (\log|D|)\) 。

# (2) Negative Sampling

Negative Sampling是不同于Hierarchical Softmax的另一种优化方法, 我们仍以Skip-gram为例进行介绍。Negative Sampling的核心思想是将目标函数进行转换, 将原始方法中每一个上下文词需要与所有其他词学习概率的差异, 变成只需要与少数几个通过采样得到的词学习概率的差异。我们以和中心词 \(w_c\) 对应的上下文词 \(w_o\) 为正样本, 对根据某种分布 \(P (w)\) 采样 \(K\) 个未出现在 \(w_c\) 窗口中的词作为负样本, 并把这 \(K\) 个词称为噪声词 \(w_k\) (\(k=1, ..., K\)) 。同时, 我们把 Softmax的多分类问题变成二分类问题, 即中心词预测上下文词的概率尽量接近1, 预测噪声词的概率尽量接近0, 因此, 似然函数可以表达如下:

```
\(\prod_{i=1}^{T} \prod_{-m \le j \le m} P(w^{(i+j)} | w^{(i)}) = \prod_{i=1}^{T} \prod_{-m \le j \le m} P(D=1 | w^{(i)}, w^{(i+j)}) \prod_{k=1, w_k \sim P(w)}^k P(D=0 | w^{(i)}, w_k)\)
```

似然函数最大化等价于log损失函数的最小化:

```
\(- \sum_{(c, w_o)} (\log\sigma(u_a^T v_c) + \sum_{k=1, v_k \sim P(w)}^K \log\sigma(-u_{w_k}^T v_c))\)
```

同时我们还需关注正负样本的采样方法, 在大型的语料中, 往往一些代词、助词等出现的频率最高, 但提供的信息却最少, 我们不希望这类词过多地进入样本, 因此通过基于词频的降采样方式来控制, 用函数counter ( \(w\) ) 表示词 \(w\) 在训练语料中的计数, 根据训练语料的实际情况设置词频

```
\(f(w) = \frac{counter(w)}{\sum counter(u)}\)
```

占比的阈值 \(t\) , 对于词频占比