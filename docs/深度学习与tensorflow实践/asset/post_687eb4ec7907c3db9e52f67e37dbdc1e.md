
[4] MINSKY M, PAPERT S A. Perceptrons: An introduction to computational geometry[M]. USA: MIT press, 2017.

[5] 张钱, 朱军, 苏航. 迈向第三代人工智能[J]. 中国科学: 信息科学, 2020, 50: 1281-1302.

[6] HORNIK K, STINCHCOMBE M, WHITE H. Multilayer feedforward networks are universal approximators[J]. Neural networks, 1989, 2(5): 359–366.

[7] 邱锡鹏. 神经网络与深度学习[M]. 北京: 机械工业出版社, 2020.

[8] MICHAEL A. NIELSEN. A visual proof that neural nets can compute any function[EB/OL]. Neural Networks and Deep Learning. http://neuralnetworksanddeeplearning.com/chap4.html.

[9] NAIR V, HINTON G E. Rectified linear units improve restricted boltzmann machines[C]//Proceedings of the 27th international conference on machine learning (ICML-10). 2010: 807–814.

[10] LENNIE P. The cost of cortical computation[J]. Current biology, 2003, 13(6): 493–497.

[11] Maas A L, Hannun A Y, Ng A Y. Rectifier nonlinearities improve neural network acoustic models[C]//Proc. icml. 2013, 30(1): 3.

[12] CLEVERT D A, UNDERTHINER T, HOCHREITER S. Fast and accurate deep network learning by exponential linear units (ELUs)[J]. arXiv preprint arXiv:1511.07289, 2015.

[13] HENDRYCKS D, GIMPEL K. Gaussian error linear units (GELUs)[J]. ArXiv preprint arXiv:1606.08415, 2016.

[14] SITZMANN V, MARTEL J N P, BERGMAN A W, et al. Implicit Neural Representations with Periodic Activation Functions[J]. arXiv preprint arXiv:2006.09661, 2020.

[15] FORINA M, ARMANINO C, CASTINO M, 等. Multivariate data analysis as a discriminating method of the origin of wines[J]. Vitis, 1986, 25(3): 189–201.

[16] [美]王维嘉. 暗知识: 机器认知如何颠覆商业和社会[M]. 北京: 中信出版社. 2019.

## Deep Learning & TensorFlow

## 第 3 章 初识 TensorFlow

它山之石，可以攻玉。为了提高机器学习项目的开发效率，通常需要计算框架为我们助上一臂之力。由谷歌荣誉出品的 TensorFlow，则是深度学习框架的杰出代表。在本章，我们主要介绍 TensorFlow 的安装和 TensorFlow 2 的新特性。

《荀子·劝学》中有句名言，“君子生非异也，善假于物也”。说的是，君子的资质与一般人没什么区别，之所以君子高常人一等，是因为他能善于利用外物。因此，善于利用已有条件，是君子成功的一个重要途径。这句话对从事深度学习工作的算法工程师而言，同样是适用的。

在前面的章节中，通过对 sklearn 的初步使用，我们已经感受到，利用计算框架可以给我们带来更多开发的便捷和高效。对深度学习而言，TensorFlow 也是一个备受业界推崇的计算框架，值得我们掌握。

### 3.1 TensorFlow 概述

我们知道，在与计算机相关的很多领域（如大数据处理、分布式计算和智能搜索等）中，Google（谷歌）公司都有卓越的表现，为计算机世界贡献了很多划时代的产品。在人工智能领域，它的贡献依然可圈可点。2011 年，Google 公司开发了它的第一代分布式机器学习系统 DistBelief[1]。著名计算机科学家杰夫·迪恩（Jeff Dean）和深度学习专家吴恩达（Andrew Y. Ng）都是这个项目的核心成员。

通过迪恩等人设计的 DistBelief，Google 可利用数据中心数以万计的 CPU 核，并以此建立深度神经网络。借助 DistBelief，Google 的语音识别准确率比之前提高了 25%。除此之外，DistBelief 在图像识别上也大显神威。

很显然，DistBelief 已然具备一定程度的自学能力。当然，这套系统的计算开销亦不容小觑。它由 1000 台机器组成，共包括 16000 个内核，训练的神经网络参数高达百亿级别。

