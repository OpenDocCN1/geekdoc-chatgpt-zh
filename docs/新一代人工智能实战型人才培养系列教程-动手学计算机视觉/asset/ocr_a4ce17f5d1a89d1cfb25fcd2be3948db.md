![](bbox=[0.253, 0.093, 0.755, 0.233])

图 7-14 将特征点所在的块状区域的横轴旋转到主方向上

对块状区域中的一点(i,j)，将其逆时针旋转角度α，得到新的点的坐标(i', j')，则有

[i' \ j']^T = \begin{bmatrix} \cos\alpha & -\sin\alpha \\ \sin\alpha & \cos\alpha \end{bmatrix} [i \ j]^T

# 7.3 代码实现

在本节中，我们将通过编程实现 SIFT 算法。我们将 SIFT 算法的每一步都编写成一个函数。我们先实现尺度空间的生成。

```
```python
import cv2
import numpy as np
from scipy.ndimage import maximum_filter
import matplotlib.pyplot as plt
import random

# 生成指定数目的高斯核，并对图像做高斯滤波
def generateGimage(image, sigma, num_layers = 8, k_stride = 2):
    sigma_res = np.sqrt(np.max([(sigma ** 2) - ((1) ** 2), 0.01]))
    # (0,0)表示卷积核的大小根据 sigma 来决定
    image = cv2.GaussianBlur(image, (0, 0), sigmaX=sigma_res,
                             sigmaY=sigma_res)

    # 生成高斯核
    k = 2 ** (1 / k_stride)
    gaussian_kernels = np.zeros(num_layers)
    # 第一层高斯核就是 1.6
    gaussian_kernels[0] = sigma

    for i in range(1, num_layers):
        # 根据高斯核的性质，可以将大的高斯核拆分，减少计算量
        gaussian_old = k**(i-1) * sigma
        gaussian_new = k * gaussian_old
        gaussian_kernels[i] = np.sqrt(gaussian_new**2 - gaussian_old**2)

    # 至此，我们已经得到一系列高斯核
```
```