```
```
# x: [batch, num_ROIs, 2xMASK_POOL_SIZE, 2xMASK_POOL_SIZE, 256]
# x: 28*28*256

# 反卷积后再次进行 1x1 卷积调整特征图的通道数，使其最终数量为 numclasses
# 不难发现上述代码块其实是一个 FCN

x = TimeDistributed(nn.Conv2d(num_classes, (1, 1), strides=1,
            activation="sigmoid"))(x)

# x: [batch, num_ROIs, 2xMASK_POOL_SIZE, 2xMASK_POOL_SIZE, numclasses]

return x
```
```

Mask R-CNN 的训练阶段和测试阶段与 Faster R-CNN 基本一致。不同的是，在训练阶段，Mask R-CNN 的损失函数 L 定义如下：

$L = L_{cls} + L_{reg} + L_{mask}$

除具有 Faster R-CNN 同样的分类损失 Lcls 和包围盒回归损失 Lreg 之外，Mask R-CNN 增加了掩模分割损失 Lmask，用于优化目标的二值掩模。对于每个目标类别，它采用逐像素的二值交叉熵损失来衡量预测类别的掩模与该类别对应的真实掩模之间的偏差。

在测试阶段，Mask R-CNN 对候选区域进行包围盒的预测，同时预测包围盒内目标的掩模。除此之外，Mask R-CNN 还需要对预测的包围盒进行 NMS 处理，去除冗余的包围盒，返回最终的结果。

# 13.4 代码运行示例

由于 Mask R-CNN 代码架构规模比较大，我们将直接调用相关接口进行效果展示。先导入必要的包。

```
```
! git clone https://github.com/matterport/Mask_RCNN.git
! cd Mask_RCNN

import os
import sys
sys.path.append('Mask_RCNN')
os.chdir('./Mask_RCNN')
os.getcwd()

! python setup.py install

from mrcnn.config import Config
from mrcnn import model as modellib
from mrcnn import visualize
import cv2
import colorsys
```
```