sin β = \sqrt{k_x^2 + k_y^2} \quad cos β = k_z (2-43)

注意到式（2-42）、式（2-43）是依据 k 为单位向量这一事实而得到的。将式（2-42）和式（2-43）代入式（2-41）中，经过计算，可以得到

$$ \boldsymbol{R}_{k,\theta} = \begin{bmatrix} k_x^2 \text{versin}\theta + \cos\theta & k_x k_y \text{versin}\theta - k_z \sin\theta & k_x k_z \text{versin}\theta + k_y \sin\theta \\ k_x k_y \text{versin}\theta + k_z \sin\theta & k_y^2 \text{versin}\theta + \cos\theta & k_y k_z \text{versin}\theta - k_x \sin\theta \\ k_x k_z \text{versin}\theta - k_y \sin\theta & k_y k_z \text{versin}\theta + k_x \sin\theta & k_z^2 \text{versin}\theta + \cos\theta \end{bmatrix} $$ (2-44)

式中， $\text{versin}\theta = 1 - \cos\theta$。

![](bbox=[0.335, 0.285, 0.625, 0.485])

图 2-12 关于一个任意轴线的旋转

实际上，任意一个旋转矩阵 $\boldsymbol{R} \in SO(3)$，可以用绕空间中某个适当轴线转过适当角度的单个旋转来表示，即

$\boldsymbol{R} = \boldsymbol{R}_{k,\theta}$ (2-45)

式中， $\boldsymbol{k}$ 是定义转轴的单位位向量， $\theta$ 是绕轴线 $k$ 转过的角度。数对 $(k, \theta)$ 被称为 $\boldsymbol{R}$ 的转轴 / 角度表示。给定一个任意旋转矩阵 $\boldsymbol{R}$，其元素为 $r_{ij}$，所对应的转角 $\theta$ 和转轴 $\boldsymbol{k}$ 为

$$ \theta = \cos^{-1} \left( \frac{r_{11} + r_{22} + r_{33} - 1}{2} \right) $$ $$ \boldsymbol{k} = \frac{1}{2 \sin \theta} \begin{bmatrix} r_{32} - r_{23} \\ r_{13} - r_{31} \\ r_{21} - r_{12} \end{bmatrix} $$ (2-46)

式（2-46）可以通过直接操作式（2-44）中矩阵的条目来得到。转轴 / 角度表示并不唯一，这是因为，关于 $-k$ 轴角度为 $-\theta$ 的旋转与关于 $k$ 轴角度为 $\theta$ 的旋转相同，也就是说

$\boldsymbol{R}_{k,\theta} = \boldsymbol{R}_{-k,-\theta}$ (2-47)

如果 $\theta = 0$，那么 $\boldsymbol{R}$ 是单位矩阵，此时旋转轴线没有定义。