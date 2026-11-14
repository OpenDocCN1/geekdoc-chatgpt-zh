
![](img/57b180d92f84b0150a5b5e4fc7583b76_84_0.png)

图 2-53 DeepPose 的结构 [34]

DeepPose 还使用了级联回归器（Cascaded Regressor）对预测结果进行细化。模型基于前一阶段预测的坐标位置对图像进行局部裁剪作为现阶段的输入，这使得现阶段的输入具有更高的分辨率，从而能学习到更为精细的特征，以此来对前一阶段的预测结果进行细化，如图 2-54 所示。图 2-54 中绿色的点与连线表示标定的关键点及连线，红色的点与连线表示每一阶段的预测输出。

### 初始阶段

### 级联回归: 第2阶段

### 级联回归: 第3阶段

![](img/57b180d92f84b0150a5b5e4fc7583b76_84_1.png)

图 2-54 DeepPose 中的级联输出 [33] (详见彩插)

DeepPose 直接对 k 个关键点的坐标进行回归，训练复杂度较高，一定程度上削弱了模型的泛化能力，而且模型使用的骨干网络是 AlexNet，它的学习能力有限。一种解决方法是将回归问题转换为预测 k 个尺寸为 W0 × H0 的热图（Heatmap）| h1, h2, ..., hk |，其中 hi 表示第 i 个关键点的热图。根据此方法，基于沙漏网络（Hourglass Network）的姿态估计算法被提出[25]。堆叠的沙漏网络（Stacked Hourglass）的提出具有里程碑意义，它不仅在各个姿态估计数据集上取得了当时最好的结果，后续也被应用于目标检测、图像分割等领域，图 2-55 中重复的形似沙漏的结构就是沙漏网络。

沙漏网络包含重复的降采样（高分辨率到低分辨率）和上采样（低分辨率到高分辨率），此外还使用了残差连接保存不同分辨率下的空间信息。与 DeepPose 采用级联回归器一样，使用 Hourglass 模块的初衷在于捕捉与利用多个尺度上的信息，因为局部特征信息对于识别人体的脸部、手部等部位十分重要，但人体最终的姿态估计也需要图像的全局特征信息。与 DeepPose 的不同之处在于，沙漏网络不直接对关键点坐标进行回归，而是输出热图用于预测在每个像素点存在关键点的概率。

![](img/57b180d92f84b0150a5b5e4fc7583b76_85_0.png)

图 2-55 沙漏网络

沙漏网络模型使用均方误差损失评价预测的热图与标定的热图（以实际关节点位置为中心的标准差为 1 的二维高斯分布）的误差。

图 2-55 根据文献 [25] 重新绘制。

## 2.5.4 人脸识别

人脸识别是指利用计算机分析人脸图像，并从中提取出有效的特征，最终判别人脸对象的身份。人脸识别已经成为计算机视觉领域的最重要的应用之一，目前在人们生产和生活的很多方面开展了应用，包括乘坐高铁/飞机时的身份验证、银行账户验证、手机支付验证等。

人脸识别一般分为两类：1）人脸验证/人脸比对（Face Verification）。指人脸 1 对 1 的比对，即判断两张人脸图片里的人是否为同一个人，如手机人脸解锁使用的就是这一类。2）人脸识别（Face Identification）。指人脸的 1 对 N 比对，即判断系统当前见到的人，为数据库中众多人中的哪一个，如疑犯识别、会场签到和客户识别等应用使用的就是这一类。

传统的人脸识别方法一般是先设计特征提取器，提取多个特征，再利用机器学习算法对提取的这些特征组合进行分类，如一共提取 6 个特征，每个特征有 2 个取值，那么就有 64 种组合，也就是说可以识别 64 张不同的人脸。传统的人脸识别方法需要人工设计要提取的特征，工作量较大，随着深度学习的诞生与发展，人们开始使用卷积神经网络进行人脸识别，识别准确率得到了很大提升。

