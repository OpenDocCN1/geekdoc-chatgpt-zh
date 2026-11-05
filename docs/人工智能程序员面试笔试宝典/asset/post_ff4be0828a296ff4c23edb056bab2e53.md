
第二个池化层pool2，经过3×3的最大池化核，stride=2，输出特征图边长大小为（27-3）/2+1=13，输出为13×13×256。

第二个归一化层norm2，local_size=5，输出为13×13×256。

第三个卷积层conv3，使用3×3的卷积核，输出384个通道，pad=1，stride=1，输出特征图边长大小等于（13+1×2-3）/1+1=13，输出为13×13×384。

第四个卷积层conv4，使用3×3的卷积核，输出384个通道，pad=1，stride=1，输出特征图边长大小等于（13+1×2-3）/1+1=13，输出为13×13×384。

第五个卷积层conv5，使用3×3的卷积核，输出256个通道，pad=1，stride=1，输出特征图边长大小等于（13+1×2-3）/1+1=13，输出为13×13×256。

第三个池化层pool5，经过（3×3）的最大池化核，stride=2，输出特征图边长大小等于（13-3）/2+1=6，输出为6×6×256。

第一个全连接层fc6，包含（6×6×256）×4096个全连接，输出为1×1×4096。

第一个dropout层为dropout6，在训练的时候以1/2概率使得隐藏层的某些神经元的输出为0，这样就丢掉了一半结点的输出，BP的时候也不更新这些结点，以下dropout同理。

第二个全连接层fc7，输入为1×1×4096，输出为1×1×4096，总参数量为4096×4096。

第二个dropout层为dropout7，输出为1×1×4096。

第三个全连接层fc8，输入为1×1×4096，输出为1000，总参数量为4096×1000。

总结AlexNet网络的结构如下：

- 1）网络比LeNet5更深，包括5个卷积层和3个全连接层，最终AlexNet总体的數據参数大概为240MB。
- 2）使用ReLU激活函数，收敛很快，解决了Sigmoid在网络较深时出现的梯度弥散问题。
- 3）加入了dropout层，防止过拟合。在训练的时候，dropout会随机丢弃神经元的输出，即设定对应的权重参数都为0。在测试的时候，则会将所有神经元的输出，乘以0.5来进行近似。
- 4）使用了LRN归一化层，对局部神经元的活动创建竞争机制，抑制反馈较小的神经元放大反应大的神经元，增强了模型的泛化能力。
- 5）使用了新型的数据增强方式，使用裁剪、翻转等操作用来做数据增强，增强了模型的泛化能力。预测时使用提取图片4个角加中间5个位置并进行左右翻转一共十幅图片的方法求取平均值，这也是后面刷比赛的基本使用技巧。
- 6）分块训练，当年的GPU是GTX 580，显存只有3GB，限制了网络的规模，AlexNet创新地将图像分为上下两块分别训练，然后在全连接层合并在一起。

这里的并行除了将模型的神经元进行了并行，还使得通信被限制在了某些网络层。比如第三层卷积要使用第二层所有的特征图，但是第四层却只需要同一块GPU中的第三层的特征图，如何去进行这样的选择方案的设定，需要进行多次实验的交叉验证。与只使用一个GPU，也就是通道减半的网络相比，性能提升了不少。不过，如今这样的方案已经不需要了，因为GPU的显存越来越大，而神经网络的参数量和结构也得到了更好的优化，可以说，AlexNet网络的成功是大数据，网络深度增加以及使用了若干新的训练技巧的共同结果。

AlexNet代码展示：

```
#输入大小为 224×224
from torch import nn
from torch.nn import functional as F
from torch.autograd import Variable
class AlexNet(nn.Module):
    def __init__(self, num_classes=1000):
        super(AlexNet, self).__init__()
        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(64, 192, kernel_size=5, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            nn.Conv2d(192, 384, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.Conv2d(384, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.Conv2d(256, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
        )
        self.classifier = nn.Sequential(
            nn.Linear(256 * 6 * 6, 4096),
            nn.ReLU(inplace=True),
            nn.Linear(4096, 4096),
            nn.ReLU(inplace=True),
            nn.Linear(4096, num_classes),
        )

    def forward(self, x):
        x = self.features(x)
        x = x.view(x.size(0), 256 * 6 * 6)
        x = self.classifier(x)
        return x
```

## 4.2.7 什么是VGG16

VGG16是牛津大学VGG组提出的。VGG16相比AlexNet的一个改进是采用连续的几个3×3的卷积核代替AlexNet中的较大卷积核（11×11，5×5）。对于给定的感受野（与输出有关的输入图片的局部大小），采用堆积的小卷积核是优于采用大的卷积核，因为多层非线性层可以增加网络深度来保证学习更复杂的模式，而且代价还比较小（参数更少）。

