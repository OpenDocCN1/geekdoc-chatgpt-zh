高分辨率图片的 loss，最终形成模型。并且在这一方法上也总结了不少经验参数，如在 SRCNN 中，使用 3 层步长为 1 的同卷积，分别为（9×9 的 64 输出、1×1 的 32 输出、5×5 的 3 输出）效果会更好。

后来出现了另一种比较高效的方法 ESPCN（实时的基于卷积神经网络的图像超分辨率方法）。ESPCN 的核心概念是亚像素卷积层（sub-pixel convolutional layer），即先在原有的低像素图片上做卷积操作，最终输出一个含有多 feature map 的结果，保证总像素点与高分辨率的像素点总和是一致的，然后将多张低分辨率图片合并成一张高分辨率图片。例如，假设需要将低分辨率图片的像素扩大 2 倍（从 128×128 扩大到 256×256），就直接将其进行卷积操作，最终输出放大倍数的平方(2×2)个 feature map,即[batch_size,W,H,4]（如果是 RGB 彩色图片就会是[batch_size,W,H,12]）。以灰度图为例，将 4 个图片中的第一个像素取出成为重构图中的 4 个像素，依此类推，在重构图中的每个 2×2 区域都是由这 4 幅图对应位置的像素组成，最终形成形状为[batch_size,2×W,2×H,1]大小的高分辨率图像。这个变换被称为 sub-pixel convolution，如图 12-12 所示。

![](bbox=[0.28, 0.4, 0.75, 0.55])

图 12-12 ESPCN 图例

sub-pixel convolution 的方法只在最后一层进行图像低分辨率到高分辨率的大小变换，保证了前面的卷积运算均在低分辨率图像上进行，得到了更高的运算效率。

另外，基于视频图像的 SR 方法还有 VESPCN，这里不再展开介绍，有兴趣的读者可以自行学习。

# 3. TensorFlow中的图片变换函数

在 TensorFlow 中变化分辨率的函数主要是 tf.image.resize_images，其具体原型如下：

```
def resize_images(images,
size,
method=ResizeMethod.BILINEAR,
align_corners=False):
```

前两个参数分别是输入的图片及要变化的尺寸，图片的形状为[batch, height, width, channels]或[height, width, channels]均可。第 3 个参数 method 的取值如下。

+   • ResizeMethod.BILINEAR：表示使用双线性插值算法变化图片。
+   • ResizeMethod.NEAREST_NEIGHBOR：表示使用邻近插值算法变化图片。