2014 年，基于卷积神经网络的人脸识别模型 DeepFace[26] 被提出，它是卷积神经网络在人脸识别领域的奠基之作。人脸识别通常包含 4 个步骤：检测、对齐（校正）、表示和分类。DeepFace 在人脸对齐和表示这两个步骤上提出了新的方法：1）人脸对齐方面主要使用了 3D 人脸模型来对齐人脸；2）人脸表示方面使用了一个 9 层的卷积神经网络，同时使用了局部卷积。DeepFace 的性能远远超过了传统人脸识别算法，在 LFW 数据集上达到了 97.35% 的人脸验证精度。

在人脸对齐方面，DeepFace 使用的方法与流程如下。

- 1) 用局部二值模式（Local Binary Pattern，LBP）结合支持向量回归（Support Vector Regression，SVR）的方法检测出人脸的 6 个基准点，包含眼睛两个点、鼻子一个点、嘴巴三个点。
- 2) 通过拟合一个对基准点的转换（缩放、旋转与平移）对图像进行裁剪。
- 3) 对图像定位 67 个基准点，并进行三角剖分。
- 4) 用一个 3D 人脸库 USF Human-ID 得到一个平均 3D 人脸模型（正脸）。
- 5) 学习一个 3D 人脸模型和原 2D 人脸之间的映射 P，并可视化为三角块。
- 6) 通过相关的映射，把原 2D 人脸中的基准点转换成 3D 模型产生的基准点。
- 7) 得到最后的正脸。

DeepFace 中用于人脸表示的卷积神经网络架构如图 2-56 所示。

![](img/57b180d92f84b0150a5b5e4fc7583b76_86_0.png)

图 2-56 DeepFace 中用于人脸表示的卷积神经网络

在用于人脸表示的卷积神经网络中，各层的功能说明如下：

- 1) C1、C3 是卷积层，M2 是最大池化层，前三层主要提取低水平特征，其中最大池化层可以使输出对微小的偏移更加鲁棒。
- 2) L4、L5、L6 是局部卷积层，对于特征图上的每个区域，学习不同的卷积核（即一张特征图上的卷积核参数不共享），因为人脸的不同区域会有不同的统计特征，比如眼睛和眉毛之间的区域比鼻子和嘴巴之间的区域具有更高的区分能力。
- 3) F7 是全连接层，用来捕捉不同区域特征的相关性，比如眼睛的位置与形状、鼻子的位置与形状、嘴巴的位置与形状。
- 4) F8 层对 F7 层的输出特征进行归一化（除以训练集上所有样本中的最大值），得到的特征向量值位于 0 到 1 之间，使用 Softmax 进行分类，输出是 4030 维的分类结果。

2014 年，DeepID[27] 被提出，主要针对的是人脸验证任务。DeepID 模型在训练集中对 10000 个人脸实体进行分类，再将学习到的特征用于人脸验证。模型使用人脸上不同的区域训练多个单独的卷积神经网络，每个卷积神经网络的最后一层的输出为提取到的特征，称为 DeepID 特征（Deep Hidden IDentity Feature）。DeepID 中的卷积神经网络结构由四个卷积层和一个 Softmax 输出层组成，如图 2-57 所示。

![](img/57b180d92f84b0150a5b5e4fc7583b76_87_0.png)

图 2-57 DeepID 中的卷积神经网络

在 DeepID 中的卷积神经网络中，输入的不是整个人脸，而是人脸中的某个区域（Patch）。输入的区域有两种：一种是 39×31×k，对应矩形区域；一种是 31×31×k，对应正方形区域，其中 k 当输入是 RGB 图像时为 3，灰度图时为 1。将不同区域提取到的 DeepID 特征连接起来作为人脸的特征，用主成分分析方法降维到 150 维后送入 Joint Bayesian 分类器（也可以采用其他分类器）进行人脸验证，此时变为二分类任务。模型在 LFW 数据集上取得了 97.45% 的准确率。

DeepID 的研究团队同年又提出了 DeepID2[34]，与 DeepID 相比，DeepID2 中的卷积神经网络的输入尺寸更大，输入图像大小从 39×31 增大到 55×47，如图 2-58 所示。DeepID2 的最大改进在于同时使用识别信号和验证信号来监督学习过程，而在 DeepID 中只用到了识别信号。DeepID2 在 LFW 数据集上的准确率达到了 99.15%。识别信号使用的是交叉熵损失，验证信号使用的是 L2 损失。可以看出，验证信号的计算需要两个样本，故每次迭代时需要随机抽取两个样本，然后计算误差。DeepID2 的损失函数如下所示：

