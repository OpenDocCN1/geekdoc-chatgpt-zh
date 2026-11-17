
## 8 胶囊人工神经网络

![](converter-master/example/165201/content_633686_17.jpg)

图4-55

胶囊人工神经网络（CapsNet）也是目前十分火热的人工神经网络之一，它是由Hinton于2017年10月提出的，Hinton称其为Capsule，即胶囊。胶囊人工神经网络和CNN一样，最初是用来处理视觉问题的。之所以提出胶囊人工神经网络，是因为Hinton发现CNN在进行图像识别时存在一些难以解决的问题。CNN通过卷积运算得出图像各个构成要素的特征信息，但CNN对各个要素的关联关系的判断能力较弱。例如，一个人有眼、鼻子、嘴巴，如果有这些要素则CNN判断为人脸，但如果鼻子长在额头，眼睛长在嘴巴旁边，CNN仍然可能会判断为人脸。但事实上，一张脸上不应该只有两只眼睛、一张嘴巴和一个鼻子，要素之间的关联关系对识别图像来说也很重要。另外，当图像左右平移或发生旋转时，CNN也难以处理。图4-56所示为几个英文字母A，人们很容易辨别出这几个字母都是A，但CNN会认为它们是不同的字母。

![](converter-master/example/165201/content_633686_18.jpg)

图4-56

Hinton认为人与CNN的最大区别是，人在识别图像时是按照树状结构抽象数据的，而CNN是按照层级结构抽象数据的。CNN追求的是不变与稳定，而胶囊神经网络追求的是变化与不同。胶囊人工神经网络可以在不同视角下对同一个图像形成信息抽象，而不是简单地从固定视角对图像进行信息抽象。

正是因为胶囊人工神经网络可以对图像进行翻转、平移、特征组合，所以大大减少了数据标注的工作，由胶囊人工神经网络自己进行学习和抽象。

与CNN所拥有的深层网络一样，胶囊人工神经网络也有层次结构，如图4-57所示。不同层级的胶囊处理不同的信息，初层级的胶囊用来训练图像中细节特征，高层级的胶囊用来训练图像中细节特征之间的相互关系，并综合每个胶囊给出的位置强度，最终重新构成图像。

![](converter-master/example/165201/content_633686_19.jpg)

图4-57

胶囊人工神经网络是近年来影响力巨大的一种颠覆性的网络架构，因为胶囊人工神经网络将输入标量变成了一组向量。目前，胶囊人工神经网络已经达到对MNIST数据集的最佳精度。由于胶囊人工神经网络的胶囊可以实现特征信息的位置、旋转的关联，因此胶囊人工神经网络通过较少的训练数据即可超过传统CNN在大数据量下的训练结果。

## 4.6 机器学习框架

随着人工智能技术的发展，人工智能产品的研发过程越发离不开各种开发库和开发框架。市场上有各种机器学习框架，如TensorFlow、Caffe、CNTK、PyTorch、Theano等，这些机器学习框架各有千秋，各有特色。2017年5月，有人总结了GitHub中最受欢迎的开源机器学习框架排名，如图4-58所示，TensorFlow受欢迎投票排名第一，Caffe和CNTK紧随其后。

![](converter-master/example/165201/content_633687_0.jpg)

图4-58

2018年年初，谷歌研究科学家Chollet通过谷歌搜索索引，展示了新的机器学习框架排行，TensorFlow被索引量排名第一，Keras被索引量排名第二，之后依次是Caffe、PyTorch、Theano，MXNet、Chainer、CNTK，如图4-59所示。

![](converter-master/example/165201/content_633687_1.jpg)

图4-59

2018年3月，特斯拉人工智能部门的Karpathy，综合研究过去6年发表在ArXiv的4 300篇机器学习论文，得出了更加精确的结果。Karpathy根据各框架被提的次数得到的总排名如下：TensorFlow 5.9%，Caffe 5.4%，Theano 3.2%，Keras 2.3%，Torch 1.6％，PyTorch 1%，其他0.5%。

从各个时间段和不同专家的排行可以了解，近几年来最为火热的机器学习框架仍为TensorFlow，其次是Caffe、Keras、Theano、PyTorch等。主流的机器学习框架特点如图4-60所示。

![](converter-master/example/165201/content_633687_2.jpg)

图4-60

### 4.6.1 TensorFlow

TensorFlow是谷歌基于DistBelief研发的机器学习框架。TensorFlow中Tensor表示张量，Flow表示流，TensorFlow可以理解为张量从数据流图中的一端流动到另一端的过程。TensorFlow的架构十分灵活，用户可以轻松地将其部署到多种平台（CPU、GPU、TPU）和设备上，甚至可以在智能手机中运行。