一开始，DistBelief 作为谷歌 X-实验室的“黑科技”也是闭源的，可能是 Google 想让开源社区来维护 DistBelief。现如今，开源社区之所以红红火火，就是因为，根据相关规则，使用开源代码的个人和机构，必须让自己在开源代码的基础上再开发代码，继续保持开源。这样一来就形成了“人人为我，我为人人”的发展态势，进而形成了群策群力力量大的格局。因此，在 2015 年 11 月，Google 将 DistBelief 的升级版实现正式开源，其协议遵循 Apache 2.0。而这个升级版的 DistBelief 也有了一个新的名称，它就是本章的主角——TensorFlow[2]，其图标如图 3-1 所示。

为什么要取这么一个名字呢？这自然也是有讲究的。TensorFlow 的命名源于其运行原理，“Tensor”的本意是“张量”，“张量”通常表示多维数组。在深度学习项目中，数据大多都高于二维，所以利用深度学习处理的数据核心特征来命名是有意义的。“Flow”的本意就是“流动”，它意味着基于数据流图的计算。

![](img/aa01495b859013c80481c58ec0894987_74_0.png)

图 3-1 TensorFlow 的图标

![](img/aa01495b859013c80481c58ec0894987_75_0.png)

合在一起，“TensorFlow”的意思是，张量从数据流图的一端流动到另一端的计算过程。而数据的流动其实就是计算的过程，这正是 TensorFlow 描述的本质所在，它生动形象地描述了复杂数据结构在人工神经网络中的流动、传输、分析和处理过程（见图 3-2）。笼统来讲，在这个图中，节点代表操作（Operation，Op），如其中的加法、乘法及激活函数操作，图中的“边”表示张量（即数据）流过系统。在后续的章节中，我们会详细讨论这些概念。

图 3-2 TensorFlow 中的数据流图

### 3.2 TensorFlow 特征

从上面的介绍可以看出，TensorFlow 是一款用于数值计算的深度学习框架，它提供封装好的应用程序接口（Application Programming Interface，API），有了这些 API 的“保驾护航”，使用户的开发效率大大提高。当然，这些机器学习算法也包括目前主流的深度学习算法。

作为一款优秀的深度学习框架，TensorFlow 在诸多方面都有着卓越的表现。例如，设计神经网络结构的代码非常简捷，部署也比较便利。特别是有技术实力雄厚的 Google 做技术支撑，拥护者众多，也在很大程度上保证了其社区的活跃度，从而也导致 TensorFlow 在技术演化之路上更新迭代非常快，基本上每周都有上万行代码的提交。

- ① 在机器学习中，数值通常由以下 4 种类型构成。（1）标量（Scalar）：一个数值，它是计算的最小单元，如 “1” 或 “3.2” 等。（2）向量（Vector）：由一些标量构成的一维数组，如[1, 3.2, 4.6]等。（3）矩阵（Matrix）：由标量构成的二维数组。（4）张量（Tensor）：由多维（通常 n >= 3）数组构成的数据集合，可理解为高维矩阵。

TensorFlow 的优点主要表现在如下 4 个方面。

- （1）TensorFlow 有一个非常直观的架构。顾名思义，它有一个“张量流”。用户可以很容易地、可视化地看到张量流动的每一个环节（需要借助 TensorBoard，在后面的章节会有所提及）。
- （2）TensorFlow 的设计是可移植的。它可轻松地在 CPU、GPU 上部署。经过少许修改或基本不改动的代码，可在云计算、集群等平台进行分布式计算，为大数据分析提供了技术支撑。
- （3）跨平台性好，灵活性强。TensorFlow 不仅可在 Linux、macOS 和 Windows 系统中运行，甚至还可在移动终端下工作。
- （4）调试方便，提供“即时执行”功能。即时执行（Eager Execution）是一个命令式、由运行定义的接口，一旦调用，可立即执行操作，这使得 TensorFlow 的调试变得更简单，也使得运行变得更直观。

TensorFlow 的核心 API 主要由 C++语言编写而成，但为了使用方便，它还提供了多种高级语言的前端接口，包括 Python、C++、Java、JavaScript 及 Go 语言等。

当然，TensorFlow 也有不足之处，其主要表现在：相比于其他计算框架，它的代码偏底层，依然需要用户编写大量代码，而且有很多相似的功能，用户不得不“重造轮子”。但瑕不掩瑜，TensorFlow 还是以雄厚的技术积淀、稳定的性能，“笑傲”于众多深度学习框架之巅。

### 3.3 深度学习框架比较

