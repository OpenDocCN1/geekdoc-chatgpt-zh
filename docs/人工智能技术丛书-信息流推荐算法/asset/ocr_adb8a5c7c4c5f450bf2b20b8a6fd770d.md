```
$$r^{\text{PV}} += p^{\text{Expose}} \cdot p_{c_i}^{\text{NEXT}} \tag{7-77}$$
$$r^{\text{IPV}} += p^{\text{Expose}} \cdot p_{c_i}^{\text{CTR}} \tag{7-78}$$
```

式中， p Expose 的初始值为1.0，迭代过程中以如下的方式更新：

```
$$p^{\text{Expose}} \cdot = p_{c_i}^{\text{NEXT}} \tag{7-79}$$
```

点击率和继续浏览率由两个point-wise的模型 M CTR （ v|u ; Θ CTR ）和 M NEXT （ v|u ; Θ NEXT ）输出得到，这两个模型的定义如下：

```
$$\hat{y}_i^{\text{CTR}} = M^{\text{CTR}}(v \mid u; \boldsymbol{\Theta}^{\text{CTR}}) = \sigma(f(f(f(\mathbf{x}_r \oplus \mathbf{x}_u)))) \tag{7-80}$$
$$\hat{y}_i^{\text{NEXT}} = M^{\text{NEXT}}(v \mid u; \boldsymbol{\Theta}^{\text{NEXT}}) = \sigma(f(f(f(\mathbf{x}_r \oplus \mathbf{x}_u)))) \tag{7-81}$$
```

式中， σ 为Sigmoid激活函数； xv 和 xu 分别表示物品和用户的特征表示； ⊕ 表示拼接操作； 多层全连接 f （ x ）=RELU （ Wx+b ）。两个模型均采用交叉熵损失函数进行优化：

```
$$\mathcal{L}^{\text{CTR}} = -\frac{1}{N} \sum_{(u,v) \in \mathcal{R}_{t_i}^{\prime} \in \mathcal{V}} \sum (y_i^{\text{CTR}} \log \hat{y}_i^{\text{CTR}} + (1-y_i^{\text{CTR}}) \log(1-\hat{y}_i^{\text{CTR}})) \tag{7-82}$$
$$\mathcal{L}^{\text{NEXT}} = -\frac{1}{N} \sum_{(u,v) \in \mathcal{R}_{t_i}^{\prime} \in \mathcal{V}} \sum (y_i^{\text{NEXT}} \log \hat{y}_i^{\text{NEXT}} + (1-y_i^{\text{NEXT}}) \log(1-\hat{y}_i^{\text{NEXT}})) \tag{7-83}$$
```

有了序列的评价方法之后，就可以根据Beam Search算法生成候选的序列。

图7-17所示为基于Beam Search的序列生成方法。假设物品集包含4个物品，每一个物品都已经计算好点击率和继续浏览率， Beam Search中每次保留的候选集大小 k 设为2。初始候选集为空， 第一轮迭代中，4个物品分别构成一个序列，对这4个序列分别计算奖励，选择Top 2的序列来构成候选集。第二轮迭代中以候选集中的序列出发，生成长度为2的6个序列，然后分别计算奖励，选择 Top 2的序列作为新的候选集。不断循环下去，直到序列长度达到阈值。