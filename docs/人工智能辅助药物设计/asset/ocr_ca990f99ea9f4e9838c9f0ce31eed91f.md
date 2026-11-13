步长是指卷积核在输入矩阵上每次移动的单位长度，在纵轴与横轴方向每次移动的步长都是可设定的。在卷积运算中步长的默认值为“1”，即每次移动一个单位长度；在池化运算时步长的默认值等于池化窗口的边长。

二维卷积的示例运算过程如下：

```
1x0+0x1+0x2+0x1=0

0x0+1x1+0x2+0x1=1

0x0+0x1+1x2+0x1=2

0x0+0x1+0x2+1x1=1
```

如上述计算过程，4个运算结果均可与输出矩阵的4个元素一一对应。上述示例中卷积运算后得到一个形状大小为2x2的输出矩阵。

# 提示

卷积运算的符号用“*”来表示，数组大小用“x”表示。

二维卷积运算的代码示例如清单8-1所示。

# 清单8-1 二维卷积运算的代码

```
tf.keras.layers.Conv2D(
    filters,
    kernel_size,
    strides=(1, 1),
    padding='valid',
    data_format=None,
    dilation_rate=(1, 1),
    activation=None
)
```

本段代码中的主要变量解释如下。

+   • filters: 卷积核的个数。

+   • kernel_size: 卷积核尺寸。如果是正方形，则用一个整数表示；如果是长方形，则需要明确指明高用 h 表示，宽用 w 表示，可以用元组或者列表的形式表示两者（如[h， w]）。

+   • strides: 步长。默认横纵向滑动步长均为1，也可以设置其他步长，例如纵向步长或者横向步长可以设为（1， 1）。

+   • padding: 填充。当padding = 'same'时，全零填充；当padding = 'valid'时，不需要填充，不区分大小写，在8.2.3节中会详细介绍。

+   • data_format: 一个字符串，表示输入维度的顺序。数据格式支持channels_last（默认）和channels_first这两种类型。channels_last对应具体的数据格式是(batch,height,width,channels)的输入，而channels_first对应具体的数据格式是(batch,channels,height,width)的输入。

+   • dilation_rate: 卷积核的膨胀系数。将卷积核进行形状膨胀，新的位置用0填充，在8.2.4节中会详细介绍

+   • activation: 激活函数。相当于经过卷积输出后，再经过一次激活函数（常见的激活函数有ReLU、Softmax、Sigmoid），在8.1.5节中会详细介绍。