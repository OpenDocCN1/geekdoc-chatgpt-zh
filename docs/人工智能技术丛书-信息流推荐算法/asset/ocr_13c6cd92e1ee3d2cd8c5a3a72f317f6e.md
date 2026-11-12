![](bbox=[0.135, 0.09, 0.875, 0.32])

# 图3-48 短期行为兴趣模型

首先，通过LSTM层挖掘用户会话中兴趣的演化。这里以消费者在电商的购物路径做类比，通常购物决策是从广泛地选品、比价到逐步聚焦到某几个品牌、价位，最终做出购买决策的过程，我们希望LSTM能捕捉决策过程中细节信号的变化。用户 u 在 t 时刻的交互物品 i 的原始稀疏特征为

[黑框] [黑框] [黑框] , 经过Embedding层得到 [黑框] 向量，那么基于LSTM，用户 u 在 t 时刻的兴趣向量可以通过如下公式进行建模：

```
i_t^u = \sigma(W_{in}^1 e_{i_t^u} + W_{in}^2 h_{t-1}^u + b_{in}) \quad (3-109)
f_t^u = \sigma(W_{f}^1 e_{i_t^u} + W_{f}^2 h_{t-1}^u + b_f) \quad (3-110)
o_t^u = \sigma(W_{o}^1 e_{i_t^u} + W_{o}^2 h_{t-1}^u + b_o) \quad (3-111)
c_t^u = f_t^u c_{t-1}^u + i_t^u \tanh(W_{c}^1 e_{i_t^u} + W_{c}^2 h_{t-1}^u + b_c) \quad (3-112)
h_t^u = o_t^u \tanh(c_t^u) \quad (3-113)
```

式中，[黑框] ... 分别为输入门、遗忘门、输出门；[黑框] 为 t 时刻 LSTM单元输出，即 t 时刻的用户兴趣向量。

接着，我们通过增加Multi-Head Self-Attention层，对LSTM层的输出 [黑框] , 即对用户在每个时刻的兴趣向量进行降噪处理：

![](bbox=[0.135, 0.74, 0.5, 0.77])

```
\text{head}^u = \text{Attention}(W_i^0 X^u, W_i^K X^u, W_i^V X^u) \quad (3-114)
\hat{X}^u = W^o \text{concat}(\text{head}^u, \cdots, \text{head}^u) = [\hat{h}_1^u, \cdots, \hat{h}_t^u] \quad (3-115)
```

另外，我们在Target Attention中也介绍过，不同类型的消费者对物品的不同细节会有不同的敏感度，比如有些消费者更看重品牌，有些消费者则更看中性价比，在视频消费中，有些用户容易被明星吸引，有些用户则更容易被故事情节吸引，因此将用户属性特征与行为进行User Attention，能为模型带来的信息增益如下：