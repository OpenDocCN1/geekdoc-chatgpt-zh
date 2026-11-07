![](bbox=[0.38, 0.1, 0.73, 0.31])

图 8-56

# 8.4.7 模型的改进优化

首先，在 3000 个训练样本上训练一个简单的小型卷积神经网络，不做任何正则化，此时主要的问题在于过拟合。我们改进优化方法，采用数据增强（Data Augmentation）的方法来降低过拟合。数据增强是针对计算机视觉处理过拟合的新方法，在计算机视觉领域是一种非常强大的降低过拟合的技术，在深度学习处理图像时几乎都会用到。数据增强的原理是从现有的训练样本中生成更多的训练数据，其方法是利用多种能够生成可信图像的随机变换来增加样本，其目标是让模型在训练时不会两次查看完全相同的图像。这让模型能够观察到数据的更多内容，从而具有更好的泛化能力。

在 Keras 中，我们可以通过对 ImageDataGenerator 实例读取的图像执行多次随机变换，来实现数据增强。

示例代码如下：

```
from keras.preprocessing.image import ImageDataGenerator
# 使用数据增强，对 ImageDataGenerator 实例读取的图像执行多次随机变换来实现
train_datagen = ImageDataGenerator(
    rescale=1./255,
    rotation_range=40,
    width_shift_range=0.2,
    height_shift_range=0.2,
    shear_range=0.2,
    zoom_range=0.2,
    horizontal_flip=True,)
```

ImageDataGenerator 中几个参数的含义如下：

+   - rescale: 将像素值缩放，这里是将像素值（0~255）缩放到 [0, 1] 区间。
+   - rotation_range: 角度值（0~180），表示图像随机旋转的角度范围。
+   - width_shift 和 height_shift: 图像在水平或垂直方向上平移的范围（相对于总宽度或总高度的比例）。