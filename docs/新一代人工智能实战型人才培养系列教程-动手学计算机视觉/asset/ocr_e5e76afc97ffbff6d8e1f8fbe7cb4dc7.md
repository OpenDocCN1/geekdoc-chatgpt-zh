# 8.3.1 计算变换矩阵

图像拼接是要将一组图像拼接成一幅大的全景图。这个任务中最基本的问题就是如何拼接两幅图像。给定两幅图像，要将它们拼接在一起，就需要计算它们之间的变换矩阵 \( T \)，就需要找到两幅图像之间的匹配关系。通过第 7 章中介绍的 SIFT 特征点检测算法找到两幅图像上的特征点，然后基于 SIFT 特征描述，就可以确定这些特征点之间的匹配关系，如图 8-5 所示。

![](bbox=[0.341, 0.23, 0.69, 0.378])

图 8-5 基于特征点的图像匹配

设 \( (x, y) \) 和 \( (x', y') \) 是两幅图像中匹配的一对特征点，在齐次坐标系中分别表示为 \( (x, y, 1) \) 和 \( (x', y', 1) \)，对应的向量分别为 \( p = (x, y, 1)^T \) 和 \( p' = (x', y', 1)^T \)，则有 \( p' = Tp \)，即

\(\begin{bmatrix} x' \\ y' \\ 1 \end{bmatrix} = \begin{bmatrix} a & b & c \\ d & e & f \\ g & h & i \end{bmatrix} \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}\)

可以得到 \( x \)、\( y \) 和 \( x'\)、\( y'\) 之间的关系为

\( x' = \frac{ax + by + c}{gx + hy + i}, y' = \frac{dx + ey + f}{gx + hy + i} \)

为了更方便地求解矩阵 \( T \) 中的未知量，将上式写成

\( x'(gx + hy + i) = ax + by + c \)  
\( y'(gx + hy + i) = dx + ey + f \)

即

\(\begin{bmatrix} x & y & 1 & 0 & 0 & 0 & -x'x & -x'y & -x' \\ 0 & 0 & 0 & x & y & 1 & -y'x & -y'y & -y' \end{bmatrix} \begin{bmatrix} a \\ b \\ c \\ d \\ e \\ f \\ g \\ h \\ i \end{bmatrix} = \begin{bmatrix} 0 \\ 0 \end{bmatrix}\)