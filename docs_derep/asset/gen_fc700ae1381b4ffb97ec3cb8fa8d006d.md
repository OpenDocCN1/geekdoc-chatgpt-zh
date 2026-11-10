在 TensorFlow 里，`tf.nn.softmax` 就是完成 Softmax 计算的常用入口，具体用法示例如下：

[PRE0]

和 `Dense` 层的使用方式相近，Softmax 也可以作为独立的层来调用，`layers.Softmax(axis=-1)` 就能直接在模型中加入 Softmax 层，其中 `axis` 用来指定参与归一化的维度。

Softmax 在实际数值计算时比较脆弱，输入值偏大的时候很容易出现溢出，交叉熵的计算同样会受此困扰。为了解决数值稳定性问题，TensorFlow 提供了把 Softmax 与交叉熵损失合并实现的统一接口，内部自动处理了不稳定的情况，建议优先使用这些封装而不是把两个操作拆开。函数式接口是 `tf.keras.losses.categorical_crossentropy(y_true，y_pred，from_logits=False)`，`y_true` 是 One-hot 编码的真实标签，`y_pred` 代表网络的预测结果。`from_logits` 设为 `True` 时，`y_pred` 需要传入未经 Softmax 的原始 logit `z`；设为 `False` 时，`y_pred` 应该是已经过 Softmax 的概率输出。出于数值稳定的考虑，通常把 `from_logits` 设为 `True`，此时该函数会在内部自行完成 Softmax，不需要在模型中显式添加 Softmax 层，例如：

[PRE1]

除了函数式调用，还可以用类的方式完成同样的合并计算，即 `losses.CategoricalCrossentropy(from_logits)`，参数含义与函数式接口一致，例如：

[PRE2]

### 6.5.4 [-1,1]

若希望网络输出被限制在（-1，1）之间，最简便的做法是采用 `tanh` 激活函数，实现示例如下：

[PRE3]

输出层的搭建具有很强的灵活性，可以依据具体任务选用合适的激活，最大化现有函数的特性。

## 6.6 误差计算

模型结构搭建完毕后，下一步就是挑选合适的误差函数来度量拟合程度。常用的误差度量包括均方差、交叉熵、KL 散度、Hinge Loss 等，其中均方差和交叉熵在深度学习中出现频率最高，均方差多用于回归任务，交叉熵多用于分类任务。

### 6.6.1 均方差误差函数

均方差 `Mean Squared Error，MSE` 的思想是将预测向量和真实向量看成笛卡儿坐标系中的两个点，用两点间欧氏距离的平方来衡量差异：

[PRE4]

MSE 的取值始终是非负的，只有当预测完全等于真实标签时 MSE 才降到 0，此时网络参数达到最优。

均方差在回归场景中应用广泛，分类问题里也偶尔会使用。在 TensorFlow 中，MSE 既可以通过函数实现，也可以通过层实现。比如函数式写法如下：

[PRE5]

需要注意的是，`MSE` 函数默认返回的是每个样本的误差，需要再在样本维度上求平均才能得到整体均值，实现如下：

[PRE6]

层式的实现对应的类是 `keras.losses.MeanSquaredError()`，调用其 `_call_()` 方法即可完成前向计算，代码如下：

[PRE7]

### 6.6.2 交叉熵损失函数

在介绍交叉熵前，先回顾信息论中的熵。1948 年 Claude Shannon 把热力学熵引入信息论，用来刻画信息的不确定性，也称信息熵或香农熵。熵越大，不确定性越高，包含的信息量也越大。某个分布 `P_i` 的熵定义为：

[PRE8]

实际计算时可以使用不同底数的对数。以 4 分类为例，若真实标签为第 4 类，One-hot 编码为 `[0，0，0，1]`，则 `P(y 为 4|x)=1`，不确定性为 0，熵为：

`-0×log2 0 -0×log2 0 -0×log2 0 -1×log2 1 =0`

若模型预测概率为 `[0.1，0.1，0.1，0.7]`，其熵约为 1.356，比确定情形不确定性更大。随机分类器各类别概率均为 0.25 时，熵约为 2，不确定性更高。

