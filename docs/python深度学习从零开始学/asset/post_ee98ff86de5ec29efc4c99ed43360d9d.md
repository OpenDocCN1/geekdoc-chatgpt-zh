
从纹理到图片风格其实只差两步。第一步也是比较神奇的，Gatys 发现纹理能够描述一个图像的风格。严格来说纹理只是图片风格的一部分，但是不仔细研究纹理和风格之间的区别的话，乍一看给人感觉是差不多的。第二步是如何只提取图片内容而不包括图片风格。

第一篇论文解决了从图片中提取纹理的任务，但是还有一个关键点就是如何只提取图片内容，而不包括图片风格？这就是 Gatys 的第二篇论文做的事情：Gatys 把物体识别模型再拿出来用了一遍，这次不使用格拉姆矩阵（Gram 矩阵）统计模型了，直接把局部特征看作近似的图片内容，这样就得到了一个把图片内容和图片风格（就是纹理）分开的系统，剩下的就是把一幅图片的内容和另一幅图片的风格合起来，即找到能让合适的特征提取神经元被激活的图片即可。

基于神经网络的图像风格迁移，其背后的每一步都是前人研究的成果。Gatys 所做的改进是把两个不同领域的研究成果有机地结合起来，做出了令人惊艳的结果，其实最让人惊讶的是，纹理竟然能够和人们心目中认识到的图片风格在很大程度上相吻合。

## 12.2 使用预训练的 VGG16 模型进行风格迁移

本节主要介绍如何使用预训练的 VGG16 模型进行风格迁移。

### 12.2.1 算法思想

卷积是一个有效的局部特征抽取操作。深度学习之所以能“深”，原因之一就是前面的卷积层用少量的参数完成了高效的特征抽取。以图像识别为目的训练出来的卷积神经网络也可以用于图像风格迁移，因为为了完成图像识别的任务，卷积神经网络必须具有抽象和理解图像的能力，即从图像中提取特征。

一般来说，卷积层的特征图蕴含这些特征，对特征图进行处理，就可以提取出图像的内容表示和风格表示，进而进行图像风格迁移。

在卷积神经网络中，通常认为较低层的特征描述了图像的具体视觉特征（即纹理、颜色等），较高层的特征是较为抽象的图像内容描述。当需要比较两幅图像的内容相似性的时候，我们只要比较两幅图像在 CNN 中高层特征的相似性即可。要比较两幅图像的风格相似性，我们需要比较它们在 CNN 中较低层特征的相似性。这意味着，对一幅图像来说，其内容和风格是可分的。

2015 年，Gatys 等人展示了如何从一个预训练的用于图像识别的卷积神经网络模型 VGG 中提取出图像的内容表示和风格表示，并将不同图像的内容和风格融合在一起，生成一幅全新的图像。具体方法是，给定一幅风格图像 a 和一幅普通图像 p，风格图像经过 VGG 的时候在每个卷积层会得到很多特征图，这些特征图组成一个集合 A。同样地，普通图像 p 通过 VGG 的时候也会得到很多特征图，这些特征图组成一个集合 P，然后生成一幅随机噪声图像 x，随机噪声图像 x 通过 VGG 的时候也会生成很多特征图，这些特征图构成集合 G 和 F 分别对应集合 A 和 P，最终的优化函数是希望调整 x，让随机噪声图像 x 最后看起来既保持普通图像 p 的内容，又有一定图像 a 的风格。

## Python 深度学习从零开始学

为了将风格图的风格和内容图的内容进行融合，所生成的图像在内容上尽可能接近内容图，在风格上尽可能接近风格图，因此需要定义内容损失函数和风格损失函数，经过加权后作为总的损失函数（总体 loss）。

总体 loss 的定义如图 12-4 所示。

```
loss函数如下:
loss = distance(style(reference_image) - style(generated_image)) + distance(content(original_image) - content(generated_image))
```

- distance 是一个范数函数 L2 范数
- content 是一个计算图像内容表示的函数
- style 是一个计算图像风格的表示函数

将上面的loss值最小化：会使得 style(generated_image) 接近于 style(reference_image)、content(generated_image) 接近于 content(original_image)，从而实现我们定义的风格迁移。

图 12-4

图 12-4 中公式的意思是希望参考图像与生成图像的风格越接近越好，同时原始图像与生成图像的内容也越接近越好，这样总体损失函数越小，则最终的结果越好。

### 12.2.2 算法细节

