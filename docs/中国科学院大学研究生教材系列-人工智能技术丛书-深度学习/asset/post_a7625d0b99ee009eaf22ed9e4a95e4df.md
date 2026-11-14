
[18] BOCHKOVSKIY A, WANG C, LIAO H M. YOLOv4: optimal speed and accuracy of object detection [J]. arXiv, abs/2004.10934, 2020.

[19] JOCHER GLENN. YOLOv5 [EB/OL]. https://github.com/ultralytics/yolov5, 2020.

[20] LI C, LI L, JIANG H, et al. YOLOv6: a single-stage object detection framework for industrial applications [J]. arXiv, abs/2209.02976, 2022.

[21] LIU W, ANGUELOV D, ERHAN D, et al. SSD: single shot multiBox detector [C]. Proceedings of the 14th European Conference on Computer Vision, 2016: 21-37.

[22] LONG J, SHELHAMER E, DARRELL T. Fully convolutional networks for semantic segmentation [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2015: 3431-3440.

[23] BADRINARAYANAN V, KENDALL A, CIPOLLA R. SegNet: a deep convolutional encoder-decoder architecture for image segmentation [J]. IEEE transactions on pattern analysis and machine intelligence, 2017, 39 (12): 2481-2495.

[24] RONNEBERGER O, FISCHER P, BROX T. UNet: convolutional networks for biomedical image segmentation [C]. Proceedings of International Conference on Medical Image Computing and Computer-Assisted Intervention, 2015: 234-241.

[25] NEWELL A, YANG K, DENG J. Stacked hourglass networks for human pose estimation [C]. Proceedings of the 14th European Conference on Computer Vision, 2016: 483-499.

[26] TAIGMAN Y, YANG M, RANZATO M, et al. DeepFace: closing the gap to human-level performance in face verification [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2014: 1701-1708.

[27] SUN Y, WANG X, TANG X. Deep learning face representation from predicting 10 000 classes [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2014: 1891-1898.

[28] SCHROFF F, KALENICHENKO D, PHILBIN J. FaceNet: a unified embedding for face recognition and clustering [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2015: 815-823.

[29] 山下隆义. 图解深度学习 [M]. 张弥, 译. 北京: 人民邮电出版社, 2018.

[30] UIJLINGS J, SANDE K, GEVERS T, et al. Selective search for object recognition [J]. International journal of computer vision, 2013, 104: 154-171.

[31] HE K, ZHANG X, REN S, et al. Spatial pyramid pooling in deep convolutional networks for visual recognition [J]. IEEE transactions on pattern analysis and machine intelligence, 2015, 37 (9): 1904-1916.

[32] WENG K, CHU X, XU X, et al. EfficientRep: an efficient repvgg-style convNets with hardware-aware neural network design [J]. arXiv, abs/2302.00386, 2023.

[33] TOSHEV A, SZEGEDY C. DeepPose: human pose estimation via deep neural networks [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2014: 1653-1660.

[34] SUN Y, CHEN Y, WANG X, et al. Deep learning face representation by joint identification-verification [C]. Advances in Neural Information Processing Systems 27, 2014: 1988-1996.

## 本章人物： Yann LeCun 教授

Yann LeCun（1960~），纽约大学教授，美国国家科学院院士，美国国家工程院院士，法国科学院院士，2018 年图灵奖获得者。 Yann LeCun 教授是纽约大学数据科学中心创始人，并担任 Facebook 人工智能首席科学家。他在卷积神经网络、改进反向传播算法、计算神经学和扩展神经网络应用等领域做出杰出贡献，被誉为“卷积神经网络之父”。

![](img/57b180d92f84b0150a5b5e4fc7583b76_92_0.png)

Yann LeCun 教授于 1983 年和 1986 年分别获 ESIEE Paris 的学士学位和巴黎第六大学（Université Paris VI）的计算机科学博士学位，毕业后在 Geoffrey Hinton 教授的实验室做了一年的博士后，之后加入贝尔实验室，2003 年加入纽约大学。

Yann LeCun 教授的主要贡献集中在卷积神经网络领域，1998 年他提出的 LeNet-5 是卷积神经网络的基础模型，当时就在一些银行和公司开展了应用，提升了手写数字和手写字母的识别能力。之后出现的典型卷积神经网络，如 AlexNet、VGGNet、GoogleNet 和 ResNet 等都以 LeNet-5 为基础，从网络深度和宽度上进行了扩展。卷积神经网络的发明使得其在计算机视觉领域各项任务上取得了突破性的成绩。此外，Yann LeCun 教授在改进反向传播算法方面也做出较大贡献。

2013 年，Yann LeCun 与 Yoshua Bengio 共同创办了表示学习领域的国际会议 ICLR（International Conference on Learning Representation），至今已举办 11 届，目前该会议已成为人工智能领域的顶级会议之一。

2021 年 8 月 1 日，Yann LeCun 教授出版图书《科学之路：人，机器与未来》，介绍了自己的学术生涯和研究成果，并对人工智能的未来做出了自己的思考。

Yann LeCun 教授也多次来中国进行交流，他还有个中文名字“杨立昆”。

Yann LeCun 教授的个人主页：http://yann.lecun.com/。

## 循环神经网络

卷积神经网络在计算机视觉领域取得了巨大的成功，但是在序列数据建模领域，卷积神经网络并不能提供有效的支持。常见的序列数据包括由字母和词汇序列组成的文本、由音节序列组成的语音、由图像帧序列组成的视频，此外还有气象观测数据、股票数据、房价数据等时态数据。为了处理这些序列数据，出现了循环神经网络（Recurrent Neural Network，RNN），后来又出现了一些循环神经网络的变种，很好地支持了序列数据处理。本章将介绍循环神经网络的起源与发展、循环神经网络的典型结构以及训练方式、长短期记忆网络、循环神经网络的其他变种以及循环神经网络的典型应用。

### 3.1 循环神经网络的起源与发展

循环神经网络的起源与发展可以分为三个阶段：早期（1980 ~ 1990 年）、中期（1990 ~ 2010 年）和当前（2010 年至今），如图 3-1 所示。

![](img/57b180d92f84b0150a5b5e4fc7583b76_93_0.png)

循环神经网络起源于著名物理学家 John Joseph Hopfield 于 1982 年提出的 Hopfield 神经网络[1]。Hopfield 神经网络具有递归特性，每一个神经元的输入来自其他所有神经元，它的输出又传递给其他所有的神经元，但自身没有反馈连接。

1986 年，Michael I. Jordan 提出了 Jordan Network[2]，它是第一个真正意义上的循环神经网络，实现了序列数据的递归，从而开启了循环神经网络的研究与发展。

1990 年，Jeffrey L. Elman 提出了 Elman Network[3]，他对 Jordan Network 进行了改进，简化了它的结构，成为当今循环神经网络的主流形式。

同样是 1990 年，Paul Werbos 提出了循环神经网络的训练算法——基于时间的反向传播算法（Backward Propagation Through Time，BPTT）[4]，它在人工神经网络的反向传播算法的基础上实现了循环神经网络的有效训练。

1997 年，Jurgen Schmidhuber 和 Sepp Hochreiter 提出了长短期记忆网络（Long-Short Term Memory，LSTM）[5]，虽然 LSTM 在当时并没有引起工业界的关注，但是在 2010 年以后，随着深度学习的发展，LSTM 得到了大规模应用。

同样在 1997 年，Mike Schuster 与 Kuldip K. Paliwal 提出了双向循环神经网络（Bi-directional RNN，BRNN）[6]，他们认为，当前的输出不仅与之前的序列有关，还与之后的序列有关。BRNN 由两个循环神经网络上下叠加在一起组成，输出由这两个循环神经网络的隐藏层的状态决定。

2014 年，Junyoung Chung 等人提出了门限循环单元（Gated Recurrent Unit，GRU）[7]，它简化了 LSTM 的结构，在多个任务上达到了与 LSTM 相媲美的结果。

同样在 2014 年，Alex Graves 等人提出了神经图灵机（Neural Turing Machine，NTM）[8]，神经图灵机引入了外部存储，增大了记忆容量。

2015 年，Armand Joulin 与 Tomas Mikolov 提出了堆叠循环神经网络（Stacked Recurrent Neural Network，Stacked RNN）[9]，它将多个循环神经网络堆叠成多层循环神经网络，上一层循环神经网络的输入为下一层循环神经网络的输出，Stack RNN 在一些自然语言处理任务（如机器翻译）上有很好的表现。

### 3.2 循环神经网络的训练

循环神经网络是一种人工神经网络，神经元之间的连接形成一个遵循时间序列的有向图。输入样本之间存在顺序关系，即每个样本和它之前的样本存在关联，通过神经元在时序上的展开，就可以找到样本之间的这些序列相关性。

RNN 的一般结构如图 3-2 所示。

如图 3-2 所示，RNN 的计算公式如下：

\( h_t = \sigma(Ux_t + Wh_{t-1} + b_h) \quad (3-1) \)

\( \hat{y}_t = \varphi(Vh_t + b_y) \quad (3-2) \)

其中：

![](img/57b180d92f84b0150a5b5e4fc7583b76_95_0.png)

图 3-2 RNN 的一般结构

- 1) \( x_t \) 是 \( t \) 时刻的输入向量，\( x_t \in \mathbb{R}^m \)。
- 2) \( h_t \) 是 \( t \) 时刻的记忆或者隐态向量，\( h_t \in \mathbb{R}^n \)。
- 3) \( \hat{y}_t \) 是 \( t \) 时刻的输出向量。
- 4) \( U \)、\( V \)、\( W \) 是连接权重矩阵。
- 5) \( b_h \)、\( b_y \) 是偏置向量。
- 6) \( \sigma \)、\( \varphi \) 是激活函数，\( \sigma \) 通常选用 Tanh 或 Sigmoid 激活函数，\( \varphi \) 通常选用 Softmax。

