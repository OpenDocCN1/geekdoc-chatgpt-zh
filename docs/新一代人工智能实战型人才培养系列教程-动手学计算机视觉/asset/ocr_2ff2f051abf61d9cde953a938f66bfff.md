Fast R-CNN 在测试阶段的步骤与训练阶段类似，也是包含上述 3 个步骤。与 R-CNN 的测试阶段类似，最后需要使用非极大值抑制来合并多个高度重叠的预测包围盒。

接下来，我们来学习 Fast R-CNN 中 ROI 池化这一核心模块的代码实现。

```
import numpy as np

def ROI_pooling(feature_map, ROIs, output_size):
    # feature_map: 输入特征图, shape 为(H, W, C)
    # ROIs: 包含 ROI 的位置坐标和高、宽, shape 为(num_ROIs, 4)
    # output_size: ROI 池化后输出的大小

    # 将 ROI 位置坐标和高度、宽度转换为整数
    ROIs = np.round(ROIs).astype(np.int32)

    # 计算每个 ROI 的高度和宽度
    ROI_heights = ROIs[:, 2] - ROIs[:, 0] # (num_ROIs,)
    ROI_widths = ROIs[:, 3] - ROIs[:, 1] # (num_ROIs,)

    # 计算每个 ROI 的垂直步长和水平步长
    stride_y = ROI_heights / output_size[0]
    stride_x = ROI_widths / output_size[1]

    # 初始化输出数组
    pooled_ROIs = np.zeros((ROIs.shape[0], output_size[0], output_size[1],
                            feature_map.shape[2]))

    # 对每个 ROI 内的特征进行池化操作
    for i, ROI in enumerate(ROIs):
        # 获取 ROI 的坐标
        y1, x1, y2, x2 = ROI

        # 计算每个 ROI 的垂直池化步长和水平池化步长
        dy = (y2 - y1) / output_size[0]
        dx = (x2 - x1) / output_size[1]

        # 对每个通道的特征进行池化
        for c in range(feature_map.shape[2]):
            for y in range(output_size[0]):
                for x in range(output_size[1]):
                    # 计算当前子窗口的坐标
                    y_start = int(np.floor(y1 + y * dy))
                    x_start = int(np.floor(x1 + x * dx))
                    y_end = int(np.ceil(y_start + dy))
                    x_end = int(np.ceil(x_start + dx))

                    # 取出当前子窗口对应的特征图区域
                    patch = feature_map[y_start:y_end, x_start:x_end, c]

                    # 对当前子窗口进行池化
                    pooled_ROIs[i, y, x, c] = np.max(patch)

return pooled_ROIs
```