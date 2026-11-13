那么如何处理非整数坐标并得到特征图上对应位置的值呢？回忆一下之前学过的双线性插值，对于特征图中的任意一个位置，都可以利用双线性插值的方式得到其对应的数值。在这个过程中不会用到取整操作，因此不会引入因坐标取整导致的偏移误差，这有利于实例分割的精度提升。同 RoI 池化一样，在 RoI 对齐中，每个 RoI 将被均分成 M × N 个子窗口（图中为 7×7 个）。随后，将每个子窗口再均分成 4 个小的子窗口，取这 4 个小子窗口的中心点，根据每个点在特征图中最近的整数坐标对应的特征值，通过插值来计算各自的特征值，再对这 4 个值进行最大池化（max pooling）或平均池化（average pooling），得到该子窗口的特征值，最终便可以得到空间分辨率为 M 像素×N 像素的 RoI 特征图。下面我们将介绍如何用代码实现 RoI 对齐。

```
import torch
import torch.nn as nn
import torch.nn.functional as F

class PyramidROIAlign(nn.Module):
    def __init__(self, pool_shape):
        super(PyramidROIAlign, self).__init__()
        self.pool_shape = tuple(pool_shape)

    def forward(self, inputs):
        # 输入包括必要的信息，如包围盒坐标和原始图像信息
        # 获取包围盒坐标
        boxes = inputs[0]

        # 获取图像元数据
        image_meta = inputs[1]

        # 获取特征图信息 [batch, height, width, channels]
        feature_maps = inputs[2:]

        # 获取包围盒的宽度和高度
        y1, x1, y2, x2 = torch.split(boxes, 1, dim=2)
        h = y2 - y1
        w = x2 - x1

        # 获取图像的大小
        image_shape = parse_image_meta(image_meta)['image_shape'][0]

        # 通过包围盒的大小确定该包围盒属于特征金字塔的哪个层级
        image_area = float(image_shape[0] * image_shape[1])
        ROI_level = torch.log2(torch.sqrt(h * w) / (224.0 / torch.sqrt(torch.tensor(image_area))))
        ROI_level = torch.clamp(4 + torch.round(ROI_level), min=2, max=5).int()
        # 压缩 axis=2 的轴，从形状 [batch, num_boxes, 1] 变为 [batch, num_boxes]
        ROI_level = torch.squeeze(ROI_level, dim=2)

        pooled = []
        box_to_level = []
        # 网络的第二层到第五层分别将原图像尺寸压缩为 1/4、1/8、1/16、1/32
        for i, level in enumerate(range(2, 6)):
```