```
Ident(f, t, \theta_{id}) = - \sum_{i=1}^n p_i \log \hat{p}_i = -\log \hat{p}_i
```

![](img/57b180d92f84b0150a5b5e4fc7583b76_87_1.png)

图 2-58 DeepID2 的卷积神经网络结构

```
\text{Verif}(f_i, f_j, y_{ij}, \theta_n) = \begin{cases} \frac{1}{2} \| f_i - f_j \|_2^2, & y_{ij} = 1 \\ \frac{1}{2} \max(0, m - \| f_i - f_j \|_2)^2, & y_{ij} = -1 \end{cases}
```

其中 \( f \) 是从人脸图像提取的特征向量，\( t \) 是目标类别，\( \theta_{id} \) 是 Softmax 层的参数，\( p_i \) 是目标类的概率分布，目标类 \( t \) 的 \( p_t = 1 \)，其他类的 \( p_i = 0 \)；\( f_i \)、\( f_j \) 是从两个对比人脸图像提取的特征向量，\( y_{ij} = 1 \) 指的是 \( f_i \)、\( f_j \) 来自同一个人脸 ID，\( y_{ij} = -1 \) 指的是 \( f_i \)、\( f_j \) 来自不同的人脸 ID，\( \theta_n = |m| \) 是可学习的验证损失函数的参数。

2015 年，FaceNet[28] 被提出，该模型通过将人脸图像映射到欧氏空间上的点进行人脸识别，点之间的空间距离表示人脸的相似度。同一个人的脸图像，映射后空间距离比较小，不同人的人脸图像，映射后空间距离比较大，这样通过人脸图像的空间映射就可以实现人脸识别。基于相同人脸的距离总是小于不同人脸的距离这一先验知识训练网络，进而可以直接对比两个人脸的欧氏距离，判断是否为同一人。FaceNet 在 LFW 数据集上达到了 99.63% 的准确率。FaceNet 的网络结构如图 2-59 所示。

![](img/57b180d92f84b0150a5b5e4fc7583b76_88_0.png)

图 2-59 FaceNet 的网络结构

FaceNet 主要用于验证两张人脸是否为同一个人，判断依据是“两张人脸的欧氏距离”，如给定一个阈值 \( \alpha = 1 \)，那么：

- 1) 当特征距离等于 0 时，认为两张人脸是同一个人。
- 2) 当特征距离小于 1 时，认为两张人脸是同一个人。
- 3) 当特征距离大于 1 时，认为两张人脸不是同一个人。

三元组损失（Triplet Loss）是 FaceNet 的核心，如图 2-60 所示。FaceNet 希望某个个体的图像 \( x_i^a \) (anchor，锚点) 和该个体其他图像 \( x_i^p \) (positive，正样本) 距离近，与其他个体图像 \( x_i^n \) (negative，负样本) 距离远。损失函数如下所示：

```
\sum_i [ \| f(x_i^a) - f(x_i^p) \|_2^2 - \| f(x_i^a) - f(x_i^n) \|_2^2 + \alpha ]_+
```

## 复习题

- 1. 简述卷积神经网络的起源与发展。
- 2. 简述卷积神经网络的主要组成部分及其功能。
- 3. 典型的卷积神经网络有哪些？分别说明它们的结构和特点。
- 4. 简述 R-CNN 系列目标检测方法的主要思想，并说明它们的结构与优缺点。
- 5. 简述 YOLO 系列目标检测方法的主要思想，并说明它们的结构与优缺点。
- 6. 简述图像分割模型 FCN 的主要思想，并说明它的结构与特点。
- 7. 多人姿态估计算法有哪两类？分别说明它们的定义与优缺点。
- 8. 简述人脸识别算法 FaceNet 的主要思想，并说明它的结构以及三元组损失的定义。

## 实验题

