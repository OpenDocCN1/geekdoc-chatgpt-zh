

可以发现，我们绘制的极线还是比较准确的。

## 19.5 小结

本章探讨了三维重建的基础知识，重点关注了对极几何和三角测量的原理。我们通过对极约束求解基础矩阵并进一步分解以求得照相机的位姿，随后利用三角测量确定空间中点的三维坐标。尽管只得到了非常稀疏的三维点坐标，但这些点的坐标是高度可靠的，为场景提供了必要的三维结构支撑，为后续的深入分析和应用打下了坚实的基础。

从二维图像中恢复出三维结构无疑是一项具有挑战性的任务。本章介绍的内容虽然只涵盖了一些基本的三维重建方法，但这些方法为理解和构建更复杂的三维视觉系统奠定了重要的基础。希望读者能从这些初步的概念中领悟三维重建的核心思想，并在此基础上进一步探索和扩展。

## 习题

- (1) 如果两台照相机中有一台的成像平面平行于基线，在这种情况下，该照相机对应的极点和极线是否存在？如果存在，提供它们的位置。

- (2) 回顾第 18 章平行双目视觉系统，其分度值受限于基线长度和照相机内参。在本章中介绍的非平行双目系统，它的分度值是否还有此限制？

- (3) 证明 F 矩阵是秩为 2 的奇异矩阵。

- (4) 假定已知一个图像对的 F 为

  $$ F = \begin{bmatrix} -0.003 & -0.028 & 13.19 \\ -0.003 & -0.008 & -29.2 \\ 2.97 & 56.38 & -9999 \end{bmatrix} $$

  求像素坐标 \(\tilde{\boldsymbol{u}}_i = [343 \quad 221 \quad 1]^{\mathsf{T}}\) 的极线。

- (5) 如图 19-9 所示，\(\boldsymbol{M}_1\) 和 \(\boldsymbol{M}_2\) 为左、右两照相机的投影矩阵，假设 \(\boldsymbol{M}_1 = [\boldsymbol{I} \mid \boldsymbol{0}]\)、\(\boldsymbol{M}_2 = [\boldsymbol{A} \mid \boldsymbol{a}]\)，其中，\(\boldsymbol{A}\) 是 \(3 \times 3\) 的非奇异矩阵。证明：

  - (a) 基础矩阵 \(\boldsymbol{F} = [\boldsymbol{a}]_{\times} \boldsymbol{A}\)；
  - (b) \(\boldsymbol{a}\) 是某个极点的坐标。

![](img/0e497601c0ca26be98c5399cbd72eb4c_310_0.png)

图 19-9 习题（5）示意（另见彩插图 26）

## 总结与展望

亲爱的读者，你已经完成了对本书内容的学习，包括：

- 计算机视觉中的图像处理；
- 计算机视觉中的视觉识别方法，特别是基于深度学习的视觉识别方法；
- 计算机视觉中的场景重建。

至此，你已经掌握了计算机视觉的基本知识，更拥有了第一手的计算机视觉代码实践经验。但要注意的是，本书只是探索计算机视觉广袤世界的开始。一方面，计算机视觉涉及的问题众多，本书并未涵盖计算机视觉所有子领域；另一方面，计算机视觉的发展日新月异，本书所介绍的内容是比较经典的计算机视觉算法，并未涵盖计算机视觉领域最新的进展。随着大数据、大模型时代的到来，计算机视觉领域正面临着巨大的变革。在本书的最后，笔者以浅薄的学识对计算机视觉技术在大数据时代下的发展做一些展望。

- **视觉大模型的兴起。**在模型方面，深度卷积神经网络逐渐不再是计算机视觉的唯一专用主干网络模型，原本用于自然语音处理的深度自注意力神经网络也被广泛用于计算机视觉。相比卷积神经网络，深度自注意力神经网络引入了自注意力机制，在处理具有长程依赖的图像结构时更具优势；同时，自注意力机制中没有显式的参数共享，可以更好地捕捉输入之间的关系，从而提高模型的泛化能力，这使得深度自注意力神经网络对大数据具有更强的拟合能力。故而在大数据时代下，深度自注意力神经网络逐渐成为用于解决计算机视觉任务的视觉大模型。
- **无监督预训练范式的普及。**大数据时代，数据往往是无标注的，以往基于有标注数据的视觉模型主干网络预训练范式已无法适应时代的发展。取而代之的是基于无标注大数据的无监督预训练范式。事实上无监督预训练范式，如掩模语言建模（masked language modeling）已经在自然语言处理领域取得了巨大成功。类似的方法，如掩模图像建模（masked image modeling）也将在计算机视觉中得到广泛应用。基于无监督预训练的视觉大模型可以拟合大规模图像数据集，使视觉大模型具有很强的泛化能力，还可在特定任务上进行微调。这种方法将加速计算机视觉任务的开发和部署。
- **多模态融合与跨模态学习的发展。**随着大模型的发展，跨模态学习（如图像与文本之间的学习）也将变得更加重要。通过联合不同模态的数据进行学习，有效融合多模态信息，可以获得更全面、更准确的视觉理解。这将有助于实现图像和文本之间的深层次交互，从而推动计算机视觉与自然语言处理等领域的融合与协同发展。
- 高性能硬件加速器的需求。视觉大模型需要大量的计算资源来训练和推理，包括但不限于硬件的计算能力、内存容量、并行计算能力、实时推理和能效。计算机视觉技术的进一步发展势必会推高对高性能硬件加速器的需求。

