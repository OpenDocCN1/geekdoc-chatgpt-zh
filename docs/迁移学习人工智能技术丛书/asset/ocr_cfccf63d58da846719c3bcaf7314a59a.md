其中，\(\lambda\) 是正则化参数。式（3.5）中目标函数的第一项是为了最小化被映射的源域和目标域之间的 MMD 距离。第二项最大化 \(\mathbf{K}\) 的迹，目的是保留新特征空间的方差，这与有色最大方差展开（Maximum Variance Unfolding，MVU）（Weinberger 等人，2004）的做法一致。第一项约束保留成对距离，第二个约束保证嵌入的数据居中。在解决问题（3.5）之后，可以在 \(\mathbf{K}\) 上应用主成分分析（Principal Component Analysis，PCA）获得主要特征向量来重构源域和目标域数据的期望映射。 MMDE 的一个缺点在于它使用直推式学习方法，不能泛化到样本外的数据。此外，式（3.5）的优化问题是一个半正定规划（Semi-Definite Programming，SDP）问题，该问题的计算成本很高。

# 3.2.1.2 迁移成分分析

为了克服 MMDE 的限制，Pan 等人提出了迁移成分分析（Transfer Component Analysis，TCA）。Pan (2010) 使用由经验得出核矩阵而不是重新学习核矩阵。具体来说，在 TCA 中，MMDE 中的核矩阵被分解为 \(\mathbf{K}=\tilde{\mathbf{K}}\mathbf{W}\mathbf{W}^{T}\tilde{\mathbf{K}}\)，其中 \(\mathbf{K}\) 为一个给定的经验核。\(\mathbf{W} \in \mathbb{R}^{n_s + n_t \times m}\)，其中 \(m << n_s + n_t\) 是要学习的项。这个优化问题形式如下：

```
\(\min_{\mathbf{W}} \text{tr}(\tilde{\mathbf{K}}\mathbf{W}\mathbf{W}^{T}\tilde{\mathbf{K}}\mathbf{L}) + \lambda \text{tr}(\mathbf{W}^{T}\mathbf{W})\)
\(\text{s. t. } \mathbf{W}^{T}\tilde{\mathbf{K}}\mathbf{H}\tilde{\mathbf{K}}\mathbf{W} = \mathbf{I}\)
```

其中，\(\mathbf{H} = \mathbf{I}_{n_s + n_t} - \frac{1}{n_s + n_t} \mathbf{1}\mathbf{1}^{T}\) 是一个中心化矩阵（centering matrix）。与 MMDE 类似，其目标函数中的第一项的目的是最小化被映射的源域和目标域数据间的 MMD 距离。第二项是一个关于 \(\mathbf{W}\) 的正则化项。约束条件是为了最大化映射后的数据方差。容易证明优化问题（3.6）具有闭合解，即 \(\mathbf{W}\) 包含了式子 \((\tilde{\mathbf{K}}\mathbf{L}\tilde{\mathbf{K}} + \lambda \mathbf{I})^{-1} \tilde{\mathbf{K}}\mathbf{H}\tilde{\mathbf{K}}\) 的 \(m\) 个主要特征向量。

和 MMDE 相比，TCA 避免了求解 SDP 问题，因此效率更高。此外，TCA 可以直接轻松地直接处理样本外数据。不同于 MMDE 首先学习核矩阵然后使用 PCA 来获得转换数据的两步走的特点，TCA 通过使用 \(\mathbf{W}\) 只需要一步就可以获得转换数据。

# 3.2.1.3 MMD 的深度架构

在深度学习的背景下，研究者提出使用深度神经网络来近似由核函数引起的特征映射 \(\varphi(\cdot)\)。例如，Tzeng 等人（2014）提出编码 MMD 来测量在卷积神经网络（Convolutional Neural Net-