由于 `P(i)∈[0，1]`，`log2 P(i) ≤0`，熵 `H(P) ≥0`，最小值 0 对应完全确定的分布，One-hot 编码即为典型例子。TensorFlow 中可用 `tf.math.log` 计算熵。

基于熵，交叉熵 `CrossEntropy` 定义为：

[PRE9]

经过推导可得 `H(p‖q) = (H_p) + D_KL(p‖q)`，其中 KL 散度定义为：

[PRE10]

KL 散度由 Solomon Kullback 与 Richard A.Leibler 于 1951 年提出，用来度量两个分布的距离。`p=q` 时 `D_KL(p‖q)` 为 0，差距越大该值越大。交叉熵和 KL 散度都不是对称的。

特别地，当分类问题中真实分布 `p` 采用 One-hot 编码时，`H_p=0`，交叉熵退化为 KL 散度。由 KL 定义可推导出分类交叉熵的计算式：

[PRE11]

其中 `i` 为 One-hot 中为 1 的索引，即真实类别。可见交叉熵只与真实类别对应的概率 `o[i]` 相关，`o[i]` 越大交叉熵越小；当 `o[i]=1` 时交叉熵达到最小值 0，预测与标签完全一致。因此最小化交叉熵等价于最大化正确类别的预测概率。

## 6.7 神经网络类型

全连接层是神经网络的基础，对后续网络的发展影响深远，其前向计算和梯度求导都较为简单，但面对高维特征时参数量会急剧膨胀，训练深层全连接网络变得困难。社交媒体带来的海量图像、视频、文本数据推动了计算机视觉和自然语言处理的研究，催生了多种变体。

### 6.7.1 卷积神经网络

识别和分析图像、视频是计算机视觉的核心。全连接层处理高维图像视频时参数过大、训练困难。Yann LeCun 在 1986 年利用局部相关性和权值共享的思想提出了卷积神经网络 `Convolutional Neural Network，CNN`。在深度学习浪潮下，CNN 在计算机视觉中的表现远超其他模型。代表性模型有用于图像分类的 `AlexNet`、`VGG`、`GoogLeNet`、`ResNet`、`DenseNet`，用于目标检测的 `RCNN`、`Fast RCNN`、`Faster RCNN`、`Mask RCNN`、`YOLO`、`SSD` 等，详见第 10 章。

### 6.7.2 循环神经网络

除空间结构数据外，序列信号也很常见，文本是典型代表。CNN 缺乏记忆机制，难以处理不定长序列。循环神经网络 `Recurrent Neural Network，RNN` 在 Yoshua Bengio、Jürgen Schmidhuber 等人研究下被证明擅长序列建模。1997 年 Jürgen Schmidhuber 提出的 `LSTM` 作为 RNN 变体，有效缓解了长期记忆和长序列问题，在 NLP 中广泛应用。基于 LSTM，Google 提出用于机器翻译的 `Seq2Seq` 模型，并应用于谷歌神经机器翻译系统 `GNMT`。其他变体还有 `GRU`、双向 RNN 等，详见第 11 章。

### 6.7.3 注意力（机制）网络

RNN 并非 NLP 的终点，注意力机制 `Attention Mechanism` 的出现克服了 RNN 训练不稳定、难并行的问题，在 NLP 和图像生成中崭露头角。注意力最初用于图像分类，随后逐步渗透到 NLP 各任务。2017 年 Google 推出首个纯注意力模型 `Transformer`，随后出现 `GPT`、`BERT`、`GPT-2` 等机器翻译相关模型。在其他领域，自注意力 `Self-Attention` 构建的网络如 `BigGAN` 也取得了不错效果。

### 6.7.4 图卷积神经网络

图像、文本等具有规则的时空结构，称为 `Euclidean Data`。CNN 和 RNN 擅长处理这类数据，但对社交网络、通信网络、蛋白质分子结构等不规则拓扑结构效果有限。2016 年 Thomas Kipf 等人在一阶近似谱卷积基础上提出图卷积网络 `Graph Convolution Network，GCN`。GCN 实现简洁，从空间一阶邻居聚合角度易于理解，在半监督任务上表现良好。随后出现 `GAT`、`EdgeConv`、`DeepGCN` 等模型。

