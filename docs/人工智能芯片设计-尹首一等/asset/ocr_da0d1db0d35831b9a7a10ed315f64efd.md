# 1) Sigmoid 函数

Sigmoid 函数也称 Logistic 函数，其函数定义为

f(x) = \frac{1}{1 + e^{-x}} (2-3)

它近似为线性函数，值域为[0,1]，当输入值靠近两端时，对输入进行抑制，趋向饱和。Sigmoid 函数主要有三个缺点：一是其输出恒为正值，不是以零点对称的，导致权值只能朝一个方向更新，使得收敛缓慢；二是需要进行指数计算，比较耗费计算资源；三是其饱和特性导致梯度消失，会使得网络变得很难学习。

# 2) tanh 函数

tanh 函数是 Sigmoid 函数的改进版，和 Sigmoid 函数有异曲同工之妙，都是研究早期被广泛使用的 2 种激活函数。

f(x) = tanh(x) (2-4)

它由双曲正弦 sinh 和双曲余弦 cosh 相除得到，其值域为[-1,1]，克服了 Sigmoid 函数非 0 均值的缺点，不容易出现 loss 值晃动，收敛速度快。但是它和 Sigmoid 函数一样，都是饱和型函数，会导致梯度消失的问题。而且，2 个函数的计算量都是指数级的，计算相对复杂。

# 3) tanh 线性逼近函数

tanh 线性逼近函数是 Sigmoid 函数的简化，主要目的是将 Sigmoid 的非线性区域线性化，常用于硬件电路的设计实现中，它的表达式为

f(x) = \begin{cases} -1, & x < -1 \\ x, & -1 \le x \le 1 \\ 1, & x > 1 \end{cases} (2-5)

# 4) ReLU 函数

ReLU 是修正线性单元（Rectified Linear Unit）的缩写，是 Krizhevsky、Hinton 等在 2012 年的论文 ImageNet classification with deep convolutional neural networks 中提出的一种线性且不饱和的激活函数，其特点是更为接近生物神经的开关阈值状态，表达式为

f(x) = max(0, x) (2-6)

ReLU 函数只有线性关系，不需要指数计算，无论前向传播还是反向传播，计算速度都比 Sigmoid 函数和 tanh 函数快。而且它具有很好的稀疏性，显著减少了计算量。此外，当 x > 0 时，其导数为 1，在一定程度上缓解了神经网络的梯度