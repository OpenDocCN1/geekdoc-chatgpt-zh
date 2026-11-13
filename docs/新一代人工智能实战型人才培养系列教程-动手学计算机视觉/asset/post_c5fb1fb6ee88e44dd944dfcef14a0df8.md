
```python
'book', 'clock', 'vase', 'scissors', 'teddy bear',
'hair drier', 'toothbrush']

image = cv2.imread('1.png')
image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
image = imutils.resize(image, width=512)
# 进行前向传播，获取预测结果
print("[INFO] making predictions with Mask R-CNN...")
result = model.detect([image], verbose=1)

r1 = result[0]
visualize.display_instances(image, r1['ROIs'], r1['masks'], r1['class_ids'],
class_names, r1['scores'])
```

![](img/0e497601c0ca26be98c5399cbd72eb4c_228_0.png)

可以观察到，Mask R-CNN 可以捕捉到每个独立的物体，并使用一个掩模来描述该物体的具体形状。

### 13.5 小结

本章介绍了实例分割的基础原理，并动手实现了一个实例分割的代表性模型——Mask R-CNN。Mask R-CNN 是 Faster R-CNN 在实例分割任务上的扩展，相比 Faster R-CNN，Mask R-CNN 增加了一个用于掩模预测的分支，更重要的是，Mask R-CNN 在特征提取上实现得更精细：引入 FPN 融合多尺度特征；使用 ROI 对齐取代 ROI 池化，避免了区域特征提取时出现的坐标量化误差。

- （1）ROI 对齐层和 ROI 池化层的区别在哪里？ROI 对齐层有什么优点？
- （2）在 Mask R-CNN 中，掩模分支计算每个类别的预测掩模和真实掩模之间的损失。如果想训练一个类别无关的掩模分支，该怎么设计对应的模块及损失函数？
- （3）在 Mask R-CNN 中，分辨率 14 像素×14 像素的特征图经过掩模分支最终上采样得到的分辨率大小为 28 像素×28 像素，这是因为提高其分辨率可以提高分割精度。如果进一步上采样，分割精度会进一步提高吗？通过实验验证。
- （4）在 Mask R-CNN 中，ROI 对齐是为了解决 ROI 池化所引入的近似误差。除此之外，还可以从其他什么角度去解释 ROI 对齐的优势（提示：变换恒等特性）？

### 13.6 参考文献

- [1] HE K, GKIOXARI G, DOLLAR P, et al. Mask R-CNN[C]//IEEE International Conference on Computer Vision, 2017:2961-2969.
- [2] LIN T Y, DOLLAR P, GIRSHICK R, et al. Feature pyramid networks for object detection [C]//IEEE Conference on Computer Vision and Pattern Recognition, 2017:2117-2125.
- [3] TIAN Z, SHEN C, CHEN H. Conditioned convolutions for instance segmentation[C]//European Conference on Computer Vision, 2020:282-298.

## 第 14 章

## 人体姿态估计

### 14.1 简介

![](img/0e497601c0ca26be98c5399cbd72eb4c_230_0.png)

人体姿态估计（human pose estimation）是计算机视觉的一个重要任务，其目的是识别人体的动作和姿势，这对于很多应用（如运动分析、虚拟现实和人机交互等）都非常重要。在虚拟现实中，人体姿态估计技术有助于跟踪用户的动作，使得虚拟现实体验更加逼真；在人机交互中，人体姿态估计技术有助于设备更好地识别用户的命令，从而使得人机交互更加流畅。

一般来说，人体姿态估计分为二维姿态估计和三维姿态估计，它们之间有一些明显的差异。二维姿态估计用于检测人体在图像中的关节点坐标，输出为二维坐标。因此二维姿态估计类似于语义分割和目标检测这种视觉识别任务，所需技术包括图像特征提取和坐标回归等。三维姿态估计用于识别人体在三维空间中的关节点坐标，输出为三维坐标。因此三维姿态估计通常还需要场景重建技术，如立体视觉、运动估计等。