- 1. 基于 MNIST 手写数字识别数据集，构建并实现一个规范的卷积神经网络，使得模型在测试集上的准确率至少达到 98%。要求能够完成数据读取、网络设计、网络构建、模型训练和模型测试等过程。数据集官方下载地址：http://yann.lecun.com/exdb/mnist/。
- 2. 基于猫狗分类数据集，构建并实现一个规范的卷积神经网络，使得模型在测试集上的准确率至少达到 75%。要求能够完成数据读取、网络设计、网络构建、模型训练和模型测试等过程。猫狗分类数据集可在 CIFAR10 数据集中选取，CIFAR10 数据集官方下载地址：https://www.cs.toronto.edu/~kriz/cifar.html。
- 3. 基于 Pascal VOC2007 数据集，使用 YOLOv5 构建并实现一个目标检测模型，实现图像中特定目标的检测，目标可自选。要求能够完成数据读取、网络设计、网络构建、模型训练和模型测试等过程。Pascal VOC2007 数据集官方下载地址：http://host.robots.ox.ac.uk/pascal/VOC/index.html。
- 4. 基于 SYNTHIA 数据集，使用 SegNet 构建并实现一个街景分割模型，实现街景图像分割。要求能够完成数据读取、网络设计、网络构建、模型训练和模型测试等过程。SYNTHIA 数据集官方下载地址：https://synthia-dataset.net/。

## 参考文献

- [1] HUBEL D H, WIESEL T N. Receptive fields, binocular interaction and functional architecture in the cat's visual cortex [J]. Journal of physiology, 1962, 160 (1): 106-154.
- [2] FUKUSHIMA K. Neocognitron: a self organizing neural network model for a mechanism of pattern recognition unaffected by shift in position [J]. Biological cybernetics, 1980, 36 (4): 193-202.
- [3] LECUN Y, BOTTOU L, BENGIO Y, et al. Gradient-based learning applied to document recognition [J]. Proceedings of the IEEE, 1998, 86 (11): 2278-2324.
- [4] KRIZHEVSKY A, SUTSKEVER I, HINTON G E. ImageNet classification with deep convolutional neural networks [C]. Advances in Neural Information Processing Systems, 2012: 1097-1105.
- [5] SIMONYAN K, ZISSERMAN A. Very deep convolutional networks for large-scale image recognition [C]. Proceedings of the 3rd International Conference on Learning Representations, 2015.
- [6] SZEGEDY C, LIU W, JIA Y, et al. Going deeper with convolutions [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2015: 1-9.
- [7] IOFFE S, SZEGEDY C. Batch normalization: accelerating deep network training by reducing internal covariate shift [C]. Proceedings of the 32nd International Conference on Machine Learning, 2015: 448-456.
- [8] SZEGEDY C, VANHOUCKE V, IOFFE S, et al. Rethinking the inception architecture for computer vision [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2016: 2818-2826.
- [9] SZEGEDY C, IOFFE S, VANHOUCKE V, et al. Inception-v4, inception-ResNet and the impact of residual connections on learning [C]. Proceedings of the 31st AAAI Conference on Artificial Intelligence, 2017: 4278-4284.
- [10] HE K, ZHANG X, REN S, et al. Deep residual learning for image recognition [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2016: 770-778.
- [11] HUANG G, LIU Z, MAATEN L, et al. Densely connected convolutional networks [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2017: 2261-2269.
- [12] GIRSHICK R, DONAHUE J, DARRELL T, et al. Rich feature hierarchies for accurate object detection and semantic segmentation [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2014: 580-587.
- [13] GIRSHICK R. Fast R-CNN [C]. Proceedings of International Conference on Computer Vision, 2015: 1440-1448.
- [14] REN S, HE K, GIRSHICK R, et al. Faster R-CNN: towards real-time object detection with region proposal networks [C]. Advances in Neural Information Processing Systems 28, 2015: 91-99.
- [15] REDMON J, DIVVALA S, GIRSHICK R, et al. You only look once: unified, real-time object detection [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2016: 779-788.
- [16] REDMON J, FARHADI A. YOLO9000: better, faster, stronger [C]. Proceedings of IEEE Conference on Computer Vision and Pattern Recognition, 2017: 6517-6525.
- [17] REDMON J, FARHADI A. YOLOv3: an incremental improvement [J]. arXiv, abs/1804.02767, 2018.