这里我们利用 VGGNet 训练好的模型来进行图像风格迁移。我们先来看看称雄于 2014 年 ImageNet（图像分类大赛）的图像识别模型 VGGNet。以 VGG16 为例进行讲解，VGG 结构示意图如图 12-5 所示。VGG 网络非常深，通常有 16 或 19 层，称作 VGG16 或 VGG19，卷积核大小为 3×3，16 和 19 层的区别主要在于后面三个卷积部分卷积层的数量。图中框出来的是 VGG16 示意图。

| ConvNet Configuration |
| A | A-LRN | B | C | D | E |
| 11 weight layers | 11 weight layers | 13 weight layers | 16 weight layers | 16 weight layers | 19 weight layers |
| input (224 × 224 RGB image) |
| conv3-64 | conv3-64 LRN | conv3-64 conv3-64 | conv3-64 conv3-64 | conv3-64 conv3-64 | conv3-64 conv3-64 |
| maxpool |
| conv3-128 | conv3-128 | conv3-128 conv3-128 | conv3-128 conv3-128 | conv3-128 conv3-128 | conv3-128 conv3-128 |
| maxpool |
| conv3-256 conv3-256 | conv3-256 conv3-256 | conv3-256 conv3-256 | conv3-256 conv3-256 conv1-256 | conv3-256 conv3-256 conv3-256 | conv3-256 conv3-256 conv3-256 conv3-256 |
| maxpool |
| conv3-512 conv3-512 | conv3-512 conv3-512 | conv3-512 conv3-512 | conv3-512 conv3-512 conv1-512 | conv3-512 conv3-512 conv3-512 | conv3-512 conv3-512 conv3-512 conv3-512 |
| maxpool |
| conv3-512 conv3-512 | conv3-512 conv3-512 | conv3-512 conv3-512 | conv3-512 conv3-512 conv1-512 | conv3-512 conv3-512 conv3-512 | conv3-512 conv3-512 conv3-512 conv3-512 |
| maxpool |
| FC-4096 |
| FC-4096 |
| FC-1000 |
| soft-max |

图 12-5

VGG16 的网络结构图如图 12-6 所示，VGG16 由 13 个卷积层、5 个池化层和 3 个全连接层组成。

![](img/470b883072d48f1f2917a784b39d4327_203_0.png)

图 12-6

我们要从这个网络结构中提取内容表示与风格表示，并定义对应的内容损失和风格损失。

VGG16 中的浅层，提取的特征往往比较简单（如检测点、线、亮度）；VGG16 中的深层，提取的特征往往比较复杂（如有人脸或某种特定物体）。

VGG16 本意是输入图像，提取特征，并输出图像类别。图像风格迁移正好与其相反，输入的是特征，输出对应这种特征的图像。风格迁移使用卷积层的中间特征还原出对应这种特征的原始图像。比如给出一个原始图像，经过 VGG 计算后得到各个卷积层的特征。接下来，根据这些卷积层的特征，还原出对应这种特征的原始图像。可以发现浅层的还原效果往往比较好，卷积特征基本保留了所有原始图像中形状、位置、颜色、纹理等信息；深层对应的还原图像失去了部分颜色和纹理信息，但大体保留了原始图像中物体的形状和位置。

### 1. 图像的内容表示

要知道两幅图像在内容上是否相似，不能仅仅靠简单的纯像素比较。卷积核能检测并提取图像的特征，卷积层输出的特征图反映了图像的内容。

衡量目标图像和生成图像内容差异的指标，即内容损失函数，可以定义为这两个内容表示之差的平方和，如图 12-7 所示。

Lcontent(p̄, x̄, l) = 1/2 Σ_{i,j} (F_{ij}^l - P_{ij}^l)^2

图 12-7

其中，等式左侧表示在卷积层 l 中，原始图像（P）和生成图像（F）的内容表示，等式右侧是对应的最小二乘法表达式（最小二乘法的思想就是要使得观测点和估计点的距离的平方和达到最小，因为观测点和估计点之差可正可负，简单求和可能将很大的误差抵消掉，只有平方和才能反映二者在总体上的接近程度）。F_ij 表示生成图像第 i 个特征图的第 j 输出值。

### 2. 图像的风格表示

卷积神经网络中的特征图可以作为图像的内容表示，但无法直接体现图像的风格。我们除了还原图像原本的“内容”之外，另一方面还希望还原图像的“风格”。那么，图像的“风格”应该怎么表示呢？“风格”本来就是一个比较虚的东西，没有固定的表示方法。一种方法是使用图像的卷积层特征的 Gram 矩阵，如图 12-8 所示。

