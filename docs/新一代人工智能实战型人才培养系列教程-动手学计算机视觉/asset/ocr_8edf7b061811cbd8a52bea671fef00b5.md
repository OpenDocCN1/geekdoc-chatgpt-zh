的运动速度也相似。这意味着，如果我们考虑一个特征点及其邻域内的点，我们可以假设这些点在 x 轴和 y 轴方向上的速度（ u 和 v ）是相同的。

以图 17-5 为例，在一个邻域内，如方框标注的区域内，所有光流向量可以认为是一致的。因此，通过在这样的特征区域内应用光流方程，我们可以联立多个方程来求解该区域内的统一速度 u 和 v。

![](bbox=[0.545, 0.122, 0.908, 0.23])

具体而言，假定使用一个 5×5 的窗口作为空间一致性假设区域，那么在这个窗口中的每个像素都可以计算一次光流方程，总共能够得到 25 个方程，即

```
\(\begin{bmatrix} I_x(p_1) & I_y(p_1) \\ I_x(p_2) & I_y(p_2) \\ \vdots & \vdots \\ I_x(p_{25}) & I_y(p_{25}) \end{bmatrix} \begin{bmatrix} u \\ v \end{bmatrix} = - \begin{bmatrix} I_t(p_1) \\ I_t(p_2) \\ \vdots \\ I_t(p_{25}) \end{bmatrix}\)
```

为了描述方便，简写为

```
\( A d = -b \rightarrow \min \| A d + b \|^2 \)
```

显然，这又是一个关于 u, v 的超定线性方程，传统上是使用最小二乘法求解：

```
\(\begin{bmatrix} \sum I_x I_x & \sum I_x I_y \\ \sum I_x I_y & \sum I_y I_y \end{bmatrix} \begin{bmatrix} u \\ v \end{bmatrix} = - \begin{bmatrix} \sum I_x I_t \\ \sum I_y I_t \end{bmatrix}\)
```

也就是

```
\( A A^T \begin{bmatrix} u \\ v \end{bmatrix} = -A^T b \)
```

那么可以求解得

```
\(\begin{bmatrix} u \\ v \end{bmatrix} = -(A^T A)^{-1} A^T b \)
```

我们在第 6 章中介绍了 Harris 角点检测算法，对比一下，可以发现上式的形式非常像 Harris 算法，因此不难得出上式有解的条件：

+   (1) \( A^T A \) 可逆；
+   
+   (2) \( A^T A \) 的特征值 \( \lambda_1, \lambda_2 \) 不能太小；
+   
+   (3) \( A^T A \) 是良态（well conditioned）矩阵，对数据的微小扰动敏感，即 \( \lambda_1 \) 和 \( \lambda_2 \) 应该尽量接近。

符合这些条件的区域一般是角点区域，因此 Lucas-Kanade 光流法在角点处效果最好。

# 17.3.4 Lucas-Kanade 光流法的改进

Lucas-Kanade 光流法的约束条件就是它的三大假设：亮度恒定、微小移动和空间一致性。