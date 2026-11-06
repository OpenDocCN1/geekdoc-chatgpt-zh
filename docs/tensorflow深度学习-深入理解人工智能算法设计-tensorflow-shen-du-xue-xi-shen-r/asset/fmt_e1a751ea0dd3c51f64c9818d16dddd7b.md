
`3×3×3×4=108`

分离卷积的第一部分参数量：

`3×3×3×1=27`

第二部分参数量：

`1×1×3×4=14`

分离卷积的总参数量只有39，但是却能实现普通卷积同样的输入、输出尺寸变换。分离卷积在`Xception`和`MobileNets`等对计算代价敏感的领域中得到了大量应用。

## 10.12　深度残差网络

![](OEBPS/Images/Figure-P27_2445.jpg)

图10.62　添加了Skip Connection的`VGG13`网络结构

`AlexNet`、`VGG`、`GoogLeNet`等网络模型的出现将神经网络的发展带入了几十层的阶段，研究人员发现网络的层数越深，越有可能获得更好的泛化能力。但是当模型加深以后，网络变得越来越难训练，这主要是由于梯度弥散和梯度爆炸现象造成的。在较深层数的神经网络中，梯度信息由网络的末层逐层传向网络的首层时，传递的过程中会出现梯度接近于0或梯度值非常大的现象。网络层数越深，这种现象可能会越严重。

如何解决深层神经网络的梯度弥散和梯度爆炸现象？一个很自然的想法是，既然浅层神经网络不容易出现这些梯度现象，那么可以尝试给深层神经网络添加一种回退到浅层神经网络的机制。当深层神经网络可以轻松地回退到浅层神经网络时，深层神经网络可以获得与浅层神经网络相当的模型性能，而不至于更糟糕。

通过在输入和输出之间添加一条直接连接的`Skip Connection`可以让神经网络具有回退的能力。以`VGG13`深度神经网络为例，假设观察到`VGG13`模型出现梯度弥散现象，而10层的网络模型并没有观测到梯度弥散现象，那么可以考虑在最后的两个卷积层添加`Skip Connection`，如图10.62所示。通过这种方式，网络模型可以自动选择是否经由这两个卷积层完成特征变换，还是直接跳过这两个卷积层而选择`Skip Connection`，抑或结合两个卷积层和`Skip Connection`的输出。