Gram 矩阵是一组向量的内积的对称矩阵，例如，向量组 \vec{x}_1, \vec{x}_2, \cdots, \vec{x}_n 的 Gram 矩阵为：

\begin{bmatrix} (\vec{x}_1,\vec{x}_2) & (\vec{x}_1,\vec{x}_2) & \cdots & (\vec{x}_1,\vec{x}_n) \\ (\vec{x}_2,\vec{x}_1) & (\vec{x}_2,\vec{x}_2) & \cdots & (\vec{x}_2,\vec{x}_n) \\ \cdots & \cdots & \cdots & \cdots \\ (\vec{x}_n,\vec{x}_1) & (\vec{x}_n,\vec{x}_2) & \cdots & (\vec{x}_n,\vec{x}_n) \end{bmatrix}

此处的内积通常为欧几里得空间中的标准内积， (\vec{x}_i, \vec{x}_j)=\vec{x}_i^T \vec{x}_j；
设卷积层的输出为 F_l^i，则卷积特征对应的 Gram 矩阵为：

D_{ij}^l = \sum_k F_{ik}^l F_{jk}^l

图 12-8

Gram 矩阵是 Gatys 提出的一个非常神奇的矩阵，从直观上看 Gram 矩阵反映了特征图之间的相关程度。我们将图像在卷积层 L 的风格表示定义为它在卷积层 L 的 Gram 矩阵。Gram 矩阵可以在一定程度上反映原始图像中的 “风格” （涉及复杂的数学知识，这里就不做展开，读者可自行查找相关资料学习）。

仿照 “内容损失”，还可以定义一个 “风格损失”（Style Loss），把每层 Gram 矩阵作为特征，让重建图像的 Gram 矩阵尽量接近原始图像的 Gram 矩阵，这也是个优化问题。卷积层 L 的风格损失公式如图 12-9 所示。

L_{style}(\bar{a}, \vec{x}, l) = \frac{1}{4 N_l^2 M_l^2} \sum_{i,j} (A_{ij}^l - X_{ij}^l)^2

图 12-9

总的风格损失是各卷积层风格损失的加权平均。为了让生成图像拥有原始图像的风格，我们将风格损失函数作为目标函数，从一幅随机生成的图像开始，利用梯度下降最小化风格损失，就可以还原出图像的风格了。

总结一下，到目前为止我们利用内容损失还原图像内容，利用风格损失还原图像风格。那么，可不可以将内容损失和风格损失结合起来，在还原一幅图像的同时还原另一幅图像的风格呢？答案是肯定的，这是图像风格迁移的基本算法。在定义了内容损失和风格损失之后，要生成任务要求的图像，目标就是优化最小化这个总的整体损失函数。

总的损失函数即内容损失函数和风格损失函数的加权，如图 12-10 所示。

L_{total}(\bar{p}, \bar{a}, \vec{x}) = \alpha L_{content}(\bar{p}, \vec{x}) + \beta L_{style}(\bar{a}, \vec{x})

图 12-10

### 12.2.3 代码实现Ⅰ

程序代码实现流程如下：

（1）准备输入图像和风格图像，并将它们调整为相同的大小。

（2）加载预训练的卷积神经网络（VGG16）。

（3）区分负责样式的卷积（基本形状、颜色等）和负责内容的卷积（特定于图像的特征），将卷积分为可以单独地处理的内容和样式。

（4）优化问题，也就是最小化：

- 内容损失（输入和输出图像之间的距离，尽力保留内容）。
- 风格损失（风格和输出图像之间的距离，尽力应用新风格）。
- 总变差损失（正则化，对输出图像进行去噪的空间平滑度）。

（5）最后设置梯度并使用 L-BFGS（Limited-memory BFGS）算法进行优化。L-BFGS 算法是一种解无约束非线性规划问题最常用的方法，具有收敛速度快、内存开销少等优点。

代码实现如下：

首先导入需要的库和函数，如图 12-11 所示，NumPy 用于处理数值计算，预训练的 VGG16 用作图像识别的模型，SciPy 提供 L-BFGS 算法接口。

```
# 导入需要的库和函数
import numpy as np
from PIL import Image
from io import BytesIO
from keras import backend
from keras.models import Model
from keras.applications.vgg16 import VGG16
from scipy.optimize import fmin_l_bfgs_b
```

图 12-11

代码如图 12-12 所示，设置超参数，如内容损失权重 CONTENT_WEIGHT、风格损失权重 STYLE_WEIGHT、总变化损失各自的权重，设置内容图像和风格图像的路径，最后保存。

