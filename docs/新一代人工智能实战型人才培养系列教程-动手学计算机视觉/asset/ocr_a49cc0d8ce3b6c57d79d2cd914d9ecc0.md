```
ConvTranspose2d(3, 3, kernel_size=(3, 3), stride=(2, 2))
```

由于是转置卷积，因此实际的步长为 1/stride。同时，在实际操作中，卷积核的参数可以通过学习得到，也可以利用双线性插值将其固定。利用双线性插值固定卷积核权重的代码如下：

```
# 利用双线性插值法设置卷积核权重
def bilinear_kernel(in_channels, out_channels, kernel_size):

    # 找到卷积核正中心的位置
    factor = (kernel_size + 1) // 2
    if kernel_size % 2 == 1:
        center = factor - 1
    else:
        center = factor - 0.5

    # np.ogrid()函数构建一个二维矩阵
    og = np.ogrid[:kernel_size, :kernel_size]
    print('og', og)

    # 构建卷积核权重
    filt = (1 - abs(og[0] - center) / factor) * (1 - abs(og[1] - center) / factor)
    print('filt:\n', filt)

    weight = np.zeros((in_channels, out_channels,
                       kernel_size, kernel_size), dtype='float32')
    weight[range(in_channels), range(out_channels), :, :] = filt

    return torch.from_numpy(weight)
```

以一个简单的例子进一步说明上述代码中的部分变量。

```
import numpy as np
bilinear_kernel(1, 1, 3)
```

```
og [array([[0],
           [1],
           [2]]), array([[0, 1, 2]])]
```

```
filt [[0.25 0.5  0.25]
       [0.5  1.   0.5 ]
       [0.25 0.5  0.25]]
```

```
tensor([[[[0.2500, 0.5000, 0.2500],
           [0.5000, 1.0000, 0.5000],
           [0.2500, 0.5000, 0.2500]]]])
```

# 11.3.2 跳跃连接

对于传统的分类网络，由于主干网络最后一层输出的特征图空间分辨率太低（如图 11-4），上采样之后预测得到的标签图会很粗糙，无法精准地勾勒出原图中物体的轮廓。一般情况下，浅层卷积层的特征图较大，能够精确定位物体的边界，但是语义性不够；深层卷积层的特征图较小，但是语义性强。有什么办法在得到较大的特征图的同时，也能使特征图拥有丰富的语义信息呢？ FCN 通过跳跃连接将网络中不同层级的卷积层特征融合，解决了该问题。如图 11-8