在本章中，我们主要介绍二维姿态估计，用数学语言描述该任务，即预定义 K 个关节点，给定 N 幅图像，定义图像空间 I，给定数据集 D = { (I^(n), y^(n)) }_1^N，其中 I^(n) in I 是数据集中的第 n 幅图像；y^(n) = (y_1^(n), ..., y_j^(n), ..., y_K^(n)) 是这幅图像中人体关节点真实坐标向量，也称姿态向量，y_j^(n) in R^2 代表第 n 幅图像中第 j 个关节点的二维图像坐标。姿态估计的任务是从 D 中学习得到一个从图像空间到人体关节点坐标向量空间的映射 f，从而给定任意一幅测试图像 I，可以用学习得到的映射函数 f 预测其标签：y* = f(I)。根据人体结构，可以把关节点连接起来，形成人体骨架（skeleton）的形式（如图 14-1 所示），所以人体姿态估计也叫作人体骨架估计。

![](img/0e497601c0ca26be98c5399cbd72eb4c_230_1.png)

![](img/0e497601c0ca26be98c5399cbd72eb4c_230_2.png)

图 14-1 人体姿态估计示意

### 14.2 数据集和度量

本书将介绍与人体姿态估计相关的数据集和评测指标。

#### 14.2.1 数据集

在人体姿态估计中，通常会选择以下几种常用的数据集进行模型的训练与测试。

- (1) Leeds Sports Pose（LSP）[1]：共收录 2000 幅全身姿势图像，这些图像分为 8 类（田径、羽毛球、棒球、体操、跑酷、足球、网球和排球），每幅人体图像都有 14 个关节点。
- (2) Frames Labeled In Cinema（FLIC）[2]：共收录 5003 幅图像，这些图像来自 30 部著名好莱坞电影，每部电影每 10 帧进行一次人体检测，共获得约 20000 个人体候选区，这些候选区被发布到众包平台 Amazon Mechanical Turk 上做标注，每个候选区获得 10 个上半身关节的真实坐标，最后再由人工去除严重遮挡或非正视图的图像。
- (3) Max Planck Institute for Informatics（MPII）Human Pose[3]：包含约 25000 幅图像，其中包含 40000 多个标注了关节点的人，整个数据集涵盖 410 种人类的活动。数据集的图像来自 YouTube，涵盖了室内外场景及各种成像条件，因此该数据集在外观变异性方面具有很高的多样性。研究人员对收集的图像提供了丰富的标注，包括身体关节的位置、头部和躯干的 3D 视角，以及眼睛和鼻子的位置，此外还对所有身体关节和部位的可见性进行了标注。
- (4) Microsoft Common Objects in Context（MS COCO）[4]：MS COCO 数据集不仅用于目标检测、分割、关节点检测等任务，也可以用于人体姿态估计。MS COCO 对每个人体实例都标注了 17 个关节点，包括鼻子、两眼、两耳、肩部、手肘、手腕、髋部、膝盖和脚踝。相比于 MPII 数据集，MS COCO 在人脸结构的标注上更为丰富和多样。

#### 14.2.2 评测指标

在人体姿态估计中，常常使用以下评测指标。

- (1) 正确关节点百分比（percentage of correct keypoints, PCK），有时也称正确关键点百分比，是预测的关节点坐标与其对应的真实坐标间的归一化距离小于设定阈值的比例。

```
\(\text{PCK}_j = \frac{1}{N} \sum_{n=1}^{N} I \left( \frac{\| \hat{y}_j^{(n)} - y_j^{(n)} \|_2}{S} \leqslant \alpha \right)\)
```

其中，\(I\) 是指示函数；\(\hat{y}_j^{(n)}\) 是第 \(n\) 幅图像中第 \(j\) 个关节点的预测坐标；\(y_j^{(n)}\) 是对应的真实坐标；\(N\) 是总图像数；\(S\) 是尺度因子，一般选用人体头部直径；\(\alpha\) 为阈值，一般取 0.5，也就是当预测关节点与真实关节点的距离小于人体尺寸的一半时认为预测正确，此时 PCK 也被写作 PCK@0.5。

- (2) 目标关节点相似度（object keypoint similarity, OKS），有时也称目标关键点相似度。

具体计算公式为

\( \text{OKS} = \frac{\sum_j \exp(-(\| \hat{y}_j^{(n)} - y^{(n)} \|_2) / 2S^2 \sigma_j^2) I(v_j > 0)}{\sum_j I(v_j > 0)} \)