AlexNet是一个8层的神经网络，在2012年以前8层是一个很深的网络，但是现在看来是一个很浅的网络，在两年后VGGNet直接将网络的深度提升了一倍，性能也大幅度提升。

AlexNet使用了11×11的卷积核和大小等于4的步长，Zeiler和Fergus在zfnet中对其进行了改进，卷积大小变为了7×7，获得了更好的性能。

研究表明使用更小的卷积是有利的，在这样的背景下2014年牛津大学视觉组提出了VGGNet，分别在ImageNet的定位和分类任务中取得第一名和第二名。

VGG网络结构中全部卷积核大小为3×3，通过对逐渐加深的网络进行评估，结果表明加深网络深度至16～19层可以极大地改进前人的网络结构。

VGG使用了更小的卷积核和更小的stride，不仅可以增加网络的非线性表达能力，还可以减小计算量。

下面以VGG16的网络结构为例进行详细介绍，它共有13个卷积层，3个全连接层，全部采用3×3大小的卷积核，步长为1，和2×2大小的最大池化核，步长为2。

首先是输入层，输入图片为224×224×3，在这一层进行了减均值预处理，减去均值可以一定程度上加速网络的训练，对于16层的标准VGG16，接下来的网络配置依次为：

CONV3-64，经过（3×3×3）×64次卷积，生成featuremap为224×224×64。

CONV3-64，经过（3×3×64）×64次卷积，生成featuremap为224×224×64。

Max pool，经过（2×2）的max pool，生成featuremap为112×112×64。

CONV3-128，经过（3×3×64）×128次卷积，生成featuremap为112×112×128。

CONV3-128，经过(3×3×128)×128次卷积，生成featuremap为112×112×128。

Max pool，经过(2×2)的maxpool，生成featuremap为56×56×128。

CONV3-256，经过(3×3×128)×256次卷积，生成featuremap为56×56×256。

CONV3-256，经过(3×3×256)×256次卷积，生成featuremap为56×56×256。

CONV3-256，经过(3×3×256)×256次卷积，生成featuremap为56×56×256。

Max pool，经过(2×2)的maxpool，生成featuremap为28×28×256

CONV3-512，经过(3×3×256)×512次卷积，生成featuremap为28×28×512

CONV3-512，经过(3×3×512)×512次卷积，生成featuremap为28×28×512。

CONV3-512，经过(3×3×512)×512次卷积，生成featuremap为28×28×512。

Max pool，经过(2×2)的maxpool，生成featuremap为14×14×512。

CONV3-512，经过(3×3×512)×512次卷积，生成featuremap为14×14×512。

CONV3-512，经过(3×3×512)×512次卷积，生成featuremap为14×14×512。

CONV3-512，经过(3×3×512)×512次卷积，生成featuremap为14×14×512。

Max pool，经过(2×2)的maxpool，生成featuremap为7×7×512。

FC-4096，输入为7×7×512，输出为1×1×4096，总参数量为7×7×512×4096。

FC-4096，输入为1×1×4096，输出为1×1×4096，总参数量为4096×4096。

FC-1000，输入为1×1×4096，输出为1000，总参数量为4096×1000。整个的VGG网络共包含参数约为550M，全部使用3×3的卷积核和2×2的最大池化核，简化了卷积神经网络的结构。

与AlexNet网络结构相比，VGG有几点重大的不同。

### （1）卷积核

VGG网络相比于AlexNet网络，只使用了3×3的卷积，网络的结构更加简单。相比于AlexNet第一个卷积层输出96维，卷积大小为11×11，VGG第一个卷积层的参数量和计算量都要小很多。AlexNet参数量为96×3×11×11，VGG参数量为64×3×3×3，64×3×3×3/(96×3×11×11)=0.05，即VGG第一层卷积的参数量只有AlexNet的1/20不到，计算量也差不多。这样一来，虽然网络的深度增加了很多，但是模型参数却不比有较大的卷积感受野和较多特征图的浅层网络多。

### （2）池化

在VGG网络中，池化的核大小为2×2，步长为2，而AlexNet中采用的是重叠池化方案，核大小为3×3，步长为2。至于是否重叠池化好还是不重叠池化好，在AlexNet中观测到，有重叠的池化相比不重叠池化能够降低过拟合问题，不过不重叠池化计算量低一些。

### （3）数据增强

在VGG中使用了比AlexNet更复杂的多尺度增强方案，即Scale Jittering。先固定一种裁剪的尺寸为m×m，比如通常为224×224，然后把图片短边缩放到一个大于m的值，比如[256, 288]，最后再裁剪出m×m。相对于先把图片缩放到固定的大小，这个能操作的空间更大，不过也可能裁剪掉了重要的区域。