RNN 的训练一般采用基于时间的反向传播算法（Back Propagation Through Time，BPTT）[3]，它在反向传播算法的基础上加上了时间演化。首先回顾一下反向传播算法的主要思想：定义误差函数 \( E \)，通过链式法则自顶向下求得 \( E \) 对网络权重与偏置的偏导，之后沿梯度的反方向更新权重与偏置的值，直到 \( E \) 收敛。下面以图 3-3 中的 RNN 为例，基于文献 [10]，详细讲解 BPTT 算法的流程。

![](img/57b180d92f84b0150a5b5e4fc7583b76_95_1.png)

图 3-3 一个典型的 RNN 示例（\( E_0 \sim E_4 \) 是每个时刻的误差函数，\( E \) 为总误差函数）

首先，对公式（3-1）和公式（3-2）中的 RNN 定义进行简化，不考虑偏置，得到：

\( h_t = \tanh(Ux_t + Wh_{t-1}) \quad (3-3) \)

\( \hat{y}_t = \text{softmax}(Vh_t) \quad (3-4) \)

RNN 的损失函数选用交叉熵损失，如下所示：

\( E_i(y_i, \hat{y}_i) = -y_i \log \hat{y}_i, \quad (3-5) \)

\( E(y, \hat{y}) = \sum_i E_i(y_i, \hat{y}_i) = - \sum_i y_i \log \hat{y}_i, \quad (3-6) \)

