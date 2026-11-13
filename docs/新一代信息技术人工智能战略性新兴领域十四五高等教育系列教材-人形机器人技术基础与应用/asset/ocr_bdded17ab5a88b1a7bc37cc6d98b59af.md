惯上使用符号 $SO(n)$ 指代所有此类 $n \times n$ 矩阵的集合，因此，$SO(n)$ 表示 $n$ 阶 $n \times n$ 的特殊正交群。

对于任意 $R \in SO(n)$，以下性质成立：

+   1) $R^T = R^{-1} \in SO(n)$。
+   2) $R$ 的各列是相互正交的单位向量。
+   3) $\det R = 1$。

为了更进一步从几何直观上解读旋转矩阵逆的概念，在二维空间中，对于一个对应转角为 $\theta$ 的旋转矩阵，它的逆矩阵可通过构造对应转角为 $-\theta$ 的旋转矩阵而简单计算如下：

```
\[ \begin{bmatrix} \cos(-\theta) & -\sin(-\theta) \\ \sin(-\theta) & \cos(-\theta) \end{bmatrix} = \begin{bmatrix} \cos \theta & \sin \theta \\ -\sin \theta & \cos \theta \end{bmatrix} = \begin{bmatrix} \cos \theta & -\sin \theta \\ \sin \theta & \cos \theta \end{bmatrix}^T \]
```

（2）三维空间内的旋转 上述投影技术可以很好地扩展到三维空间。在三维空间中，坐标系 {} 的各坐标轴被投射到坐标系 {0} 中，所得的旋转矩阵为

```
\[ {}_0^1 R = \begin{bmatrix} x_1 \cdot x_0 & y_1 \cdot x_0 & z_1 \cdot x_0 \\ x_1 \cdot y_0 & y_1 \cdot y_0 & z_1 \cdot y_0 \\ x_1 \cdot z_0 & y_1 \cdot z_0 & z_1 \cdot z_0 \end{bmatrix} \]
```

类似于二维空间内的旋转矩阵，该矩阵为正交矩阵，其行列式等于 1。在这种情形下，$3 \times 3$ 旋转矩阵属于 $SO(3)$ 群。

例 2.1 假设坐标系 {I} 绕 $z_0$ 轴旋转一个角度 $\theta$，希望找到对应的变换矩阵 ${}_0^1 R$。按照右手定则，定义旋转角度 $\theta$ 为正的方向是绕 $z$ 轴正向按照 “右旋螺纹” 法则的旋转方向。从图 2-3 中可以得到

```
\(x_1 \cdot x_0 = \cos \theta \quad y_1 \cdot x_0 = -\sin \theta\)
\(x_1 \cdot y_0 = \sin \theta \quad y_1 \cdot y_0 = \cos \theta\)
```

以及

```
\(z_1 \cdot z_0 = 1\)
```

而所有其他的点积均为零。因此，此情形下的旋转矩阵 ${}_0^1 R$ 有一个特别简单的形式，即

```
\[ {}_0^1 R = \begin{bmatrix} \cos \theta & -\sin \theta & 0 \\ \sin \theta & \cos \theta & 0 \\ 0 & 0 & 1 \end{bmatrix} \]
```

式（2-3）给出的旋转矩阵称为基本旋转矩阵（绕 $z$ 轴）。在这种情况下，可以使用更具描述性的符号 $R_{z,\theta}$，而不是 ${}_0^1 R$，来表示这个旋转矩阵。容易验证，基本旋转矩阵 $R_{z,\theta}$ 具有下述特性

![](bbox=[0.7, 0.72, 0.93, 0.9])