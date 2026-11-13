| 名称 | 表达式与参数 |
| :--- | :--- |
| 线性核 (linear kernel) | $\kappa(x_i, x_j) = x_i^T x_j$ |
| 多项式核 (polynomial kernel) | $\kappa(x_i, x_j) = (x_i^T x_j)^d$，其中，$d \geqslant 1$，为多项式的次数 |
| 高斯核 (Gaussian kernel) | $\kappa(x_i, x_j) = \exp\left(-\frac{\|x_i - x_j\|^2}{2\sigma^2}\right)$，其中，$\sigma > 0$ 为高斯核的带宽 |
| 拉普拉斯核 (Laplacian kernel) | $\kappa(x_i, x_j) = \exp\left(-\frac{\|x_i - x_j\|}{\sigma}\right)$，其中，$\sigma > 0$ |