```
# Hyperparams
ITERATIONS = 10
CHANNELS = 3
IMAGE_SIZE = 500
IMAGE_WIDTH = IMAGE_SIZE
IMAGE_HEIGHT = IMAGE_SIZE
IMAGENET_MEAN_RGB_VALUES = [123.68, 116.779, 103.939]
CONTENT_WEIGHT = 0.02
STYLE_WEIGHT = 4.5
TOTAL_VARIATION_WEIGHT = 0.995
TOTAL_VARIATION_LOSS_FACTOR = 1.25

# Paths
input_image_path = "input.png"
style_image_path = "style.png"
output_image_path = "output.jpg"
combined_image_path = "combined.jpg"
# 内容图像
content_image="content.jpg"
# 风格图像
style_image="style.jpg"
```

图 12-12

显示内容图像，如图 12-13 所示，并且设置固定的图像大小。

```
input_image = Image.open(content_image)
input_image = input_image.resize((IMAGE_WIDTH, IMAGE_HEIGHT))
input_image.save(input_image_path)
input_image
```

![](img/470b883072d48f1f2917a784b39d4327_206_0.png)

图 12-13

显示风格图像，如图 12-14 所示，并且设置固定图像大小。

```
style_image = Image.open(style_image)
style_image = style_image.resize((IMAGE_WIDTH, IMAGE_HEIGHT))
style_image.save(style_image_path)
style_image
```

![](img/470b883072d48f1f2917a784b39d4327_206_1.png)

图 12-14

如图 12-15 所示，将 RGB 图像转换为 BGR，加载 VGG16 网络模型。在 Windows 下，不管是摄像头制造者，还是软件开发者，当时流行的都是 BGR 格式的数据结构，后面 RGB 格式才逐渐开始流行。自己训练的时候完全可以使用 RGB，新库也不存在是 RGB 还是 BGR 这个问题。但如果使用的是别人训练好的模型，就要注意一下使用的是 RGB 还是 BGR。

```
# Data normalisation and reshaping from RGB to BGR
input_image_array = np.asarray(input_image, dtype="float32")
input_image_array = np.expand_dims(input_image_array, axis=0)
input_image_array[:, :, :, 0] -= IMAGENET_MEAN_RGB_VALUES[2]
input_image_array[:, :, :, 1] -= IMAGENET_MEAN_RGB_VALUES[1]
input_image_array[:, :, :, 2] -= IMAGENET_MEAN_RGB_VALUES[0]
input_image_array = input_image_array[:, :, :, ::-1]

style_image_array = np.asarray(style_image, dtype="float32")
style_image_array = np.expand_dims(style_image_array, axis=0)
style_image_array[:, :, :, 0] -= IMAGENET_MEAN_RGB_VALUES[2]
style_image_array[:, :, :, 1] -= IMAGENET_MEAN_RGB_VALUES[1]
style_image_array[:, :, :, 2] -= IMAGENET_MEAN_RGB_VALUES[0]
style_image_array = style_image_array[:, :, :, ::-1]

# Model
input_image = backend.variable(input_image_array)
style_image = backend.variable(style_image_array)
combination_image = backend.placeholder((1, IMAGE_HEIGHT, IMAGE_SIZE, 3))

input_tensor = backend.concatenate([input_image, style_image, combination_image], axis=0)
model = VGG16(input_tensor=input_tensor, include_top=False)
```

图 12-15

使用预训练的 ImageNet 的权重构建 VGG16 网络，参数 include_top=False 表示会载入 VGG16 的模型，不包括加在最后 3 层的全连接层，通常是取得特征通过命令 print(model.summary()) 输出网络模型，结果如下所示；

| Layer (type) | Output Shape | Param # |
|---|---|---|
| input_2 (InputLayer) | (None, None, None, 3) | 0 |
| block1_conv1 (Conv2D) | (None, None, None, 64) | 1792 |
| block1_conv2 (Conv2D) | (None, None, None, 64) | 36928 |
| block1_pool (MaxPooling2D) | (None, None, None, 64) | 0 |
| block2_conv1 (Conv2D) | (None, None, None, 128) | 73856 |
| block2_conv2 (Conv2D) | (None, None, None, 128) | 147584 |
| block2_pool (MaxPooling2D) | (None, None, None, 128) | 0 |
| block3_conv1 (Conv2D) | (None, None, None, 256) | 295168 |
| block3_conv2 (Conv2D) | (None, None, None, 256) | 590080 |