TensorFlow的优点在于其支持Python语言，用户易于上手。并且TensorFlow使用了数据流图表进行抽象，提供了TensorBoard进行可视化展现。目前使用TensorFlow进行开发的公司很多，如AMD、NVIDIA、Uber、SAP、DeepMind、小米、京东、联想、开普云等。

TensorFlow完全开源，任何人都可以下载使用，而且TensorFlow使用起来也很方便，通过简单的几行代码就可以运行。正是因为TensorFlow的操作简单，使人工神经网络的入门也变得简单起来。TensorFlow的安装也非常简单，如果在Windows环境下安装CPU版本，在命令行模式下输入`pip install tensorflow`即可；如若安装GPU版本，命令行模式下输入`pip install tensorflow-gpu`即可。安装TensorFlow后可以安装Jupyter Notebook编辑器，以便创建程序文档，安装时在命令行输入`pip install jupyter`即可。

除通过命令行手动安装外，也可以通过Anaconda等工具进行安装。如果我们把TensorFlow比作做饭的炉灶，则Anaconda可以理解为一个厨房，里面除炉灶外还有各种食材供人选择，如图4-61所示。安装Anaconda非常简单，可以直接从官网进行下载，然后单击“安装”按钮，并按照引导一步步确定即可。安装完毕后，可以在Environments里看到很多工具包，在搜索条中搜索“TensorFlow”并勾选其工具包，单击“Apply”按钮即可安装。

![](converter-master/example/165201/content_633687_3.jpg)

图4-61

当然，TensorFlow也存在一定的缺陷，TensorFlow的每个计算流都必须构造成一个静态图，且缺乏符号性循环，这会带来一些计算困难。而且TensorFlow的性能与其他竞争对手相比也并无明显优势，但这些都不妨碍TensorFlow成为目前火热的机器学习框架。

### 4.6.2 Caffe

Caffe（Convolutional Architecture for Fast Feature Embedding）是自2013年年底以来，第一款主流的工业级机器学习框架。Caffe是一个高效的机器学习框架并且开源，具备非常好的CNN建模能力。但是Caffe中的RNN资源相对较少，所以初期Caffe并未定位于通用框架，而是专注于图像识别、推荐引擎和自然语言识别等方向的应用，不面向其他深度学习应用，诸如语音识别、时间序列预测、图像字幕和文本等需要处理顺序信息的任务。但优秀的卷积模型使Caffe成为计算机视觉界十分流行的工具包之一，并在2014年的ImageNet挑战赛中一举夺魁。Caffe在图像处理方面的优异性能也使其完美地应用于实验研究和商业部署。

在2017年4月的F8年度开发者大会上，Facebook发布了一款全新的机器学习框架——Caffe2，如图4-62所示。这是一个轻量化和模块化的机器学习框架，在强调轻便性的同时也保持了Caffe的可扩展性和计算性能。

![](converter-master/example/165201/content_633687_4.jpg)

图4-62

Caffe2可以通过一台机器上的多个GPU（或具有一个或多个GPU的多台机器）来进行分布式训练。Caffe2也可以在iOS系统、安卓系统和树莓派上训练及部署模型，并且Caffe2只需要运行几行代码即可调用预先训练好的模型。目前，NVIDIA、高通、英特尔、亚马逊和微软等公司的云平台都已支持Caffe2。

### 4.6.3 PyTorch

PyTorch是基于Python的科学计算包，可为用户提供最大的灵活性和速度。例如，一直以来Facebook的机器学习和科学计算都是建立在PyTorch之上的，很多人选择PyTorch作为开发工具，是因为其不仅支持Python，还支持动态图的创建。市面上大多数框架，如TensorFlow、Theano、Caffe普遍仅采用静态图，即建立一个神经网络，并重复使用相同的结构。静态图定义的缺陷是在处理数据前必须定义好完整的一套能够处理所有边际情况的模型，而如果使用动态图则可以非常自由地定义模型、使用模型和修正模型。

### 4.6.4 CNTK

Microsoft的CNTK是一个非常强大的机器学习框架，可以方便快捷地创建人工神经网络。CNTK最初是为了在Microsoft内部使用而开发的，随着人工智能的发展，CNTK也逐渐升级迭代，目前已经是市面上较为主流的学习框架之一。

CNTK最大的优点是速度快，并且CNTK支持CPU和GPU计算，另外，CNTK的使用和训练都非常方便，这使初学者非常容易理解。CNTK可以有效地胜任诸如分类、语言识别、图像识别等任务。

CNTK下载完毕后解压，运行`Install.bat`安装即可，程序会自动帮用户安装整个环境中所需要的相关类库和软件包。安装完成后运行`cntkpy35.bat`激活CNTK环境，并运行以下代码运行框架。

![](converter-master/example/165201/content_633687_5.jpg)

读者可以在电脑上安装CNTK。如果电脑上没安装CNTK的运行环境工具包，笔者建议读者选择预选安装CNTK运行在Azure Notebook的托管环境。
