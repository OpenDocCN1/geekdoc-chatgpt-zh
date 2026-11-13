会在空间中相交于同一点，这个点就是我们要求解的三维点的位置。这个过程称为三角测量，又称三角化。

以图 19-4 为例，根据透视投影规则，可以得到

$$z_l \begin{bmatrix} u_l \\ v_l \\ 1 \end{bmatrix} = \begin{bmatrix} f_x^{(l)} & 0 & o_x^{(l)} & 0 \\ 0 & f_y^{(l)} & o_y^{(l)} & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix} \begin{bmatrix} x_l \\ y_l \\ z_l \\ 1 \end{bmatrix}$$

和

$$z_r \begin{bmatrix} u_r \\ v_r \\ 1 \end{bmatrix} = \begin{bmatrix} f_x^{(r)} & 0 & o_x^{(r)} & 0 \\ 0 & f_y^{(r)} & o_y^{(r)} & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix} \begin{bmatrix} x_r \\ y_r \\ z_r \\ 1 \end{bmatrix}$$

其中，$f_x, f_y, o_x, o_y$ 分别代表照相机在 x 轴上的焦距、在 y 轴上的焦距、光轴与成像平面的交点在图像坐标系上的 x 坐标以及 y 坐标，上标(l)和(r)则代表这些参数属于左照相机还是右照相机。又因为两台照相机间的相对位姿是已知的，因此有

$$\begin{bmatrix} x_l \\ y_l \\ z_l \\ 1 \end{bmatrix} = \begin{bmatrix} r_{11} & r_{12} & r_{13} & t_x \\ r_{21} & r_{22} & r_{23} & t_y \\ r_{31} & r_{32} & r_{33} & t_z \\ 0 & 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} x_r \\ y_r \\ z_r \\ 1 \end{bmatrix}$$

联立得到

$$z_l \begin{bmatrix} u_l \\ v_l \\ 1 \end{bmatrix} = \begin{bmatrix} f_x^{(l)} & 0 & o_x^{(l)} & 0 \\ 0 & f_y^{(l)} & o_y^{(l)} & 0 \\ 0 & 0 & 1 & 0 \end{bmatrix} \begin{bmatrix} r_{11} & r_{12} & r_{13} & t_x \\ r_{21} & r_{22} & r_{23} & t_y \\ r_{31} & r_{32} & r_{33} & t_z \\ 0 & 0 & 0 & 1 \end{bmatrix} \begin{bmatrix} x_r \\ y_r \\ z_r \\ 1 \end{bmatrix}$$

简写为

$$\tilde{u_l} \propto M_l \tilde{x_r}$$

其中，$	ilde{u_l}$ 代表 $u_l$ 的齐次坐标；$M_l$ 代表中间两个矩阵的乘积；$	ilde{x_r}$ 代表 $x_r$ 的齐次坐标；正比于符号 $\propto$ 表示两个齐次坐标向量是成正比的。又因为

$$\tilde{u_r} \propto P_r \tilde{x_r}$$

其中，$P_r$ 代表右照相机的投影矩阵。联立两式，就可以得到关于 $x_r, y_r, z_r$ 的方程组：

$$\begin{bmatrix} u_r P_{31} - P_{11} & u_r P_{32} - P_{12} & u_r P_{33} - P_{13} \\ v_r P_{31} - P_{21} & v_r P_{32} - P_{22} & v_r P_{33} - P_{23} \\ u_r m_{31} - m_{11} & u_r m_{32} - m_{12} & u_r m_{33} - m_{13} \\ v_r m_{31} - m_{21} & v_r m_{32} - m_{22} & v_r m_{33} - m_{23} \end{bmatrix} \begin{bmatrix} x_r \\ y_r \\ z_r \end{bmatrix} = \begin{bmatrix} P_{14} - P_{34} \\ P_{24} - P_{34} \\ m_{14} - m_{34} \\ m_{24} - m_{34} \end{bmatrix}$$