根据反向传播算法，需要求 E 对 U、V、W 的梯度：

\( \frac{\partial E}{\partial V} = \sum_i \frac{\partial E_i}{\partial V}, \quad (3-7) \)

\( \frac{\partial E}{\partial W} = \sum_i \frac{\partial E_i}{\partial W}, \quad (3-8) \)

\( \frac{\partial E}{\partial U} = \sum_i \frac{\partial E_i}{\partial U}, \quad (3-9) \)

如图 3-4 所示，求 E 对 V 的梯度，需要分别求各个时刻的误差 E_i 对 V 的梯度再求和，以求 E_3 对 V 的梯度为例：

\( \frac{\partial E_3}{\partial V} = \frac{\partial E_3}{\partial \hat{y}_3} \frac{\partial \hat{y}_3}{\partial V} = \frac{\partial E_3}{\partial \hat{y}_3} \frac{\partial \hat{y}_3}{\partial z_3} \frac{\partial z_3}{\partial V}, \quad (3-10) \)

其中，\( z_3 = V h_3 \)，同样求得其他时刻误差对 V 的梯度，求和可得 \( \frac{\partial E}{\partial V} \)。

![](img/57b180d92f84b0150a5b5e4fc7583b76_96_0.png)

图 3-4 求 E 对 V 的梯度示意图