总之，大数据、大模型时代将为计算机视觉技术带来许多机遇和挑战。透过大规模数据和强大的计算能力，我们可以期待计算机视觉技术在自动驾驶、人机交互、视频监控、医学影像辅助诊断等场景下的更多落地应用。

## 中英文术语对照表

| 中文术语 | 英文术语 |
|---|---|
| k 均值 | k-means |
| top-k 错误率 | top-k error |
| 包围盒 | bounding box |
| 本质矩阵 | essential matrix |
| 边缘 | edge |
| 表象 | appearance |
| 步长 | stride |
| 查全率 | recall |
| 查准率 | precision |
| 查准率-查全率 | precision-recall (PR) |
| 尺度不变特征变换 | scale-invariant feature transform (SIFT) |
| 重复性 | repeatability |
| 楚列斯基分解 | Cholesky decomposition |
| 词袋模型 | bag of words model |
| 磁共振成像 | magnetic resonance imaging (MRI) |
| 单应性矩阵 | homography matrix |
| 底层视觉 | low-level vision |
| 定位 | localization |
| 度量 | metric |
| 短路连接 | shortcut connection |
| 对极点 | epipoles |
| 对极几何 | epipolar geometry |
| 对极平面 | epipolar plane |
| 对极线 | epipolar line |
| 对极约束 | epipolar constraint |
| 仿射变换 | affine transformation |
| 非极大值抑制 | non-maximum suppression |
| 幅值 | magnitude |
| 感兴趣区域 | region of interest (ROI) |
| 感兴趣区域对齐 | ROI align |
| 高层视觉 | high-level vision |
| 高斯差分 | difference of Gaussian |
| 高斯拉普拉斯 | Laplacian of Gaussian |
| 高斯滤波器 | Gaussian filter |
| 高斯-牛顿法 | Gauss-Newton method |
| 高斯噪声 | Gaussian noise |
| 骨架 | skeleton |
| 关键点特征 | keypoint feature |
| 光流 | optical flow |
| 光学字符识别 | optical character recognition (OCR) |
| 归一化互相关 | normalized cross correlation |
| 归一化割 | normalized cut |
| 黑塞矩阵 | Hessian matrix |
| 候选目标检测 | object proposal detection |
| 候选区域 | region proposal |
| 候选区域网络 | region proposal network |
| 滑动窗口 | sliding window |
| 会聚度 | convergence |
| 基础矩阵 | fundamental matrix |
| 基线 | baseline |
| 基于滞后的阈值化 | hysteresis-based thresholding |
| 畸变 | distortion |
| 计算机断层扫描 | computed tomography (CT) |
| 假阳性 | false positive (FP) |
| 假阴性 | false negative (FN) |
| 交并比 | intersection over union |
| 椒盐噪声 | salt & pepper noise |
| 角点 | corner |
| 紧性 | compactness |
| 精确度 | accuracy |
| 聚类 | clustering |
| 卷积 | convolution |
| 卷积神经网络 | convolutional neural network (CNN) |
| 绝对差和 | sum of absolute difference |
| 均值滤波器 | mean filter |
| 空间金字塔匹配 | spatial pyramid matching |
| 空间下采样因子 | spatial downsampling factor |
| 块状区域 | blob |
| 类别标签图 | label map |
| 离群点 | outlier |
| 良态 | well conditioned |
| 列文伯格-马夸特法 | Levenberg-Marquardt method |
| 零填充 | zero-padding |
| 鲁棒性 | robustness |
| 滤波器 | filter |
| 锚框 | anchor |
| 目标关节点相似度 | object keypoint similarity |
| 目标检测 | object detection |
| 内群点 | inlier |
| 批量标准化 | batch normalization |
| 平方差和 | sum of squared difference (SSD) |
| 平均池化 | average pooling |
| 平均交并比 | mean intersection over union |
| 平均精度 | average precision (AP) |
| 齐次坐标系 | homogeneous coordinate system |
| 区域卷积神经网络 | region convolutional neural network (R-CNN) |
| 全卷积网络 | fully convolutional network (FCN) |
| 全类平均精度 | mean average precision (mAP) |
| 热图 | heatmap |
| 人体动作识别 | human action recognition |
| 人体姿态估计 | human pose estimation |
| 三角测量 | triangulation |
| 上采样 | upsampling |
| 射影变换 | projective transformation |
| 深度卷积神经网络 | deep convolutional neural network |
| 深度图 | depth map |
| 施密特正交化 | Schmidt orthogonalization |
| 实例分割 | instance segmentation |
| 视差 | disparity |
| 视差图 | disparity map |
| 视觉词袋模型 | bag of features |
| 随机抽样一致 | random sample consensus |
| 特征金字塔网络 | feature pyramid network |
| 梯度 | gradient |
| 跳跃链接 | skip-connection |
| 图像变换 | image transform |
| 图像分割 | image segmentation |
| 图像分类 | image classification |
| 图像块 | image patch |
| 图像匹配 | image matching |
| 图像拼接 | image stitching |
| 图像去噪 | image denoising |
| 图像锐化 | image sharpening |
| 无监督图像分割 | unsupervised image segmentation |
| 无监督学习 | unsupervised learning |
| 显著性 | saliency |
| 小批次 | mini-batch |
| 兴趣点特征 | interesting point feature |
| 掩模 | mask |
| 掩模图像建模 | masked image modeling |
| 掩模语言建模 | masked language modeling |
| 有监督图像分割 | supervised image segmentation |
| 语义分割 | semantic segmentation |
| 语义鸿沟 | semantic gap |
| 预训练 | pretrain |
| 运动场 | motion field |
| 栅格 | grid |
| 照相机标定 | camera calibration |
| 照相机内参 | camera intrinsics |
| 照相机外参 | camera extrinsics |
| 真阳性 | true positive (TP) |
| 正确关节点百分比 | percentage of correct keypoints |
| 中值滤波器 | median filter |
| 逐元素相加 | element-wise sum |
| 最大池化 | max pooling |
| 最高水平 | state-of-the-art (SOTA) |
| 最小割 | minimum cut |
| 坐标偏移量 | coordinate offset |