## 6.8 汽车油耗预测实战

本节使用全连接网络预测汽车效能指标 `MPG`（`MilePer Gallon`，每加仑燃油英里数）。

### 6.8.1 数据集

选用 `Auto MPG` 数据集，记录了汽车效能指标与气缸数、重量、马力等因子的真实数据，前 5 条记录如表 6.1 所示，除产地为数字类别外，其余均为数值。产地 1 表示美国，2 表示欧洲，3 表示日本。

表 6.1 AutoMPG 数据集前 5 项

[PRE12]

数据集共 398 条记录，从 UCI 下载后读入 `DataFrame`，代码如下：

[PRE13]

原始数据可能含有缺失值，需要剔除这些记录：

[PRE14]

清洗后记录数降为 392。

`Origin` 为类别字段，去掉后转换为三个二值字段 `USA`、`Europe`、`Japan`，表示是否来自对应产地：

[PRE15]

按 8:2 比例划分为训练集和测试集：

[PRE16]

将 `MPG` 字段抽出作为标签：

[PRE17]

统计训练集各特征均值与标准差并进行标准化，通过 `norm()` 函数实现：

[PRE18]

打印训练集与测试集大小：

[PRE19]

基于训练集构建数据对象：

[PRE20]

通过统计各特征两两分布可观察对 MPG 的影响，如图 6.16 所示。排量、重量增大时 MPG 降低，能耗增加；气缸数越小，最优 MPG 越高，更省油，这与生活经验一致。

[PRE21]

图 6.16 特征之间的两两分布

### 6.8.2 创建网络

`Auto MPG` 规模较小，构建一个 3 层全连接网络。输入特征 9 个，第一层输入节点 9 个，第一、第二隐藏层输出节点分别为 64、64，输出层 1 个。`MPG∈R^+`，输出层可不加激活或加 `ReLU`。

网络实现为自定义类，继承 `keras.Model`，在 `__init__` 中创建子层，在 `call` 中定义计算逻辑，便于使用 `trainable_variables`、`save_weights` 等功能。网络类代码如下：

[PRE22]

### 6.8.3 训练与测试

实例化网络对象并创建优化器：

[PRE23]

训练采用 Epoch 与 Step 的双层循环，共 200 个 Epoch：

[PRE24]

回归任务除 MSE 外，还可用平均绝对误差 `Mean Absolute Error，MAE` 评估，定义为：

[PRE25]

程序记录每个 Epoch 的训练和测试 MAE 并绘制曲线，如图 6.17 所示。

[PRE26]

图 6.17 MAE 变化曲线

可观察到约第 25 个 Epoch 后 MAE 下降放缓，训练集 MAE 仍缓慢下降而测试集 MAE 基本不变，因此可在约第 25 个 Epoch 提前停止，并用此时参数预测新样本。

## 参考文献

`[1]` 尼克.人工智能简史[M]．北京：人民邮电出版社，2017.

`[2]` Glorot X,Bordes A,Bengio Y. Deep Sparse Rectifier Neural Networks [C]//Proceedings of the Fourteenth International Conference on Artificial Intelligence and Statistics, Fort Lauderdale, FL, USA, 2011.

`[3]` Mizera-Pietraszko J,Pichappan P. Lecture Notes in Real-Time Intelligent Systems [M]. Springer International Publishing, 2017.

* * *

[(1)] 图片来自 https://www.theverge.com/2019/3/27/18280665/ai-godfathers-turing-award-2018-yoshua-bengio-geoffrey-hinton-yann-lecun。

# 第 7 章 反向传播算法

回首得越久，你会看得越远。——温斯顿·丘吉尔

第 6 章已经系统地介绍完基础的神经网络算法：从输入和输出的表示开始，介绍感知机模型，多输入、多输出的全连接层，然后扩展至多层神经网络；介绍了针对不同的问题场景下输出层的设计，最后介绍常用的损失函数及其实现方法。