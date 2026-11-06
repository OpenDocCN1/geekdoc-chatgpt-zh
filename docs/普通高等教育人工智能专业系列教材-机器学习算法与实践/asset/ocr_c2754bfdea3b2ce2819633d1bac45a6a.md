# 2. 多分类问题

# (1) 投影

对于多分类问题，设类别数为 $(N<n)$，LDA 将样本投影到超平面（即 $N-1$ 维子空间）上。某超平面的一组标准基正交基为 $w_1,w_2,\cdots,w_{N-1}$，其中 $w_i(i=1,2,\cdots,N-1)\in\mathbf{R}^n$。记 $\mathbf{W}=[w_1,w_2,\cdots,w_{N-1}]$，则任意 $x\in\mathbf{R}^n$ 到上述超平面（子空间）的投影为

$x' = \mathbf{W}^\mathrm{T}x = [w_1^\mathrm{T}x,w_2^\mathrm{T}x,\cdots,w_{N-1}^\mathrm{T}x]^\mathrm{T}$

# (2) 类内散度

设数据集 $D=[(x_1,y_1),(x_2,y_2),\cdots,(x_m,y_m)]$，其中 $x_i\in\mathbf{R}^n(i=1,2,\cdots,m)$，$y_i\in\{0,1\}$。记 $X_j(j=1,2,\cdots,N)$ 为第 $j$ 类样本的集合，$|X_j|(j=1,2,\cdots,N)$ 表示第 $j$ 类样本的个数，$u_j(j=1,2,\cdots,N)$ 为第 $j$ 类样本的中心（均值），$\Sigma_j(j=1,2,\cdots,N)$ 表示第 $j$ 类样本的协方差矩阵。对于类内散度 $S_w$ 矩阵重新定义为每个类别的协方差矩阵之和：

$S_w := \sum_{j=1}^{N}\Sigma_j$

那么，类似于二分类问题，投影后的样本中心为

$u'_j = \frac{1}{|X_j|}\sum_{x\in X_j}\mathbf{W}^\mathrm{T}x = \mathbf{W}^\mathrm{T}u_j(j=1,2,\cdots,N)$

类似于二分类，投影后的第 $j(j=1,2,\cdots,N)$ 类样本的方差为

$\sigma_j^2 = \sum_{l=1}^{N-1}w_l^\mathrm{T}\Sigma_j w_l$

每个类别的方差和为

$\sum_{j=1}^{N}\sigma_j^2 = \sum_{j=1}^{N}w_l^\mathrm{T}\sum_{l=1}^{N-1}\Sigma_j w_l = \sum_{l=1}^{N-1}w_l^\mathrm{T}S_w w_l = \mathrm{tr}(\mathbf{W}^\mathrm{T}S_w\mathbf{W})$

式中，$\mathrm{tr}(\cdot)$ 表示矩阵的迹（trace）。因此，最小化投影后类内方差，即最小化 $\mathrm{tr}(\mathbf{W}^\mathrm{T}S_w\mathbf{W})$。

# (3) 类间散度

设全局样本中心为 $u$，即

$u = \frac{1}{m}\sum_x x$

我们首先将全局协方差矩阵，定义为“全局散度矩阵”

$S_t := \sum_{i=1}^{m}(x_i - u)(x_i - u)^\mathrm{T}$

再定义类间散度为

$S_b := S_t - S_w = \sum_{j=1}^{N}|X_j|(u_j - u)(u_j - u)^\mathrm{T}$

对于多分类问题，对投影后不同类别的“远离”程度的度量方式有多种，这里利用

$\sum_{l=1}^{N-1}w_l^\mathrm{T}S_b w_l = \mathrm{tr}(\mathbf{W}^\mathrm{T}S_b\mathbf{W})$

因此，最大化投影后类间“距离”即最大化 $\mathrm{tr}(\mathbf{W}^\mathrm{T}S_b\mathbf{W})$。

# (4) LDA 模型（多分类）

将上述类内散度与类间散度结合，得到多分类问题 LDA 的目标函数