其中，\( I \) 是指示函数；\( v_j \) 是关节点的可见性标志，\( v_j = 1 \) 表示这个关节点无遮挡且已标注，\( v_j = 2 \) 表示关节点有遮挡但已标注；\( S \) 是尺度因子，不过这里的 \( S \) 一般选用人体检测框面积的平方根 \( \sqrt{H \times W} \)，\( H \) 和 \( W \) 是人体检测框的高和宽；\( \sigma_j \) 表示该关节点的归一化因子，通常是根据数据集中关节点标注误差分布而确定的，反映了当前关节点的标注精度，值越大，说明该关节点的标注效果越差，反之则说明标注效果越好。可以看出，OKS 对于关节点的可见性、尺度因子还有关节点标注效果都进行了考虑，能够更加综合地评估人体姿态估计的性能。

### 14.3 人体姿态估计模型——DeepPose

早期的人体姿态估计方法都是基于图像块去预测关节点坐标，并根据人体学的知识加以约束[3]。但是仅根据图像的局部信息很难对关节点进行准确的预测。例如，从图 14-2 左边的两个图像块中很难判断出它们所表示的是人体的哪个部分，因此需要有一种可以将整幅图像作为输入直接预测所有关节点的方法。根据前面几章的内容可知，深度神经网络正好能够实现这个功能，于是就出现了基于深度神经网络的 DeepPose[5]。

![](img/0e497601c0ca26be98c5399cbd72eb4c_232_0.png)

图 14-2 单靠局部信息很难有效预测姿态

接下来我们将详细介绍 DeepPose 的网络架构及训练过程。

#### 14.3.1 基于深度神经网络的人体姿态估计

在训练 DeepPose 之前，首先需要对数据进行预处理。在数据集中，关节点坐标通常以图像的绝对坐标给出，但这种表示方式对于网络学习并不理想。因此，DeepPose 将关节点坐标转换为相对坐标，即将关节点坐标相对于人体包围盒做归一化。具体地，给定一幅图像 \( I \)，定义包围盒 \( b = (b_x, b_y, b_h, b_w) \)。对于人体的一个关节点坐标 \( y_j^{(n)} \)，其相对于包围盒进行归一化可以表示为

```
norm(\mathbf{y}_j^{(n)};\mathbf{b}) = \begin{bmatrix} \frac{1}{b_w} & 0 \\ 0 & \frac{1}{b_h} \end{bmatrix} \left( \mathbf{y}_i^{(n)} - \begin{bmatrix} b_x \\ b_y \end{bmatrix} \right)
```

进一步，可以将同样的归一化应用到姿态向量 \(\mathbf{y}^{(n)}\) 的所有元素上，得到 \(\text{norm}(\mathbf{y}^{(n)};\mathbf{b}) = (\text{norm}(\mathbf{y}_1^{(n)};\mathbf{b}), ..., \text{norm}(\mathbf{y}_j^{(n)};\mathbf{b}), ..., \text{norm}(\mathbf{y}_K^{(n)};\mathbf{b}))\)。为了方便描述，这里使用 \(\text{crop}(\mathbf{I};\mathbf{b})\) 来表示根据包围盒 \(\mathbf{b}\) 对图像 \(\mathbf{I}\) 进行裁剪得到的局部图像。

DeepPose 将姿态估计问题视为一个回归问题，通过训练并使用一个姿态回归器 \(\psi(\mathbf{I};\boldsymbol{\theta})\)，将给定的图像 \(\mathbf{I}\) 回归到一个归一化的姿态向量，其中 \(\boldsymbol{\theta}\) 表示姿态回归器的参数。因此，根据上面提到的归一化变换，图像的绝对坐标中的预测姿态向量 \(\mathbf{y}^*\) 表示为

```
\mathbf{y}^* = \text{norm}^{-1}(\psi(\text{crop}(\mathbf{I};\mathbf{b});\boldsymbol{\theta});\mathbf{b})
```

其中 \(\text{norm}^{-1}(\cdot)\) 是归一化的逆变换，定义为

```
\text{norm}^{-1}(\mathbf{y}';\mathbf{b}) = \begin{bmatrix} b_w & 0 \\ 0 & b_h \end{bmatrix} \mathbf{y}' + \begin{bmatrix} b_x \\ b_y \end{bmatrix}
```

