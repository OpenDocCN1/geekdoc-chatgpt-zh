![](bbox=[0.143, 0.091, 0.871, 0.415])

图3-36 GRU单元结构

$r_t=\sigma\left(W_r \cdot\left[h_{t-1}, x_t\right]\right)$ (3-91)

$u_t=\sigma\left(W_u \cdot\left[h_{t-1}, x_t\right]\right)$ (3-92)

![](bbox=[0.393, 0.531, 0.441, 0.561])

式中，Reset门（ $r_t$ ）用来计算隐层 ，代表的业务含义是用户在当前时刻兴趣的变化点（新信号），它由前一时刻的兴趣表达及当前行为物品通过Reset门控制决定：

$\widetilde{h}_t=\tanh W \cdot\left[r_t \cdot h_{t-1}, x_t\right]$ (3-93)

Update门（ $u_t$ ）用来计算当前时刻输出的兴趣向量，并控制当前兴趣有多少来自上一时刻的兴趣，有多少来自这一时刻的变化点：

$h_t=\left(1-u_t\right) \cdot h_{t-1}+u_t \cdot\widetilde{h}_t$ (3-94)

最终，用户当前时刻的兴趣向量输出为 $h_t$。

在基础的LSTM / GRU方法之上，我们同样可以借鉴Target Attention的思路，显式地学习在用户兴趣随着时序演化的过程中每个时刻的兴趣对最终的兴趣表达的不同权重的影响，典型算法有深度兴趣进化网络（Deep Interest Evolution Network, DIEN） [19] ，其模型结构如图3-37所示，网络结构的核心部分是通过用户行为为序列输入层（Behavior Layer）、兴趣抽取层（Interest Extractor Layer）、兴趣进化层（Interest Evolving Layer）来学习得到用户的最终兴趣表达。