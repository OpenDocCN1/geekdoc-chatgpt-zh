循环神经网络起源于著名物理学家 John Joseph Hopfield 于 1982 年提出的 Hopfield 神经网络[1]。Hopfield 神经网络具有递归特性，每一个神经元的输入来自其他所有神经元，它的输出又传递给其他所有的神经元，但自身没有反馈连接。

1986 年，Michael I. Jordan 提出了 Jordan Network[2]，它是第一个真正意义上的循环神经网络，实现了序列数据的递归，从而开启了循环神经网络的研究与发展。

1990 年，Jeffrey L. Elman 提出了 Elman Network[3]，他对 Jordan Network 进行了改进，简化了它的结构，成为当今循环神经网络的主流形式。

同样是 1990 年，Paul Werbos 提出了循环神经网络的训练算法——基于时间的反向传播算法（Backward Propagation Through Time，BPTT）[4]，它在人工神经网络的反向传播算法的基础上实现了循环神经网络的有效训练。

1997 年，Jurgen Schmidhuber 和 Sepp Hochreiter 提出了长短期记忆网络（Long-Short Term Memory，LSTM）[5]，虽然 LSTM 在当时并没有引起工业界的关注，但是在 2010 年以后，随着深度学习的发展，LSTM 得到了大规模应用。

同样在 1997 年，Mike Schuster 与 Kuldip K. Paliwal 提出了双向循环神经网络（Bi-directional RNN，BRNN）[6]，他们认为，当前的输出不仅与之前的序列有关，还与之后的序列有关。BRNN 由两个循环神经网络上下叠加在一起组成，输出由这两个循环神经网络的隐藏层的状态决定。

2014 年，Junyoung Chung 等人提出了门限循环单元（Gated Recurrent Unit，GRU）[7]，它简化了 LSTM 的结构，在多个任务上达到了与 LSTM 相媲美的结果。

同样在 2014 年，Alex Graves 等人提出了神经图灵机（Neural Turing Machine，NTM）[8]，神经图灵机引入了外部存储，增大了记忆容量。

2015 年，Armand Joulin 与 Tomas Mikolov 提出了堆叠循环神经网络（Stacked Recurrent Neural Network，Stacked RNN）[9]，它将多个循环神经网络堆叠成多层循环神经网络，上一层循环神经网络的输入为下一层循环神经网络的输出，Stack RNN 在一些自然语言处理任务（如机器翻译）上有很好的表现。

# 3.2 循环神经网络的训练

循环神经网络是一种人工神经网络，神经元之间的连接形成一个遵循时间序列的有向图。输入样本之间存在顺序关系，即每个样本和它之前的样本存在关联，通过神经元在时序上的展开，就可以找到样本之间的这些序列相关性。

RNN 的一般结构如图 3-2 所示。

如图 3-2 所示，RNN 的计算公式如下：

```
$h_t = \sigma(Ux_t + Wh_{t-1} + b_h,)$ (3-1)

\(\hat{y}_t = \varphi(Vh_t + b_y)\) (3-2)
```

其中：