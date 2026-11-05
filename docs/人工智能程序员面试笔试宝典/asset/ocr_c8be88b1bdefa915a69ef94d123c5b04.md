$O_k^t = \sum_{h'=1}^{H} V_{hk} s_h^t$

根据前向传播的公式就可以看到RNN与BP模型的区别，即在$t$时刻隐藏层的输入上，除了来自$t$时刻的输入，还有来自$t-1$时刻隐藏层的信息。

# （2）反向传播

RNN的反向传播采用的是BPTT的方法，其残差的公式如下：

$\delta_h^t = f(a_h^t)\left(\sum_{k=1}^{K} v_{hk} \delta_k^t + \sum_{h'=1}^{H} w_{h'h} \delta_{h'}^{t+1}\right)$

值得注意的是，整个RNN网络共享同一组参数。

$t$时刻损失函数对于参数的求导公式分别如下：

$\frac{\partial E}{\partial w_{h'h}} = \frac{\partial E}{\partial a_h^t} \frac{\partial a_h^t}{\partial w_{h'h}} = \delta_h^t \delta_{k'}^{t-1}$

$\frac{\partial E}{\partial u_{ih}} = \frac{\partial E}{\partial a_h^t} \frac{\partial a_h^t}{\partial u_{ih}} = \delta_h^t x_i^t$

$\frac{\partial E}{\partial v_{hk}} = \delta_k^t s_h^t$

而根据BPTT的算法，RNN的参数导数为各时间点上导数之和：

$\frac{\partial E}{\partial w_{h'h}} = \sum \frac{\partial E}{\partial a_h^t} \frac{\partial a_h^t}{\partial w_{h'h}} = \sum \delta_h'^t s_{k'}^{t-1}$

$\frac{\partial E}{\partial u_{ih}} = \sum \frac{\partial E}{\partial a_h'^t} \frac{\partial a_h^t}{\partial u_{ih}} = \sum \delta_h^t x_i^t$

$\frac{\partial E}{\partial v_{hk}} = \sum \delta_k^t s_h^t$

# 4.3.2 RNN的梯度消失问题以及代码展示