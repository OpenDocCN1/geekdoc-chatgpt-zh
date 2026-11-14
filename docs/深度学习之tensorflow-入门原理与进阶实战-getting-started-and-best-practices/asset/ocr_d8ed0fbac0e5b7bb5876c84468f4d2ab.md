度，图像通道数，滤波器个数”，要求类型与参数 input 相同。有一个地方需要注意，第三维 in_channels，就是参数 input 的第四维。

+   • strides: 卷积时在图像每一维的步长，这是一个一维的向量，长度为 4。

+   • padding: 定义元素边框与元素内容之间的空间。string 类型的量，只能是 SAME 和 VALID 其中之一，这个值决定了不同的卷积方式，padding 的值为VALID'时，表示边缘不填充，当其为'SAME'时，表示填充到滤波器可以到达图像边缘。

+   • use_cudnn_on_gpu: bool 类型，是否使用 cudnn 加速，默认为 true。

+   • 返回值: tf.nn.conr2d 函数结果返回一个 Tensor，这个输出就是常说的 feature map。

⚠注意：在卷积函数中，padding 参数是最容易引起歧义的，该参数仅仅决定是否要补 0，因此一定要清楚 padding 设为 SAME 的真正含义。在设为 SAME 的情况下，只有在步长为 1 时生成的 feature map 才会与输入值相等。

# 8.4.2 padding 规则介绍

padding 属性的意义是定义元素边框与元素内容之间的空间。

在 tf.nn.conv2d 函数中，当变量 padding 为 VALID 和 SAME 时，函数具体是怎么计算的呢？其实是有公式的。为了方便演示，先来定义几个变量：

+   • 输入的尺寸中高和宽定义成 in_height、in_width。

+   • 卷积核的高和宽定义成 filter_height、filter_width。

+   • 输出的尺寸中高和宽定义成 output_height、output_width。

+   • 步长的高宽方向定义成 strides_height、strides_width。

# 1. VALID情况

输出宽和高的公式代码分别为：

```
output_width=(in_width-filter_width + 1)/strides_width （结果向上取整）
output_height=(in_height-filter_height+1)/strides_height （结果向上取整）
```

# 2. SAME情况

输出的宽和高将与卷积核没有关系，具体公式代码如下：

```
out_height = in_height / strides_height （结果向上取整）
out_width = in_width / strides_width （结果向上取整）
```

这里有一个很重要的知识点——补零的规则，见如下代码：

```
pad_height=max((out_height-1)×strides_height +filter_height-in_height,0)
pad_width = max((out_width-1)×strides_width +filter_width - in_width, 0)
pad_top = pad_height / 2
pad_bottom = pad_height - pad_top
```