## 计算机视觉

HANDS-ON COMPUTER VISION

本书源自上海交通大学人工智能（卓越人才试点班）计算机视觉课程讲义，核心内容分为图像处理、视觉识别和场景重建 3 个部分，不仅包含基于传统数学模型的经典算法，还包含基于深度学习模型的前沿算法，全面覆盖了计算机视觉的知识体系。本书还配备了大量可在线运行的代码，帮助读者通过动手实战深入掌握计算机视觉技术。无论是对于初学者还是希望继续深研计算机视觉领域知识体系的读者，这都是一本非常值得推荐的好书。

> ——杨小康 上海交通大学人工智能研究院常务副院长、IEEE 会士

本书结合图文、代码和视频等丰富的内容形式，深入浅出地讲解计算机视觉的核心理论。每章不仅配置了详细的理论讲解视频，还提供了大量可在线运行的代码，让读者能够边学边练。本书还配备了电子资料仓库，为读者学习提供了丰富的资源，是一本不可多得的计算机视觉教材。强烈推荐！

> ——厚卓文 加州大学圣地亚哥分校认知科学系教授、IEEE 会士

计算机视觉是人工智能皇冠上的耀眼明珠，内涵深刻，外延宽广。我非常欣赏本书的两个特点：一是它从图像处理入手，由浅入深、循序渐进地展开计算机视觉的全貌，非常适合想踏入此领域的零基础读者参考学习；二是它配套了大量可在线运行的代码，不仅给读者提供了动手实战的机会，还有利于加深读者对本书理论知识的理解。本书是有志进入计算机视觉领域的读者的必读书。

> ——山世光 中国科学院智能信息处理重点实验室主任、IEEE 会士

计算机视觉是人工智能的一个重要应用领域。学习计算机视觉不仅需要深入理解计算机视觉算法背后的数学原理，还需要强大的动手实战能力。本书正是以此为目标进行编写的，通过详细的理论知识讲解和丰富的代码示例，帮助读者快速入门并尝试编程实现各种计算机视觉算法。在此将本书推荐给每一位想要学习计算机视觉的读者。

> ——白翔 华中科技大学软件学院院长、IAPR 会士

从图像处理到视觉识别，再到场景重建，本书涵盖了计算机视觉领域的关键内容，也提供了方便读者动手实战的在线代码，是一本既能学知识又能锻炼实战能力的前沿教材，值得推荐给每一位希望学习计算机视觉的读者。

> ——张军平 复旦大学计算机科学技术学院教授、中国自动化学会普及工作委员会主任

封面设计：董志彬
分类建议：计算机 / 人工智能 / 计算机视觉
人民邮电出版社网址：www.ptpress.com.cn

定价：89.80元