“工欲善其事，必先利其器。”事实上，适用于深度学习的“器”有很多，如 Theano、Keras、Caffe 及 PyTorch 等，它们各有特色。下面我们对这几款比较流行的深度学习框架分别给予简单的介绍，为读者提供一个宏观的认知。

#### 3.3.1 Theano

Theano 是一个偏向底层的深度学习框架，它开启了基于符号运算的机器学习框架的先河（见图 3-3）。Theano 支持自动的函数梯度计算，带有 Python 接口，并集成了 NumPy。所以，从严格意义上来说，Theano 就是一个基于 Python 和 NumPy 而构建的数值计算工具包。

![](img/aa01495b859013c80481c58ec0894987_76_0.png)

相比于 TensorFlow、Keras 等框架，Theano 更显学术范儿，因为它并没有提供专门的深度学习 API 接口。如 Theano 没有神经网络的分级。因此，用户需要从底层开始，做许多工作，来创建自己需要的模型。

框架毕竟就是一个半成品，最终要能出活，才是硬道理。受限于其底层特性，Theano 在出活效率上，表现平平。当开发效率更高的后起之秀蜂拥而出时，也该是它退出江湖之日。

Theano 的开发始于 2007 年，早期的开发者包括当今深度学习大家约书亚·本吉奥（Yoshua Bengio）和他的学生伊恩·古德费洛（Ian Goodfellow，GAN 框架的提出者）。在 2017 年 9 月 29 日，本吉奥宣布，在发布 Theano 1.0 版本后，Theano 光荣退休。

虽然 Theano 已淡出历史舞台，但其功绩不可磨灭，其最大的“技术遗产”在于，它极大地启迪或培养了其他深度学习框架。在某种程度上，TensorFlow、Keras 都继承了它的部分基因。

#### 3.3.2 Keras

Keras 是一个纯 Python 编写的深度学习库，它提供很多高级神经网络 API，通过一系列的配置，它可以工作在 CNTK（微软公司开发的一款深度学习开发框架）、Theano 和 TensorFlow 等框架之上。因此，Keras 可被视为在上述框架下的二次封装。也就是说，如果想用 Keras，那么必须预先安装上述框架的一种或多种。

Keras 的语法简捷，用户可以直观地了解它的指令、函数和每个模块之间的连接方式。作为极简主义的代表，Keras 经过高度封装，在一些场景下，仅需几行代码就能构建一个能够正常工作的神经网络，或用十几行代码就能搭建一个 AlexNet 网络（2012 年，ImageNet 竞赛冠军 Alex Krizhevsky 设计的卷积神经网络）。这种高效性是其他深度学习框架难以企及的。

![](img/aa01495b859013c80481c58ec0894987_77_0.png)

图 3-4 Keras 的图标与口号

这样看来，Keras 简直太“平易近人”了。Keras 的宣传口号是 “You have just found Keras.”（你才发现 Keras）。但有时候，某个事物的优点也恰是它的缺点，就看你从哪个角度来审视它了。Keras 的高度封装性，确实让用户的开发效率变得很高，但也因为它的高度封装性，不容你置喙于它的个性化设置。Keras 的图标与口号如图 3-4 所示。

Keras 最初是由 Google AI 开发人员弗朗索瓦·肖莱（Francois Chollet）创建和开发的。Google 员工和 Keras 创始人，这个双重身份，就决定了 Keras 与 TensorFlow 有着千丝万缕的联系。

一开始，TensorFlow 仅仅是 Keras 的后台（Backend）之一。但在 TensorFlow 1.10 版本之后，TensorFlow 开始引入 tf.keras 子模块，这是将 Keras 集成在 TensorFlow 中的第一步。2019 年 6 月，Google 发布 TensorFlow 2，直接宣布 Keras 成为 TensorFlow 的官方高级 API 的一部分。

鉴于 Keras 和 TensorFlow 有“合二为一”的趋势，所以，在本书后续章节的代码，将会是这两大框架的混合使用。

#### 3.3.3 Caffe

Caffe 的成名，最早源于在加州大学伯克利分校（University of California, Berkeley）博士生贾扬清撰写的一篇论文——Caffe: Convolutional architecture for fast feature embedding[3]。顾名思义，Caffe 的主要用途体现在“用于快速特征提取的卷积架构”。这篇有关 Caffe 的论文，一经面世，就受到世人的极大关注，“谷歌学术”上显示它的引用次数已超过 14000 次，可见其受关注程度之高。目前，Caffe 已被广泛应用在工业界和学术界，Caffe 的图标如图 3-5 所示。

