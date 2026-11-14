Tanh 激活函数同样存在“梯度饱和和效应”问题，即 Tanh 激活函数两端梯度也都趋于 0，因此在使用误差反向传播算法进行网络训练时，该区域的误差也无法传递到前一层，从而导致网络训练失败。

# 3. ReLU 激活函数

为了在一定程度上避免“梯度饱和和效应”问题，Vinod Nair 和 Geoffrey Hinton 提出修正线性单元（Rectified Linear Unit，ReLU）激活函数[29]，其函数定义为

ReLU(x) = \begin{cases} x & x \geqslant 0 \\ 0 & x < 0 \end{cases}

ReLU 激活函数及其梯度如图 2-14 所示。

![](bbox=[0.12, 0.32, 0.44, 0.46])

![](bbox=[0.51, 0.32, 0.84, 0.46])

图 2-14 ReLU 激活函数及其梯度

与 Sigmoid 激活函数相比，ReLU 在 x ⩾ 0 部分消除了“梯度饱和和效应”，且 ReLU 的计算更简单，计算速度更快。但 ReLU 本身也存在缺陷，如果输入为负值，其梯度等于 0，导致“神经元死亡”，将无法进行权重更新，进而无法完成网络训练。即便如此，ReLU 仍然是当前深度学习领域中最为常用的激活函数之一。

# 4. PReLU

为了解决 ReLU 激活函数存在的“神经元死亡”问题，人们又提出了参数化的 ReLU（Parametric ReLU，PReLU）激活函数，其定义为

f(x) = \begin{cases} \alpha x, & x < 0 \\ x, & x \geqslant 0 \end{cases}, x \in (-\infty, +\infty)

PReLU 激活函数及其梯度如图 2-15 所示。

![](bbox=[0.12, 0.77, 0.44, 0.9])

![](bbox=[0.51, 0.77, 0.84, 0.9])

图 2-15 PReLU 激活函数（α=0.2）及其梯度