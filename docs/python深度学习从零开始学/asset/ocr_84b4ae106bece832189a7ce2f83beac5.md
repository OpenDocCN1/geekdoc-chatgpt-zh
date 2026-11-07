观察这些图片，可以发现图片种类各异，分辨率也各不相同。图片中的猫和狗的形状、所处位置、体表颜色各不一样；它们的姿态不同，有的坐着有的站着；它们的情绪可能是开心的也可能是伤心的；猫可能在睡觉，而狗可能在汪汪叫着；照片可能以任一焦距从任意角度拍下。这些图片有着无限种可能，对于我们人类来说，在一系列不同种类的照片中识别出一个场景中的宠物自然是毫不费力的事情，然而这对于一台机器来说这可不是一件小事。实际上，如果要机器实现自动分类，那么我们需要知道如何强有力地描绘出猫和狗的特征，也就是说为什么我们认为这幅图片中的是猫，而那幅图片中的却是狗。这需要描绘每个动物的内在特征。深度神经网络在图像分类任务上效果很好的原因是，它们有着能够自动学习多重抽象层的能力，这些抽象层在给定一个分类任务后，又可以对每个类别给出更简单的特征表示。

使用很少的数据来训练一个图像分类模型，这是很常见的情况。猫狗数据集中包含 4000 幅猫和狗的图像（2000 幅猫的图像，2000 幅狗的图像）。我们将 3000 幅图像用于训练（猫 1500 幅，狗 1500 幅），1000 幅图像（猫和狗各 500 幅）用于验证，代码执行结果如图 8-46 所示。

```
In [1]:
import os
base_dir = 'cat-and-dog'
#构造路径存储训练数据，验证数据以及测试数据
train_dir = os.path.join(base_dir, 'training_set')
os.makedirs(train_dir, exist_ok = True)
validation_dir = os.path.join(base_dir, 'validation_set')
os.makedirs(validation_dir, exist_ok = True)
train_cats_dir = os.path.join(train_dir, 'cats')
train_dogs_dir = os.path.join(train_dir, 'dogs')
validation_cats_dir = os.path.join(validation_dir, 'cats')
validation_dogs_dir = os.path.join(validation_dir, 'dogs')
#我们来检查一下，看看每个分组（训练／验证）中分别包含多少张图像
print('total training cat images: ', len(os.listdir(train_cats_dir)))
print('total training dog images: ', len(os.listdir(train_dogs_dir)))
print('total validation cat images: ', len(os.listdir(validation_cats_dir)))
print('total validation dog images: ', len(os.listdir(validation_dogs_dir)))
```

total training cat images: 1500
total training dog images: 1500
total validation cat images: 500
total validation dog images: 500

图 8-46

# 8.4.2 建立模型

我们基于 Keras 深度学习框架创建卷积神经网络模型来识别猫和狗。在以前 MNIST 手写数字识别示例中，我们创建了一个小型卷积神经网络，现在将复用相同的总体结构，即卷积神经网络由卷积层（使用 ReLU 激活函数）和最大池化层交替堆叠构成。

但由于这里要处理的是更大的图像和更复杂的问题，因此需要相应地增大网络，即再增加一个卷积层 + 卷积层最大池化层的组合。这样既可以增大网络容量，也可以进一步减小特征图的尺寸，使其在连接平坦层时尺寸不会太大。本例中初始输入的尺寸为 150×150，最后在平坦层之前的特征图大小为 7×7。

通过 Sequential 对象创建卷积神经网络模型的程序代码如图 8-47 所示。