其中，\(\mathbf{y}' = \psi(\text{crop}(\mathbf{I};\mathbf{b});\boldsymbol{\theta})\)。姿态回归器 \(\psi\) 的目标是最小化预测姿态向量 \(\mathbf{y}^*\) 和真实姿态向量 \(\mathbf{y}\) 之间的误差，这里，DeepPose 采用输入尺寸固定为 220 像素 × 220 像素的深度神经网络作为姿态回归器。作为一个回归任务，DeepPose 通过最小化均方误差损失函数来训练网络，从而预测姿态向量。由于真实的姿态向量以图像的绝对坐标定义，且不同图像中的姿态大小不一，其使用上文中定义的归一化方法对训练集 \(\mathcal{D}\) 进行归一化：

```
\mathcal{D}_N = \{(\text{crop}(\mathbf{I};\mathbf{b}), \text{norm}(\mathbf{y};\mathbf{b})) | (\mathbf{I},\mathbf{y}) \in \mathcal{D}\}
```

用于训练 DeepPose 的均方误差损失函数定义为

```
\sum_{(\mathbf{I},\mathbf{y}) \in \mathcal{D}_N} \sum_{j=1}^K \left\| \mathbf{y}_j - \psi(\mathbf{I};\boldsymbol{\theta}) \right\|_2^2
```

需要注意的是，即使某些图像中没有标记所有关节点，上述损失函数也可以使用。在这种情况下，求和中相应的项将被忽略。

#### 14.3.2 级联回归

14.3.1 节中介绍的方法存在一个问题，即网络的输入图像尺寸固定为 220 像素 × 220 像素，这限制了网络对细节的捕捉能力。为了解决这个问题，DeepPose 提出了级联回归的方法。在初始阶段，DeepPose 会估计一个初始姿态。在随后的精化阶段中，对每个关节点训练一个额外的姿态回归器，以此来预测该关节点从上一阶段预测的位置到真实位置的位移。每个精化阶段使用上一阶段预测的关节点位置来集中关注关节点在图像的相关部分，也就是围绕上一阶段预测的关节点位置进行裁剪得到局部图像，并且应用姿态回归器在这个局部图像上预测关节点的精细化位置，如图 14-3 所示。这种级联回归的方法可以提高网络对细节的感知能力，从而提高姿态估计的精度。

DeepPose 对所有阶段使用相同的网络架构，但学习不同的网络参数。对于总共 C 个级联阶段中的阶段 c ∈ {1, ⋯, C}，第 c 个阶段针对第 j 个关节点的姿态回归器表示为 ψ(crop(I; b_j; θ_j^c)，其中 θ_j^c 表示其网络参数。对于给定的关节点坐标 y_j，考虑一个关节点包围盒 b_j，该包围盒捕捉关节点坐标 y_j 周围的局部图像： b_j = (y_j, H_j, W_j)，其中 H_j 和 W_j 代表对第 j 个关节点预定义的包围盒尺寸，取决于关节点的物理意义和数据集分布，在 DeepPose 中 H_j = W_j 。

在第一个阶段（初始阶段），即 c = 1 时，DeepPose 从初始包围盒 b^0 开始检测，该包围盒可以是整幅图像，也可以是由一个人体目标检测器获得的。从包围盒 b^0 预测出初始的关节点坐标：

```
y^1 = norm^{-1}(ψ(crop(I; b^0); θ_1); b^0)
```

在每个后续阶段，即 c ≥ 2 时，对于所有关节点 j ∈ {1, ⋯, K} ，DeepPose 首先使用上一阶段中回归得到的关节点坐标 y_j^{c-1} 来定义局部图像，得到对应的包围盒 b_j^{c-1}，然后使用一个对应的姿态回归器 ψ 来预测关节点的精细化位移 y_j^c - y_j^{c-1} ：

```
y_j^c ← y_j^{c-1} + norm^{-1}(ψ(crop(I; b_j^{c-1}); θ_j^c); b_j^{c-1})
```

这样，就可以得到精化阶段 c 的关节点包围盒 b_j^c：

```
b_j^c ← (y_j^c, H_j, W_j)
```

在实际的实验中，DeepPose 使用了 C = 3 个级联阶段。

![](img/0e497601c0ca26be98c5399cbd72eb4c_234_0.png)

![](img/0e497601c0ca26be98c5399cbd72eb4c_234_1.png)

图 14-3 DeepPose 流程（插图源自参考文献[5]）

DeepPose 还针对关节点进行数据增强。具体做法是随机偏移关节点 j 的真实位置，偏移量
