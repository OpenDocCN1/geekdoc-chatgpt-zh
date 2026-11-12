$$U_j=\overline{x_j}+\sigma_j$$ (11.2.5) $$U_k=\overline{x_k}+\sigma_k$$ (11.2.6)

第 $j, k$ 个传感器的相互支持程度用 $U_j, U_k$ 的同一度 $a(U_j, U_k)$ 表示： $$a(U_j, U_k)=\frac{\min(U_j, U_k)}{\max(U_j, U_k)}$$ (11.2.7)

# 步骤 2 传感器的聚类。

根据两个传感器同一度值的大小，可把多个传感器按相互支持程度的强弱分为三类，其中当

$$a(U_j, U_k) \begin{cases} >0.5, \\ >0 \& \leqslant 0.5, \\ \leqslant 0, \end{cases}$$ 强相互支持(相融性好) 弱相互支持(相融性一般) 相互不支持(相融性差)

# 步骤 3 计算目标观测值 $Z$ 与测量特性联系数的同一度。

当用 $n$ 个传感器分别测量目标 $Z$ 的 $n$ 个指标，且与 $m$ 个待识别物比较时，需分别计算 $n$ 个指标 $Z_p (P=1, 2, \cdots, n)$ 与 $m$ 个待识别物特性联系数 $\overline{x}_{jp} + i\sigma_{jp} (j=1, 2, \cdots, m)$ 的同一度 $a(Z_p, \overline{x}_{jp} + i\sigma_{jp})$：

$$a(Z_p, \overline{x}_{jp} + i\sigma_{jp})=\frac{\min(Z_p, \overline{x}_{jp} + i\sigma_{jp})}{\max(Z_p, \overline{x}_{jp} + i\sigma_{jp})}$$ (11.2.8)

由此得到一个 $n \times m$ 的同一度矩阵 $H_{n \times m}$：

$$H_{n \times m} = \begin{bmatrix} a(Z_1, \overline{x}_{11} + i\sigma_{11}) & \cdots & a(Z_1, \overline{x}_{m1} + i\sigma_{m1}) \\ \vdots & & \vdots \\ a(Z_n, \overline{x}_{1n} + i\sigma_{1n}) & \cdots & a(Z_n, \overline{x}_{mn} + i\sigma_{mn}) \end{bmatrix}$$ (11.2.9)

# 步骤 4 目标识别决策。

原则上根据式(11.2.9)所示矩阵中属于强相互支持的传感器组所对应的同一度子矩阵 $R_{e \times f} (e \in n, f \in m)$ 作出目标识别决策。具体是，当第 $k$ 列中的最小同一度大于其余列最大同一度时，被观测的目标 $Z$ 为物体 $k$，其他情况属不确定。

# 11.2.3 数据融合实例

为便于对照，这里取文献[2]～[4]中的一个共用实例，共有 4 个传感器测量目标 $Z$，试判别目标 $Z$ 应当是物体 A 还是物体 B，或是其他不明物。具体数据见表 11.2.1。