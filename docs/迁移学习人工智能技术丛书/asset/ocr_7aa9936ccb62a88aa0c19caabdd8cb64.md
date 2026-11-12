制门 \( o_{n,t}^s \)，每个词的个性化控制门由训练数据中学习得到。

给定第 \( n \) 个回答的嵌入向量 \( h_{n}^{-s} \)，以及预测词语 \( \hat{y}_{n,0}^s \) 的初始状态 \( h_{n,0}^{s,d} \)，该初始状态计算如下：

```
\(\begin{aligned} h_{n,0}^{s,g} &= h_{n,0}^{s,d}, h_{n,0}^{c} = h_{n,0}^{s,d}, o_{n,0}^s = 0 \\ \hat{y}_{n,0}^s &= 0, \hat{y}_{n,0}^{s^-} = 0 \end\aligned}\)
```

其中 \( h_{n}^{s,g} \) 是共享组件的隐状态，\( \hat{y}_{n,t}^{s-c} \) 记录了由共享组件生成的最后一个词语，\( h_{n,t}^s \) 是个性化组件的隐状态，以及 \( h_{n,t}^{s-d} \) 是用于生成词语 \( \hat{y}_{n,t}^s \) 的隐状态。

共享组件使用 GRU 模型来捕获被所有用户共享的长期依赖信息。特别地，在每个时间步 \( t \)，

共享组件定义为

```
\(\begin{aligned} z_{n,t}^{s,g} &= \sigma(W_z h_{n,t-1}^{s,g} + U_z \hat{y}_{n,t-1}^{s,g} + V_z h_n^{s,c} + b_z) \quad (18.5) \\ r_{n,t}^{s,g} &= \sigma(W_r h_{n,t-1}^{s,g} + U_r \hat{y}_{n,t-1}^{s,g} + V_r h_n^{s,c} + b_r) \quad (18.6) \\ \tilde{h}_{n,t}^{s,g} &= \sigma(W_h (r_{n,t}^{s,g} \odot h_{n,t-1}^{s,g}) + U \hat{y}_{n,t-1}^{s,g} + V_s h_n^{s,c} + b_h) \quad (18.7) \\ h_{n,t}^{s,g} &= z_{n,t}^{s,g} \odot h_{n,t-1}^{s,g} + (1 - z_{n,t}^{s,g}) \odot \tilde{h}_{n,t}^{s,g} \quad (18.8) \end\aligned}\)
```

其中 \( \odot \) 代表向量或矩阵间的点积，\( \sigma(\cdot) \) 是 sigmoid 函数，\( z_{n,t}^{s,g} \) 是更新门，\( r_{n,t}^{s,g} \) 是遗忘门，\( \tilde{h}_{n,t}^{s,g} \) 是暂定更新隐状态。如果第 \( t \) 个词是一个共享词语（即 \( \hat{o}_{n,t}^s = 0 \)），则模型会像往常一样更新共享隐状态和最后的通用词语，否则 \( h_{n,t}^{s,g} \) 和 \( \hat{y}_{t}^{s-c} \) 将保持不变。因此 \( h_{n,t}^{s-c} \) 和 \( \hat{y}^{s-c} \) 可以按如下公式更新：

```
\(\begin{aligned} h_{n,t}^{s-c} &= (1 - \hat{o}_{n,t}^s) \odot \tilde{h}_{n,t}^{s-c} + \hat{o}_{n,t}^s \odot h_{n,t-1}^{s-c} \quad (18.9) \\ \hat{y}_{n,t}^{s-c} &= (1 - \hat{o}_{n,t}^s) \odot \hat{y}_{t-1}^{s-c} + \hat{o}_{n,t}^s \odot \hat{y}_{n,t-1}^{s-c} \quad (18.10) \end\aligned}\)
```

个性化组件是一个 RNN 模型，它可以从共享组件中生成基于句子上下文 \( h_{n,t}^{s,f} \) 的序列。针对每个用户都有一个独立的 RNN 模型。在每个时间步 \( t \)，个性化组件接收 \( \hat{y}_{t-1}^u \)、\( \hat{o}_{n,t}^u \)、\( h_{n,t-1}^u \) 和 \( h_{n,t}^{s,f-1} \) 为输入，输出 \( \hat{h}_{n,t}^u \)，其定义如下：

```
\(\hat{h}_{n,t}^u = \sigma(W^u h_{n,t-1}^u + U^u \hat{y}_{n,t-1}^u + V^u h_{n,t-1}^{s,f-1}) \quad (18.11)\)
```

个性化隐状态将按如下公式更新：

```
\(h_{n,t}^u = (1 - \hat{o}_{n,t}^u) \odot h_{n,t}^{s,f} + \hat{o}_{n,t}^u \odot \hat{h}_{n,t}^u \quad (18.12)\)
```

如果控制门 \( \hat{o}_{n,t}^u = 1 \)，则 \( h_{n,t}^u \) 等于 \( \hat{h}_{n,t}^u \)；如果 \( \hat{o}_{n,t}^u = 0 \)，模型则会将 \( h_{n,t}^{s,f} \) 的值赋给 \( h_{n,t}^u \)。

个性化控制门 \( o_{n,t}^u \) 是二元的，即 \( o_{n,t}^u \in \{0, 1\} \)。在 \( t \) 时刻，预测控制门 \( \hat{o}_{n,t}^u \) 是 \( \hat{o}_{n,t-1}^u \)、\( h_{n,t-1}^{s,f} \)、\( h_{n,t-1}^u \) 和 \( h_{n,t-1}^{u} \) 的函数 \( \odot \)：