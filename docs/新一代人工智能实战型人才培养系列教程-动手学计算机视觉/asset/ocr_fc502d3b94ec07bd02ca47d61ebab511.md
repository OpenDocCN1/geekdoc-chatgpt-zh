# 7.2 SIFT 算法

SIFT[1]的全称是 scale invariant feature transform（尺度不变特征变换），由 David G. Lowe 教授于 1999 年在国际计算机视觉会议（IEEE International Conference on Computer Vision，ICCV）上提出，是计算机视觉领域二十多年来引用率最高的论文之一。SIFT 算法是一种高效的基于尺度空间的块状区域检测算法，包含块状区域的检测及块状区域的描述（SIFT 特征）。它的提出极大推动了计算机视觉领域的发展，并被广泛用于视觉识别和三维重建。SIFT 算法的主要思想与 7.1 节中描述的一致，即利用尺度空间来检测图像中的块状区域。

# 7.2.1 局部极值点检测

在 7.1 节中，我们已经知道如何利用尺度空间检测特征点，即利用尺度归一化的高斯拉普拉斯算子 \(\sigma^2\nabla^2 G\) 与图像 \(I\) 做卷积，寻找响应中极值点对应的 \(\sigma\) 。为了提高计算效率，我们可以使用高斯差分（difference of Gaussian，DoG）算子 \(D\) 近似代替归一化高斯拉普拉斯算子。高斯差分算子定义如下：

\(D(x,y,\sigma)=G(x,y,k\sigma)-G(x,y,\sigma)\)

因为

\(\sigma\nabla^2 G = \frac{\partial G}{\partial \sigma} \approx \frac{G(x,y,k\sigma)-G(x,y,\sigma)}{k\sigma-\sigma}\)

因此，我们可以得到

\(D(x,y,\sigma)=G(x,y,k\sigma)-G(x,y,\sigma) \approx (k-1)\sigma^2 \nabla^2 G\)

其中， \(k\) 是常数，所以 \(k-1\) 对极值点不会有影响。根据图 7-8 所示，我们可以发现，归一化高斯拉普拉斯算子和高斯差分算子近似。

![](bbox=[0.282, 0.642, 0.738, 0.872])

图 7-8 归一化高斯拉普拉斯算子和高斯差分算子的比较