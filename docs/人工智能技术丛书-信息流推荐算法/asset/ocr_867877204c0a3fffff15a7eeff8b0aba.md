# 算法 5-1 置信域算法

+   0: 给定 w^0
+   1: for k=0, 1, ... (外循环迭代次数)
+   2: 如果 \nabla f(w^k) = 0, 停止
+   3: 找到置信域子问题的一个近似解 s^k
+      \min_{s} q_k(s) \text{ subject to } \| s \| \leqslant \Delta_k
+   4: 根据式 (5-15) 计算 \rho_k
+   5: 根据式 (5-16) 更新 w^{k+1}
+   6: 根据式 (5-17) 获得 \Delta_{k+1}

内循环的部分对应共轭梯度的求解部分：

# 算法 5-2 共轭梯度求解置信域子问题

+   0: 给定 \xi_k < 1, \Delta_k > 0, 令 s^0 = 0, r^0 = -\nabla f(w^k), d^0 = r^0
+   1: for i = 0, 1, ... (内循环迭代次数)
+   2: 如果 \| r^i \| = \| \nabla f(w^k) + \nabla^2 f(w^k) s^i \| \leqslant \xi_k \| \nabla f(w^k) \|
+      然后输出 s^k = s^i, 并停止
+   3: \alpha_i = \| r^i \|^2 / ( (d^i)^T \nabla^2 f(w^k) d^i )
+   4: s^{i+1} = s^i + \alpha_i d^i
+   5: 如果 \| s^{i+1} \| \geqslant \Delta_k, 则计算 \tau 以满足 \| s^i + \tau d^i \| = \Delta_k
+      输出 s^k = s^i + \tau d^i, 并停止
+   6: r^{i+1} = r^i - \alpha_i \nabla f(w^k) d^i
+   7: \beta_i = \| r^{i+1} \|^2 / \| r^i \|^2
+   8: d^{i+1} = r^{i+1} + \beta_i d^i

实际使用中可以选择多核版本的LibLinear，也可以使用Spark版本的LibLinear，都可以支持百万级的样本和特征。

# 5.2.2 FM

FM可以看成特征交叉方向上的LR进阶版本，本小节首先介绍FM的相关基础，然后介绍FM的升级算法FFM [5] 。

# (1) FM基础: 处理稀疏、大规模场景

FM是推荐领域中一个比较全能的算法，既可以用于排序，也可以用于召回，相关的扩展算法也比较多。这里首先看一下二阶FM的计算公式：

```
\hat{y}(x) := w_0 + \sum_{i=1}^n w_i x_i + \sum_{i=1}^{n-1} \sum_{j=i+1}^n \langle v_i, v_j \rangle x_i x_j (5-18)
```

式中，w_0 是全局的偏置；w_i 表示特征 x_i 的权重；用 v_i 和 v_j 的内积来表示 x_i 和 x_j 之间的相关性权重 w_{ij}。值得注意的是，FM没有直接学习 w_{ij}，而是学习 x_i 和 x_j 对应的向量 v_i 和 v_j。从矩阵的角度来看，就是做了一个常用的正定矩阵分解 W = VV^T。分解的维度越大，学习复杂相关性的能力越强。

表5-1 稀疏推荐场景的样本示例