如图 3-5 所示，求 E 对 W 的梯度，也是以求 E_3 对 W 的梯度为例：

\( \frac{\partial E_3}{\partial W} = \frac{\partial E_3}{\partial \hat{y}_3} \frac{\partial \hat{y}_3}{\partial h_3} \frac{\partial h_3}{\partial W}, \quad (3-11) \)

![](img/57b180d92f84b0150a5b5e4fc7583b76_96_1.png)

图 3-5 求 E 对 W 的梯度示意图

\( h_3 = \tanh(Ux_3+Wh_2) \quad (3-12) \)

\( \frac{\partial E_3}{\partial W} = \sum_{k=0}^3 \frac{\partial E_3}{\partial \hat{y}_3} \frac{\partial \hat{y}_3}{\partial h_3} \frac{\partial h_3}{\partial \theta_k} \frac{\partial \theta_k}{\partial W} = \sum_{k=0}^3 \frac{\partial E_3}{\partial \hat{y}_3} \frac{\partial \hat{y}_3}{\partial h_3} \left( \prod_{j=k+1}^3 \frac{\partial h_j}{\partial h_{j-1}} \right) \frac{\partial h_k}{\partial W} \quad (3-13) \)

需要说明的是：\( h_3 \) 依赖于 \( h_2 \)，而 \( h_2 \) 依赖于 \( h_1 \) 和 \( W \)，依赖关系一直传递到 \( t=0 \) 的时刻。
因此，当计算对 \( W \) 的偏导时，不能把 \( h_2 \) 看作是常数项。

同样求得其他时刻误差对 \( W \) 的梯度，求和可得 \(\frac{\partial E}{\partial W}\)。

如图 3-6 所示，求 \( E \) 对 \( U \) 的梯度，同样以求 \( E_3 \) 对 \( U \) 的梯度为例：

\( \frac{\partial E_3}{\partial U} = \frac{\partial E_3}{\partial \hat{y}_3} \frac{\partial \hat{y}_3}{\partial h_3} \frac{\partial h_3}{\partial U} \quad (3-14) \)

\( h_3 = \tanh(Ux_3+Wh_2) \quad (3-15) \)

\( \frac{\partial E_3}{\partial U} = \sum_{k=0}^3 \frac{\partial E_3}{\partial \hat{y}_3} \frac{\partial \hat{y}_3}{\partial h_3} \frac{\partial h_3}{\partial \theta_k} \frac{\partial \theta_k}{\partial U} = \sum_{k=0}^3 \frac{\partial E_3}{\partial \hat{y}_3} \frac{\partial \hat{y}_3}{\partial h_3} \left( \prod_{j=k+1}^3 \frac{\partial h_j}{\partial h_{j-1}} \right) \frac{\partial h_k}{\partial U} \quad (3-16) \)

同样，需要说明的是：\( h_3 \) 依赖于 \( h_2 \)，而 \( h_2 \) 依赖于 \( h_1 \) 和 \( U \)，依赖关系一直传递到 \( t=0 \) 的
时刻。因此，当计算对 \( U \) 的偏导时，也不能把 \( h_2 \) 看作是常数项。

同样求得其他时刻误差对 \( U \) 的梯度，求和可得 \(\frac{\partial E}{\partial U}\)。

![](img/57b180d92f84b0150a5b5e4fc7583b76_97_0.png)

### 3.3 长短期记忆网络

循环神经网络的前向传播过程，会使得与当前时刻间隔较长的时间步对当前的影响削弱
甚至消失，其根本原因在于 BPTT 算法与 BP 算法一样，在进行梯度反向传播的时候，会产生
梯度不断减小甚至消失的问题。

为了解决循环神经网络的梯度消失问题，人们提出了多种方法，包括引入时间维度的跳
跃连接、门控 RNN、梯度截断、引导信息流的正则化和外部记忆等方法。其中，门控 RNN 是
一类比较特殊的 RNN，最有代表性的是 LSTM[5]。本节我们将详细介绍 LSTM 的网络结构。

LSTM 单元（Unit）一般由一个细胞（Cell）、一个输入门（Input Gate）、一个输出门
（Output Gate）和一个遗忘门（Forget Gate）组成。细胞能够记住任意时间间隔上的细胞状态，
