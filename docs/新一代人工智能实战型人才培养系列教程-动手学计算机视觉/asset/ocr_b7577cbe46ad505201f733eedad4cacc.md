令 $z = D^{\frac{1}{2}}y$, 则有

$D^{-\frac{1}{2}}(D-W)D^{-\frac{1}{2}}z = \lambda z$

很明显，这里 $z$ 和 $\lambda$ 分别是 $D^{-\frac{1}{2}}(D-W)D^{-\frac{1}{2}}$ 的特征向量和特征值。因此，只需求解该特征值即可。需要注意，以最小的特征值对应的特征向量作为切割依据会将图中所有顶点聚为一类，没有任何意义，因此我们要的切割点是第二小的特征值对应的特征向量。

归一化割割算法的计算过程可以总结为以下几步。

+   (1) 输入图像，以像素作为顶点构建图，并定义边的权重表达式。这里，我们定义权重函数为

$W_{ij} = \exp\left(\frac{-\|I_i - I_j\|_2^2}{\sigma_I^2}\right) * \begin{cases} \exp\left(\frac{-\|x_i - x_j\|_2^2}{\sigma_x^2}\right), & \|x_i - x_j\|_2 < r \\ 0, & \text{其他} \end{cases}$

其中，$\sigma_I$、$\sigma_x$ 和 $r$ 是 3 个超参数。

+   (2) 计算权重矩阵 $W$ 和对角矩阵 $D$。

+   (3) 计算 $D^{-\frac{1}{2}}(D-W)D^{-\frac{1}{2}}$ 的特征值和特征向量，并确定第二小的特征值所对应的特征向量。

下面，我们将编程实现归一化割算法。我们先输入原图像。

```
```python
import numpy as np
import cv2
from PIL import Image
import matplotlib.pyplot as plt

sample_img = cv2.imread('segmentation.jpeg', cv2.IMREAD_GRAYSCALE).astype(
    np.float32) / 255
plt.imshow(sample_img, cmap='gray')
```
```

<matplotlib.image.AxesImage at 0x162442580>

![](bbox=[0.342, 0.685, 0.685, 0.915])