设计之初，Caffe 仅关注计算机视觉领域问题的处理，因此，可将其视为一个面向图像处理的专用卷积神经网络（CNN）框架。但随着它的开源（遵循 BSD 协议），社区人员不断“添砖加瓦”，让它的通用性日臻完美，它也开始在文本、语音及时间序列数据等领域，有着非凡的表现。

Caffe 的一个突出优势是，它有一个模型百宝箱——Model Zoo（模型动物园），在这个园子里，汇集了大量已经训练好的经典模型，如 AlexNet、VGG、Inception、ResNet 等。这样一来，一些常用模型根本无须用户训练，选好拿来就可使用，节省了大量的模型训练时间。如果说高度封装性成就了 Keras 的“极简主义”，那么 Model Zoo 成就了 Caffe 的“极速主义”。

“黄金无足色，白璧有微瑕”。Caffe 自然也有其不足之处。首先，它不够灵活，模型虽然可以拿来就用，但倘若需要对模型做一些小的变更，都需要用户利用 C++ 和 CUDA 更新底层代码。

其次，源于其诞生基因，Caffe 天生对卷积神经网络有着卓越的支持，但对于递归神经网络（Recurrent Neural Network，RNN）和“长短期记忆”（Long Short-Term Memory，LSTM）的支持，表现不佳。

目前，Caffe 慢慢退出深度学习框架的前台，逐渐变成 PyTorch 的后台（Backend）。

![](img/aa01495b859013c80481c58ec0894987_78_0.png)

#### 3.3.4 PyTorch

PyTorch 是由 Torch7 团队开发的一款深度学习框架，它是非常有潜力的后起之秀。从历史渊源上来讲，它脱胎于另一款深度学习框架——Torch。Torch 本身也很优秀，但它的编程语言是 Lua，这是一门用 C 语言编写的、可扩展的轻量级编程语言。正由于 Lua 语言的小众化，导致 Torch 的受众也有限，这成为 Torch 发展的一大障碍。

![](img/aa01495b859013c80481c58ec0894987_79_0.png)

图 3-6 PyTorch 的图标

目前，深度学习社区的编程语言，绝大多数以 Python 为主。在这个大趋势下，“识时务者为俊杰”，Torch7 团队改用 Python 重新开发了 Torch，取名为 PyTorch，其图标如图 3-6 所示。

PyTorch 非常年轻，2017 年 3 月才开源发布。一经发布，PyTorch 就受到了社区的热烈支持。PyTorch 的设计思路是线性的，代码直观易用，调试方便。它的主要优点还在于，能够支持动态神经网络，通过一种反向自动求导计数，可以让用户零延迟地改变神经网络的行为。完善而清晰的文档支持，简捷而优雅的语法构成，特别受到学术界的支持，大有超越 TensorFlow 之势。

当然，PyTorch 也有不足。它最大的不足之处就在于“大年轻”，生态还有待改善。但后生可畏，不容小觑，特别是有 Facebook 这样的大公司做技术支撑，且把 Caffe 作其后端，方便性和实用性都大大增强，目前 PyTorch 在学术界已占据半壁江山。

> 根据“存在即合理”的哲学理念，之所以这些框架可并存、能共进，肯定是因为它们各自找到了有自己存在价值的细分市场。

事实上，有关深度学习的计算框架层出不穷，远远不止前文提及的这几个。例如，比较有名的框架还有 Chainer（由日本公司 Preferred Networks 领导开发的开源深度学习框架，PyTorch 就从该框架借鉴很多简捷的设计风格）、MXNet（由李沐等人开发的支持多语言开发的深度学习框架，现被亚马逊官方采用）[4]。中国科技公司也开始在深度学习框架中暂露头角，如百度公司的飞桨（PaddlePaddle）、华为公司的 MindSpore，都在深度学习框架中“可圈可点”，值得关注。

读者朋友需要根据自己的应用场景来选择适合自己的计算框架，不必拘泥于本章的主要讲解对象 TensorFlow。下面我们介绍 TensorFlow 的安装。虽然 TensorFlow 适用于三大主流系统 Windows、Linux、和 macOS，但客观来讲，TensorFlow 对 Linux 和 macOS 支持较好，而对 Windows 支持相对较弱，这是因为，大部分有关深度学习的项目都运行在类 UNIX 系
