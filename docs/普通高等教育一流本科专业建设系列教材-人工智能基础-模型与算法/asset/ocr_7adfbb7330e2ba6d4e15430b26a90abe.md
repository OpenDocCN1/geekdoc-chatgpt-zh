$$\min J = \frac{1}{2} \sum_{j=1}^{P_m} (y_j^m - y_{sj})^2$$ (7.60)

即选择神经网络权值，使期望输出 \(y_{sj}\) 与神经网络实际输出 \(y_j^m\) 之差的平方和最小。

这种学习算法实际上是求目标函数 \(J\) 的极小值，约束条件是式 (7.59)，可以利用非线性规划中的“最快下降法”，使权值沿目标函数的负阶梯方向改变。因此，神经网络权值的修正量为

$$\Delta w_{ij}^{k-1} = -\varepsilon \frac{\partial J}{\partial w_{ij}^{k-1}}, \quad \varepsilon > 0$$ (7.61)

式中，\(\varepsilon\) 为学习步长，一般小于 0.5。

下面推导 BP 学习算法。先求 \(\frac{\partial J}{\partial w_{ij}^{k-1}}\)，过程如下：

$$\frac{\partial J}{\partial w_{ij}^{k-1}} = \frac{\partial J}{\partial u_i^k} \frac{\partial u_i^k}{\partial w_{ij}^{k-1}} = \frac{\partial J}{\partial u_i^k} \frac{\partial}{\partial w_{ij}^{k-1}} \left( \sum_j w_{ij}^{k-1} y_j^{k-1} \right) = \frac{\partial J}{\partial u_i^k} y_j^{k-1}$$ (7.62)

记

$$d_i^k = \frac{\partial J}{\partial u_i^k}, \quad k=3,4,\cdots,m$$ (7.63)

则

$$\Delta w_{ij}^{k-1} = -\varepsilon d_i^k y_j^{k-1}, \quad k=2,3,\cdots,m$$ (7.64)

接下来推导计算 \(d_i^k\) 的公式：

$$d_i^k = \frac{\partial J}{\partial u_i^k} = \frac{\partial J}{\partial y_i^k} \frac{\partial y_i^k}{\partial u_i^k} = \frac{\partial J}{\partial y_i^k} f'_k(u_i^k)$$ (7.65)

下面分两种情况求 \(\frac{\partial J}{\partial y_i^k}\)。

1) 对输出层（第 \(m\) 层）的神经元，即 \(k=m, y_i^k = y_i^m\)，由误差定义式得

$$\frac{\partial J}{\partial y_i^k} = \frac{\partial J}{\partial y_i^m} = y_i^m - y_{si}$$ (7.66)

则

$$d_i^m = (y_i^m - y_{si}) f'_m(u_i^m)$$ (7.67)

2) 若 \(i\) 为隐单元层 \(k\)，则有

$$\frac{\partial J}{\partial y_i^k} = \sum_l \frac{\partial J}{\partial u_l^{k+1}} \frac{\partial u_l^{k+1}}{\partial y_i^k} = \sum_l d_l^{k+1} w_{li}^k$$ (7.68)

则

$$d_i^k = f'_k(u_i^k) \sum_l d_l^{k+1} w_{li}^k$$ (7.69)

综上所述，BP 学习算法可以归纳为

$$\Delta w_{ij}^{k-1} = -\varepsilon d_i^k y_j^{k-1}$$ (7.70a)
$$d_i^m = (y_i^m - y_{si}) f'_m(u_i^m)$$ (7.71a)
$$d_i^k = f'_k(u_i^k) \sum_l d_l^{k+1} w_{li}^k, \quad k=m-1, m-2, \cdots, 2$$ (7.72a)