下面这段代码即展示了VGG16网络的实现过程，代码中共有13个卷积层，3个全连接层，全部采用3×3大小的卷积核2×2大小的最大池化核。

```
class VGG16(network.Module):
    def __init__(self):
        super(VGG16, self).__init__()
        self.layer1 = network.Sequential(
            network.Conv3d(3, 64, kernel_size=3, padding=1),
            network.BatchNorm3d(64),
            network.ReLU(),
            network.Conv3d(64, 64, kernel_size=3, padding=1),
            network.BatchNorm3d(64),
            network.ReLU(),
            network.MaxPool3d(kernel_size=2, stride=2))

        self.layer2 = network.Sequential(
            network.Conv3d(64, 128, kernel_size=3, padding=1),
            network.BatchNorm3d(128),
            network.ReLU(),
            network.Conv3d(128, 128, kernel_size=3, padding=1),
            network.BatchNorm3d(128),
            network.ReLU(),
            network.MaxPool3d(kernel_size=2, stride=2))

        self.layer3 = network.Sequential(
            network.Conv3d(128, 256, kernel_size=3, padding=1),
            network.BatchNorm3d(256),
            network.ReLU(),
            network.Conv3d(256, 256, kernel_size=3, padding=1),
            network.BatchNorm3d(256),
            network.ReLU(),
            network.MaxPool3d(kernel_size=2, stride=2))

        self.layer4 = network.Sequential(
            network.Conv3d(256, 512, kernel_size=3, padding=1),
network.BatchNorm3d(512),
network.ReLU(),
network.Conv3d(512, 512, kernel_size=3, padding=1),
network.BatchNorm3d(512),
network.ReLU(),
network.MaxPool3d(kernel_size=2, stride=2))

self.layer5 = network.Sequential(
    network.Conv3d(512, 512, kernel_size=3, padding=1),
    network.BatchNorm3d(512),
    network.ReLU(),
    network.Conv3d(512, 512, kernel_size=3, padding=1),
    network.BatchNorm3d(512),
    network.ReLU(),
    network.MaxPool3d(kernel_size=2, stride=2))

self.layer6 = network.Sequential(
    network.Linear(4096, 4096),
    network.BatchNorm1d(4096),
    network.ReLU())

self.layer7 = network.Sequential(
    network.Linear(4096, 4096),
    network.BatchNorm1d(4096),
    network.ReLU()

self.layer8 = network.Sequential(
    network.Linear(4096, 1000)
    network.BatchNorm1d(1000),
    network.Softmax()
#layer6、layer7、layer8 即对应的是先前介绍的 FC-4096、FC-4096 和 FC-1000

def forward(self, x):
    out = self.layer1(x)
    out = self.layer2(out)
    out = self.layer3(out)
    out = self.layer4(out)
    out = self.layer5(out)
    vgg16_features = out.view(out.size(0), -1)
    out = self.layer6(vgg16_features)
    out = self.layer7(out)
    out = self.layer8(out)
    return vgg16_features, out
```

## 4.3 LSTM基础知识与PyTorch实战部分

由于传统的神经网络没有设计记忆结构，因此在处理序列数据上无所适从（即便经过特殊的处理），这不仅导致工作量变大，预测的结果也会受到很大的影响。循环神经网络（Recurrent Neural Network，RNN）针对BP神经网络的缺点，增加了信息跨时传递的结构。

对于RNN而言，每个时刻的隐藏层除了连接本期的输入层和输出层，还连接着上一时刻和下一时刻的隐藏单元，这也就是历史信息传递的方式。过去的信息正是通过这样的结构影响当期的输出。此外，在模型训练上，RNN虽然也是采用反向传播的方式，但跟BP模型不一样的是，它还包含从最后一个时间将累积的残差传递回来的过程。这种方法被称为基于时间的反向传播（Back Propagation Through Time，BPTT）。

传统的神经网络模型面对许多问题显得无能为力，因为同层的结点之间无连接，网络的传播也是顺序的。而循环神经网络对序列化数据有很强的模型拟合能力，因为它相比起ANN来讲是一种具有反馈结构的神经网络，其输出不但与当前输入和网络的权值有关，而且也与先前网络的输入有关。本节将介绍循环神经网络（RNN）及其变种长短时记忆网络（LSTM）、门控循环神经网络（GRU）。

### 4.3.1 什么是循环神经网络（RNN）

循环神经网络在隐含层会对之前的信息进行存储记忆，然后输入到当前计算的隐含层单元中，也就是隐含层的内部结点不再是相互独立的，而是互相有消息传递。RNN通过隐层神经元的记忆功能，实现了所有时刻的权重矩阵共享。在学习同样数量权重的情况下，相比于前馈神经网络，RNN用到的信息更少，也就是说在同样的信息下，RNN学习能力更强。其内部结构如图4-9所示。
