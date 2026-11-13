$R_{z,0} = I$

$R_{z,\theta}R_{z,\phi} = R_{z,\theta+\phi}$

由式 (2-4)、式 (2-5) 可推出

$(R_{z,\theta})^{-1} = R_{z,-\theta}$

同样，表示绕 x 轴和 y 轴旋转的基本旋转矩阵由式 (2-7)、式 (2-8) 给出

$R_{x,\theta} = \begin{bmatrix} 1 & 0 & 0 \\ 0 & \cos\theta & -\sin\theta \\ 0 & \sin\theta & \cos\theta \end{bmatrix}$

$R_{y,\theta} = \begin{bmatrix} \cos\theta & 0 & \sin\theta \\ 0 & 1 & 0 \\ -\sin\theta & 0 & \cos\theta \end{bmatrix}$

它们也满足类似于式 (2-4) ～ 式 (2-6) 的性质。

例 2.2 定义如图 2-4 所示的坐标系 {0} 和坐标系 {1} 之间的相对姿态，将单位向量 $x_1, y_1, z_1$ 投射到 $x_0, y_0, z_0$ 坐标轴上，给出 $x_1, y_1, z_1$ 在坐标系 {0} 中的坐标，即

![](bbox=[0.67, 0.41, 0.91, 0.55])

${}^0x_1 = \begin{bmatrix} \frac{1}{\sqrt{2}} \\ 0 \\ \frac{1}{\sqrt{2}} \end{bmatrix}, {}^0y_1 = \begin{bmatrix} \frac{1}{\sqrt{2}} \\ 0 \\ \frac{-1}{\sqrt{2}} \end{bmatrix}, {}^0z_1 = \begin{bmatrix} 0 \\ 0 \\ 1 \end{bmatrix}$

图 2-4 定义两个坐标系之间的相对姿态

旋转矩阵 ${}^0_1R$ 的列向量为坐标系 {1} 相对于坐标系 {0} 的姿态角，即

${}^0_1R = \begin{bmatrix} \frac{1}{\sqrt{2}} & \frac{1}{\sqrt{2}} & 0 \\ 0 & 0 & 1 \\ \frac{1}{\sqrt{2}} & \frac{-1}{\sqrt{2}} & 0 \end{bmatrix}$

# 2.1.2 旋转表示

# 1. 旋转变换

图 2-5 所示为一个刚体 S 以及附在其上的坐标系 {1}，给定点 p 的位置向量 ${}^1p$ (即给定点 p 相对于坐标系 {1} 的坐标)，要确定点 p 相对于固定参考坐标系 {0} 的坐标。