2015年，微软亚洲研究院何凯明等人发表了基于`Skip Connection`的深度残差网络(`Residual Neural Network`，`ResNet`)算法[^(\[10\])](#chapter10.xhtml#jz_12_10)，并提出了18层、34层、50层、101层、152层的`ResNet-18`、`ResNet-34`、`ResNet-50`、`ResNet-101`和`ResNet-152`等模型，甚至成功训练出层数达到1202层的极深层神经网络。`ResNet`在`ILSVRC2015`挑战赛`ImageNet`数据集上的分类、检测等任务上面均获得了最好性能，`ResNet`论文至今已经获得超25000条的引用量，可见`ResNet`在人工智能行业的影响力。

### 10.12.1　ResNet原理

`ResNet`通过在卷积层的输入和输出之间添加`Skip Connection`实现层数回退机制，如图10.63所示，输入`x`通过两个卷积层，得到特征变换后的输出![](OEBPS/Images/278_2.png)(`x`)，与输入`x`进行对应元素的相加运算，得到最终输出![](OEBPS/Images/278.png)(`x`)：

![](OEBPS/Images/278.png)(`x`) = `x` + ![](OEBPS/Images/278_2.png)(`x`)

![](OEBPS/Images/278.png)(`x`)称为残差模块(`Residual Block`，`ResBlock`)。由于被`Skip Connection`包围的卷积神经网络需要学习映射![](OEBPS/Images/278_2.png)(`x`) = ![](OEBPS/Images/278.png)(`x`) - `x`，故称为残差网络。

![](OEBPS/Images/Figure-P28_2449.jpg)

图10.63　残差模块

为了能够满足输入`x`与卷积层的输出![](OEBPS/Images/278_2.png)(`x`)相加运算，需要输入`x`的`shape`与![](OEBPS/Images/278_2.png)(`x`)的`shape`完全一致。当出现`shape`不一致时，一般通过在`Skip Connection`上添加额外的卷积运算环节将输入`x`变换到与![](OEBPS/Images/278_2.png)(`x`)相同的`shape`，如图10.63中`identity(x)`函数所示，其中`identity(x)`以`1×1`的卷积运算居多，主要用于调整输入的通道数。

图10.64对比了34层的深度残差网络、34层的普通深度网络以及19层的`VGG`网络结构。可以看到，深度残差网络通过堆叠残差模块，达到了较深的网络层数，从而获得了训练稳定、性能优越的深层网络模型。

### 10.12.2　ResBlock实现

深度残差网络并没有增加新的网络层类型，只是通过在输入和输出之间添加一条`Skip Connection`，因此并没有针对`ResNet`的底层实现。在`TensorFlow`中通过调用普通卷积层即可实现残差模块。

首先创建一个新类，在初始化阶段创建残差块中需要的卷积层、激活函数层等，首先新建![](OEBPS/Images/278_2.png)(`x`)卷积层，代码如下：

![](OEBPS/Images/Figure-P28_2451.jpg)![](OEBPS/Images/Figure-P29_2658.jpg)

图10.64　网络结构比较[^(\[10\])](#chapter10.xhtml#jz_12_10)

![](OEBPS/Images/Figure-P30_2452.jpg)

当![](OEBPS/Images/278_2.png)(`x`)的形状与`x`不同时，无法直接相加，需要新建`identity(x)`卷积层，来完成`x`的形状转换。紧跟上面代码，实现如下：

![](OEBPS/Images/Figure-P30_2453.jpg)

在前向传播时，只需要将![](OEBPS/Images/278_2.png)(`x`)与`identity(x)`相加，并添加`ReLU`激活函数即可。前向计算函数代码如下：

![](OEBPS/Images/Figure-P30_2455.jpg)

## 10.13　DenseNet

`Skip Connection`的思想在`ResNet`上面获得了巨大的成功，研究人员开始尝试不同的`Skip Connection`方案，其中比较流行的就是`DenseNet`[^(\[11\])](#chapter10.xhtml#jz_12_11)。`DenseNet`将前面所有层的特征图信息通过`Skip Connection`与当前层输出进行聚合，与`ResNet`的对应位置相加方式不同，`DenseNet`采用在通道轴`c`维度进行拼接操作，聚合特征信息。

如图10.65所示，输入`X[0]`通过`H[1]`卷积层得到输出`X[1]`，`X[1]`与`X[0]`在通道轴上进行拼接，得到聚合后的特征张量，送入`H[2]`卷积层，得到输出`X[2]`，同样的方法，`X[2]`与前面所有层的特征信息`X[1]`与`X[0]`进行聚合，再送入下一层。如此循环，直至最后一层的输出`X[4]`和前面所有层的特征信息：{`X[i]`, `i`=0，1，2，3}进行聚合得到模块的最终输出。这样一种基于`Skip Connection`稠密连接的模块称为`DenseBlock`。

![](OEBPS/Images/Figure-P31_2659.jpg)

图10.65　Dense Block结构[^((1))](#chapter10.xhtml#jz_1_31)

`DenseNet`通过堆叠多个`Dense Block`构成复杂的深层神经网络，如图10.66所示。

![](OEBPS/Images/Figure-P31_2662.jpg)

图10.66　一个典型的`DenseNet`结构[^((2))](#chapter10.xhtml#jz_2_31)

图10.67比较了不同版本的`DenseNet`的性能、`DenseNet`与`ResNet`的性能比较，以及`DenseNet`与`ResNet`训练曲线。

![](OEBPS/Images/Figure-P32_2461.jpg)

图10.67　`DenseNet`与`ResNet`性能比较[^(\[11\])](#chapter10.xhtml#jz_12_11)

## 10.14　CIFAR10与ResNet18实战

本节将实现18层的深度残差网络`ResNet18`，并在`CIFAR10`图片数据集上训练与测试。并将与13层的普通神经网络`VGG13`进行简单的性能比较。

标准的`ResNet18`接受输入为224×224大小的图片数据，将`ResNet18`进行适量调整，使得它输入大小为32×32，输出维度为10。调整后的`ResNet18`网络结构如图10.68所示。

首先实现中间两个卷积层，`Skip Connection` `1×1`卷积层的残差模块。代码如下：

![](OEBPS/Images/Figure-P32_2462.jpg)![](OEBPS/Images/Figure-P33_2465.jpg)

图10.68　调整后的`ResNet18`网络结构

![](OEBPS/Images/Figure-P34_2471.jpg)

在设计深度卷积神经网络时，一般按照特征图高、宽`h`/`w`逐渐减少，通道数`c`逐渐增大的经验法则。可以通过堆叠通道数逐渐增大的`ResBlock`来实现高层特征的提取，通过`build_resblock`可以一次完成多个残差模块的新建。代码如下：

![](OEBPS/Images/Figure-P34_2472.jpg)

下面实现通用的`ResNet`网络模型。代码如下：

![](OEBPS/Images/Figure-P34_2473.jpg)

通过调整每个`Res Block`的堆叠数量和通道数可以产生不同的`ResNet`，如通过64-64-128-128-256-256-512-512通道数配置，共8个`ResBlock`，可得到`ResNet18`的网络模型。每个`ResBlock`包含了2个主要的卷积层，因此卷积层数量是8×2=16，加上网络末尾的全连接层，共18层。创建`ResNet18`和`ResNet34`可以简单实现如下：

![](OEBPS/Images/Figure-P35_2476.jpg)

下面完成`CIFAR10`数据集的加载工作，代码如下：

![](OEBPS/Images/Figure-P35_2477.jpg)

数据的预处理逻辑比较简单，直接将数据范围映射到[-1，1]。这里也可以基于`ImageNet`数据图片的均值和标准差做标准化处理。代码如下：

![](OEBPS/Images/Figure-P36_2480.jpg)

网络训练逻辑和普通的分类网络训练部分一样，固定训练50个`Epoch`。代码如下

![](OEBPS/Images/Figure-P36_2481.jpg)

`ResNet18`的网络参数量共1100万个，经过50个`Epoch`后，网络的准确率达到了79.3%。这里的实战代码比较精简，在精挑超参数、数据增强等手段加持下，准确率可以达到更高。

## 参考文献

[\[1\]](#chapter10.xhtml#jzyy_12_1)Hinton G E,Osindero S, Teh Y W.A Fast Learning Algorithm for Deep Belief Nets \[J\].Neural Comput., 2006,18(7):1527-1554.

[\[2\]](#chapter10.xhtml#jzyy_12_2)LeCun Y, Boser B, Denker J S,et al.Backpropagation Applied to Handwritten Zip Code Recognition \[J\]. Neural Comput.,1989,1(12): 541-551.

[\[3\]](#chapter10.xhtml#jzyy_12_3)Krizhevsky A,Sutskever I, Hinton G E. ImageNet Classification with Deep Convolutional Neural Networks\[C\]//Pereira F, Burges C J C, Bottou L et al. Advances in Neural Information Processing Systems 25.Curran Associates, Inc., 2012: 1097-1105.

[\[4\]](#chapter10.xhtml#jzyy_12_4)Lecun Y, Bottou L, Bengio Y,et al.Gradient-based learning applied to document recognition\[C\]// Proceedings of the IEEE, 1998.

[\[5\]](#chapter10.xhtml#jzyy_12_5)Zeiler M D,Fergus R.Visualizing and Understanding Convolutional Networks \[C\]//Computer Vision— ECCV 2014, Cham, 2014.

[\[6\]](#chapter10.xhtml#jzyy_12_6)Ioffe S, Szegedy C. Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift\[DB/OL\] .(2019-10-03) \[2015-02-11\].https://arxiv.org/abs/1502.03167.

[\[7\]](#chapter10.xhtml#jzyy_12_7)Wu Y, He K. Group Normalization\[DB/OL\].(2019-10-03) \[2018-03-22\].https://arxiv.org/abs/1803.08494.

[\[8\]](#chapter10.xhtml#jzyy_12_8)Simonyan K, Zisserman A. Very Deep Convolutional Networks for Large-Scale Image Recognition \[DB/OL\].(2019-10-03)\[2014-09-04\].https://arxiv.org/abs/1409.1556.

[\[9\]](#chapter10.xhtml#jzyy_12_9)Szegedy C, Liu W, Jia Y,et al.Going Deeper with Convolutions\[C\]//Computer Vision and Pattern Recognition (CVPR), 2015.

[\[10\]](#chapter10.xhtml#jzyy_12_10)He K, Zhang X, Ren S et al.Deep Residual Learning for Image Recognition\[DB/OL\].(2019-10-03)\[2015-12-10\].https://arxiv.org/abs/1512.03385.

[\[11\]](#chapter10.xhtml#jzyy_12_11)Huang G, Liu Z , Weinberger K Q. Densely Connected Convolutional Networks\[DB/OL\](2019-10-03) \[2016-08-25\] .https://arxiv.org/abs/1608.06993.

[\[12\]](#chapter10.xhtml#jzyy_12_12)Radford A,Metz L,Chintala S, Unsupervised Representation Learning with Deep Convolutional Generative Adversarial Networks \[C \]//International Conference on Learning Representations (ICLR),2015.

[(1)](#chapter10.xhtml#jzyy_1_31)图片来自https://github.com/liuzhuang13/DenseNet

[(2)](#chapter10.xhtml#jzyy_2_31)图片来自https://github.com/liuzhuang13/DenseNet

# 第11章　循环神经网络

人工智能的强力崛起，可能是人类历史上最好的事情，也可能是最糟糕的事情。——史蒂芬·霍金

卷积神经网络利用数据的局部相关性和权值共享的思想大大减少了网络的参数量，非常适合于图片这种具有空间(Spatial)局部相关性的数据，已经被成功地应用到计算机视觉领域的一系列任务上。自然界的信号除了具有空间维度之外，还有一个时间(Temporal)维度。具有时间维度的信号非常常见，例如正在阅读的文本、说话时发出的语音信号、随着时间变化的股市参数等。这类数据并不一定具有局部相关性，同时数据在时间维度上的长度也是可变的，卷积神经网络并不擅长处理此类数据。

如何解决这一类信号的分析、识别等问题是将人工智能推向通用人工智能路上必须解决的一项任务。本章将要介绍的循环神经网络可以较好地解决此类问题。在介绍循环神经网络之前，首先介绍对于具有时间先后顺序的数据的表示方法。

## 11.1　序列表示方法

具有先后顺序的数据一般称为序列(Sequence)，例如随时间而变化的商品价格数据就是非常典型的序列。考虑某件商品A在1～6月的价格变化趋势，记为一维向量：[`x1`，`x2`，`x3`，`x4`，`x5`，`x6`]，它的`shape`为[`6`]。如果要表示`b`件商品在1～6月的价格变化趋势，可以记为2维张量：

![](OEBPS/Images/Figure-P38_2485.jpg)

其中`b`表示商品的数量，张量`shape`为[`b`，6]。
