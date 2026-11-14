从上式可看出，AdaDelta 不需要人工指定学习率。

# 5. Adam

动量法在 SGD 的基础上引入了一阶动量，AdaGrad、RMSProp 和 ADADELTA 则引入了二阶动量。Adam（Adaptive Moment）[13] 则同时利用了梯度的一阶动量和二阶动量，其参数更新公式如下：

```
x'=x-\epsilon \frac{\hat{m}_t}{\sqrt{\hat{s}_t}+c} \quad (7-63)

m_t=\beta_1 m_{t-1}+(1-\beta_1) \nabla_{x_j} f(x_j, t), \quad \hat{m}_t=\frac{m_t}{1-\beta_1^t} \quad (7-64)

s_t=\beta_2 s_{t-1}+(1-\beta_2) \nabla_{x_j} f(x_j, t)^2, \quad \hat{s}_t=\frac{s_t}{1-\beta_2^t} \quad (7-65)
```

其中，\(\beta_1\) 和 \(\beta_2\) 为非负超参数，默认设置 \(\beta_1=0.9\)、\(\beta_2=0.999\)，\(m_t\) 和 \(s_t\) 为参数梯度的一阶动量和二阶动量的指数移动平均值，即对参数梯度的一阶动量期望和二阶动量期望的估计；但是由于初始值 \(m_0\) 和 \(s_0\) 设置为 0，当 \(t\) 较小时存在较大的初始偏差，从而估计值并不准确。例如，当 \(\beta_1=0.9\) 时，\(m_1=0.1 \nabla_{x_j} f(x_j, 1)\)。为消除这样的影响，研究人员提出使用 \(m_t\) 和 \(s_t\) 的偏差修正值 \(\hat{m}_t\) 和 \(\hat{s}_t\) 对参数进行更新。

# 6. Nadam

Nadam（Nesterov Adam）[14] 在 Adam 的基础上引入了 NAG 的思想，引入“未来时刻”的梯度，首先将 Adam 中的 \(\hat{m}_t\) 展开，参数更新公式如下：

```
x'=x-\epsilon \frac{1}{\sqrt{\hat{s}_t}+c} \left( \frac{\beta_1 m_{t-1}}{1-\beta_1^t} + \frac{(1-\beta_1) \nabla_{x_j} f(x_j, t)}{1-\beta_1^t} \right) \quad (7-66)

x'=x-\epsilon \frac{1}{\sqrt{\hat{s}_t}+c} \left( \frac{\beta_1 m_t}{1-\beta_1^t} + \frac{(1-\beta_1) \nabla_{x_j} f(x_j, t)}{1-\beta_1^t} \right)

=x-\epsilon \frac{1}{\sqrt{\hat{s}_t}+c} \left( \beta_1 \hat{m}_t + \frac{(1-\beta_1) \nabla_{x_j} f(x_j, t)}{1-\beta_1^t} \right) \quad (7-67)
```

# 复习题

+   1. 请简述深度学习模型的训练和测试过程。
+   2. 请说明 Bagging 算法的思想并分析集成模型为什么会比单一模型效果好。
+   3. 请说明 Dropout 的工作原理。
+   4. 请简要介绍深度学习中常用的归一化方法。
+   5. 请简要介绍深度学习中常用的优化算法。