`h'=w'=2*3=6`

转置卷积的调用方式与普通层并无二致，只要通过 `layers.Conv2DTranspose` 构造一个实例，随后在前向流程中调用即可完成计算：

[PRE0]

### 10.11.3　轻量卷积：深度可分离

以下以深度可分离卷积（Depth-wise Separable Convolution）为例展开。传统卷积面对多通道输入时，每个卷积核会分别与输入各通道做卷积得到多张特征子图，再逐元素相加得到该核的最终输出，详见图10.60。

深度可分离的流程则有所分化：首先让每个卷积核通道与输入对应通道单独卷积，得到一组中间特征，见图10.61。随后对这组中间特征施加若干 1×1 卷积核的点卷积，生成高宽保持不变的多路输出，之后沿通道维度拼接形成最终结果。可以看出，深度可分离实际上拆成两步：第一步仅使用单一核做深度卷积，第二步使用多核做点卷积。

[PRE1]

图10.60　普通卷积计算

[PRE2]

图10.61　深度可分离卷积计算

为什么要采用深度可分离？最直观的好处是参数开销大幅下降，在相同输入输出规模下，`Separable Convolution` 的参数量大约是普通卷积的 [PRE3]。以图10.61 为例：

普通卷积的参数量：

`3×3×3×4=108`

深度可分离第一阶段参数量：

`3×3×3×1=27`

第二阶段参数量：

`1×1×3×4=14`

两者相加仅 39，却能实现与普通卷积一致的尺寸映射。正是这一特性，让深度可分离在 Xception、MobileNets 等对计算成本敏感的模型中被广泛使用。

## 10.12　残差结构：如何让网络更深

[PRE4]

图10.62　给 VGG13 增加 Skip Connection 后的结构

AlexNet、VGG、GoogLeNet 等经典模型把神经网络推向了几十层，研究发现层数提升通常带来泛化能力增强。但模型越深，训练难度越大，根本原因是梯度在反向传播时出现衰减或爆炸。梯度从末端一路向入口传递时，往往变成接近零或异常庞大的数值，这一问题随层数增加而加剧。

怎样缓解深层网络的梯度问题？一个自然思路是让深网具备退回到浅网的能力。若深网络可以轻松退化为浅网络，其性能至少不会劣于浅层版本。

在输入与输出之间搭建一条直连的 Skip Connection 就能提供这种退路。以 VGG13 为例，若发现该模型存在梯度衰减，而 10 层模型并未出现此现象，可在最后两层卷积后加入跳接，如图10.62。此时网络可自行选择：走完整卷积变换、直接走跳接、或两者融合。

