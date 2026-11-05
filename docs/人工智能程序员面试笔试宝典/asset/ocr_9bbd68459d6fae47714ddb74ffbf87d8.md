设输入层为 $x^r = (x_1, x_2, \cdots, x_r)^T$，隐含层内包括 $n$ 个神经结点，激活函数为 $f_1$，输出层结点数为 $m$，激活函数为 $f_2$。输出向量为 $y^m = (y_1, y_2, \cdots, y_r)^T$，实际输出值为 $Y$，目标值是 $T$。

隐含层第 $i$ 个神经元输出为：
$$
\alpha_i = f_1\left(\sum_{j=1}^{r} w_{1ij} x_i + b_{1j}\right) \qquad j = 1,2,\cdots,n
$$

输出层第 $k$ 个结点输出为：
$$
y_k = f_2\left(\sum_{i=1}^{n} w_{2ki} \alpha_i + b_{2j}\right) \qquad k = 1,2,\cdots,m
$$

定义误差函数：
$$
L = \frac{1}{2} \sum_{k=1}^{m} (t_k - y_k)^2
$$

# 2. 误差反向传播

通过输出值与期望值的对比、反校，便可以确定输出的误差范围，从而将误差进一步反向传播到BP神经网络的隐含层，BP神经网络进行下一步的学习，直到输出误差满足要求。如需进行预测工作，只需要应用已经训练好的BP神经网络，将待预测时段的各影响因素输入神经网络，便可以得到相应预测结果。由此可见，BP神经网络具备一定的推理联想能力，对于在训练过程中未出现的情形，BP网络同样可以进行预测。

记学习率为 $\eta$，输出层权值的变化：
$$
\Delta w_{2ki} = \eta \cdot \frac{\partial L}{\partial w_{2ki}} = \eta \cdot \frac{\partial L}{\partial y_k} \cdot \frac{\partial y_k}{\partial w_{2ki}}
= \eta \cdot (t_k - y_k) \cdot f'_2 \cdot \alpha_i = \eta \cdot \delta_{ki} \cdot \alpha
$$

输出层阈值的变化：
$$
\Delta b_{2ki} = \eta \cdot \frac{\partial L}{\partial b_{2ki}} = \eta \cdot \frac{\partial L}{\partial y_k} \cdot \frac{\partial y_k}{\partial b_{2ki}}
= \eta \cdot (t_k - y_k) \cdot f'_2 = \eta \cdot \delta_{ki}
$$

隐含层权值的变化，对从第 $j$ 个输入到第 $i$ 个输出的权值变化：