2015 年，微软亚洲研究院的何凯明团队提出基于 Skip Connection 的深度残差网络（Residual Neural Network，ResNet）[^(\[10\])](#chapter10.xhtml#jz_12_10)，并发布了 ResNet-18、ResNet-34、ResNet-50、ResNet-101、ResNet-152 等系列模型，甚至训练出 1202 层的超深网络。ResNet 在 ILSVRC2015 ImageNet 分类、检测等任务上取得最佳成绩，论文引用至今超过 25000 次，影响力可见一斑。

### 10.12.1　ResNet 工作机制

ResNet 靠在卷积层输入输出间加入 Skip Connection 实现降层，如图10.63。输入 x 经两层卷积得到变换后的 [PRE5](`x`)，再与原输入 x 逐元素相加得到最终输出 [PRE6](`x`)：

[PRE7](`x`) = `x` + [PRE8](`x`)

[PRE9](`x`) 即为残差模块（Residual Block，ResBlock）。由于被跳接包围的卷积部分只需学习 [PRE10](`x`) = [PRE11](`x`) - `x`，故称残差网络。

[PRE12]

图10.63　残差模块

要进行逐元素相加，x 与 [PRE13](`x`) 的 shape 必须完全一致。若不一致，通常在跳接上加一个额外卷积把 x 映射到相同形状，如图10.63 中的 identity(x) 所示，常见做法是使用 1×1 卷积调整通道数。

图10.64 对比了 34 层残差网络、34 层普通深网和 19 层 VGG 的结构。可看出残差网络通过堆叠残差模块实现了更深且训练更稳定的架构。

### 10.12.2　残差块代码实践

残差网络并未引入新层类型，仅靠跳接即可实现。TensorFlow 中直接调用普通卷积层就能搭建 ResBlock。

先定义一个新类，在初始化阶段创建残差块所需的卷积、激活等层，先创建 [PRE16](`x`) 卷积层，代码如下：

[PRE17][PRE18]

图10.64　网络结构比较[^(\[10\])](#chapter10.xhtml#jz_12_10)

[PRE19]

当 [PRE20](`x`) 的形状与 x 不同时无法直接相加，需再新建 identity(x) 卷积层完成形状转换，紧接上方代码实现如下：

[PRE21]

前向时只需将 [PRE22](`x`) 与 identity(x) 相加再接 ReLU 激活，前向函数代码如下：

[PRE23]

## 10.13　稠密连接网络 DenseNet

Skip Connection 在 ResNet 上取得巨大成功后，研究者探索了更多连接方式，其中最具代表性的是 DenseNet[^(\[11\])](#chapter10.xhtml#jz_12_11)。DenseNet 将前面所有层的特征图通过跳接聚合到当前层，与 ResNet 的逐元素相加不同，DenseNet 在通道维度 c 上进行拼接。

如图10.65，输入 X[0] 经 H[1] 得到 X[1]，随后 X[1] 与 X[0] 在通道上拼接，送入 H[2] 得到 X[2]，以此类推。X[2] 会与之前所有特征 X[1]、X[0] 再次聚合，再进入下一层。循环直至最后一层输出 X[4] 与之前所有 {X[i], i=0,1,2,3} 聚合，形成模块最终输出。这种稠密连接模块称为 DenseBlock。

[PRE24]

图10.65　Dense Block 结构^((1))

DenseNet 由多个 Dense Block 堆叠构成，如图10.66 所示。

[PRE25]

图10.66　一个典型的 DenseNet 结构^((2))

图10.67 展示了不同 DenseNet 版本的性能、与 ResNet 的对比以及训练曲线。

[PRE26]

图10.67　DenseNet 与 ResNet 性能比较[^(\[11\])](#chapter10.xhtml#jz_12_11)

## 10.14　CIFAR-10 上实战 ResNet18

本节在 CIFAR10 上实现 18 层 ResNet 并与 13 层 VGG13 做简单对比。标准 ResNet18 原本接收 224×224 输入，这里将其调整为 32×32 输入、10 类输出，网络结构见图10.68。

首先实现包含两层卷积与 1×1 跳接卷积的残差模块，代码如下：

[PRE27][PRE28]

图10.68　调整后的 ResNet18 网络结构

[PRE29]

设计深层卷积网络时通常遵循高宽 h/w 递减、通道数 c 递增的原则。可通过堆叠通道数递增的 ResBlock 提取高层特征，用 build_resblock 一次性生成多个模块，代码如下：

[PRE30]

下面给出通用 ResNet 模型实现，代码如下：

[PRE31]

调节每个 ResBlock 的堆叠数与通道数即可得到不同 ResNet，例如采用 64-64-128-128-256-256-512-512 通道配置，共 8 个 ResBlock，可得到 ResNet18。每个 ResBlock 含 2 个主卷积，共 16 个卷积层，加上末尾全连接共 18 层。创建 ResNet18 与 ResNet34 可简化为：

[PRE32]

随后加载 CIFAR10 数据集，代码如下：

[PRE33]

预处理很简单，将像素范围映射到 [-1, 1]。也可采用 ImageNet 均值方差做标准化，代码如下：

[PRE34]

训练流程与普通分类网络一致，固定训练 50 个 Epoch，代码如下：

[PRE35]

ResNet18 参数量约 1100 万，训练 50 Epoch 后准确率达到 79.3%。代码较为精简，经超参数优化与数据增强后表现还能进一步提升。

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

[\[11\]](#chapter10.xhtml#jzyy_12_11)Huang G, Liu Z , Weinberger K Q. Densely Connected Convolutional Networks\DB/OL\ \[2016-08-25\] .https://arxiv.org/abs/1608.06993.

[\[12\]](#chapter10.xhtml#jzyy_12_12)Radford A,Metz L,Chintala S, Unsupervised Representation Learning with Deep Convolutional Generative Adversarial Networks \[C \]//International Conference on Learning Representations (ICLR),2015.

(1)图片来自 https://github.com/liuzhuang13/DenseNet

(2)图片来自 https://github.com/liuzhuang13/DenseNet

# 第 11 章　循环神经网络：时序建模入门

人工智能的强势崛起，或许是人类历史上最美好的事，也可能是最糟糕的事。——史蒂芬·霍金

卷积神经网络凭借局部相关性和权值共享大幅降低参数，非常适合空间局部相关的数据，已在计算机视觉中取得广泛成功。自然界信号除空间维度外还存在时间维度，例如文本、语音、股市时序等。这类数据往往不具备局部相关性，且长度可变，卷积网络处理起来并不理想。