

考虑时间信息。在本节中，我们将介绍一个经典的动作识别模型——C3D[1]（convolutional 3D），它利用三维卷积网络来提取视频数据中的时空特征，从而实现高精确度的动作识别。

### 15.3.1 三维卷积

在介绍 C3D 之前，我们先来了解一下其核心组件——三维卷积。三维卷积是对二维卷积的自然扩展。在二维卷积中，卷积核在输入的二维数据（如图像）上滑动，计算邻近区域的加权和。而在三维卷积中，卷积核除了在宽度（W）和高度（H）上滑动外，还会在深度（D）上滑动，如图 15-2 所示。这里深度通常对应于视频帧的时间维度。给定视频序列 S（假设其中的图像均为灰度图），将其理解成一个三阶张量，对其进行三维卷积的数学表达式为

```
G(i, j, k) = \sum_{m=1}^{k_d} \sum_{n=1}^{k_h} \sum_{p=1}^{k_w} K(m, n, p) \cdot S(i-m, j-n, k-p)
```

其中，K 是三维卷积核；G 是输出张量；\(k_d\)、\(k_h\) 和 \(k_w\) 分别表示卷积核在空间宽度、高度和时间深度上的尺寸。三维卷积的补零、步长等操作与二维卷积类似，这里不做展开。

![](img/0e497601c0ca26be98c5399cbd72eb4c_241_0.png)

图 15-2 三维卷积示意

下面展示一个简易的三维卷积的例子。

```
import numpy as np
import pprint as pp

def generate_increasing_volume(d, h, w):
    """
    生成一个递增的张量，每个元素的值为其在张量中的索引值加 1

    参数:
    - d, h, w: 生成张量的深度、高度和宽度

    返回:
    - volume: 生成的递增张量
    """
    return np.arange(1, d*h*w + 1).reshape(d, h, w)

def conv3d(input_volume, kernel):
    """
    对给定的输入张量和指定的卷积核进行三维卷积操作

    参数:
    - input_volume: 三维 numpy 数组，维度对应深度、高度和宽度
    - kernel: 三维 numpy 数组，维度比输入张量阶数小，且每个维度的大小为奇数

    返回:
    - output_volume: 三维 numpy 数组, 卷积操作的输出张量
    """
    d, h, w = input_volume.shape
    kd, kh, kw = kernel.shape

    # 计算输出张量的阶数
    output_d = d - kd + 1
    output_h = h - kh + 1
    output_w = w - kw + 1

    # 初始化输出的张量
    output_volume = np.zeros((output_d, output_h, output_w))

    # 计算三维卷积
    for i in range(output_d):
        for j in range(output_h):
            for k in range(output_w):
                sub_volume = input_volume[i:i+kd, j:j+kh, k:k+kw]
                output_volume[i, j, k] = np.sum(sub_volume * kernel)

    return output_volume

# 生成一个 4x4x4 的三阶递增张量
input_volume_increasing = generate_increasing_volume(4, 4, 4)
kernel = np.array([[[1, 1], [1, 1]], [[1, 1], [1, 1]]]) # 一个 2x2x2 的核

# 对递增张量进行三维卷积操作
output_volume_increasing = conv3d(input_volume_increasing, kernel)
print("输入张量的部分:")
pp.pprint(input_volume_increasing[0:2, 0:2, 0:2])
print("核:")
pp.pprint(kernel)
print("输出张量的部分:")
print(output_volume_increasing[0, 0, 0])
```

```
输入张量的部分:
array([[[ 1,  2],
        [ 5,  6]],

       [[17, 18],
        [21, 22]]])
核:
array([[[1, 1],
        [1, 1]],

       [[1, 1],
        [1, 1]]])
输出张量的部分:
92.0
```

在此例中，我们使用一个 2×2×2 的全 1 卷积核，对一个元素递增的 4×4×4 张量进行三维卷积运算。卷积核在输入张量上滑动，对每个局部张量进行乘法和求和操作，得到一个 3×3×3 的输出张量。

三维卷积与二维卷积相比，其主要优势在于能够直接处理视频数据的时间维度信息。二维卷积只在空间上进行操作，因此在图像上卷积后还需要设计融合时间信息的方法。而三维卷积能够直接捕捉视频序列的时间连续性，从而更有效地识别并分析动态场景和行为。这使得三维卷积在动作识别和其他视频分析任务中具有显著优势。

### 15.3.2 C3D 模型

正因为三维卷积的优势，Du Tran 等人[1]于 2014 年提出直接使用三维卷积网络来提取视频数据中的时空特征，从而实现高精确度的视频动作识别。他们提出的 C3D 模型是一个端到端的三维卷积网络，由 8 个卷积层、5 个池化层、2 个全连接层和 1 个 softmax 层组成，其网络架构如图 15-3 所示。

![](img/0e497601c0ca26be98c5399cbd72eb4c_243_0.png)

图 15-3 C3D 模型示意

C3D 模型的输入是 16×112×112×3 的视频数据，其中 16 表示输入的视频序列长度，112 表示视频帧的宽度和高度，3 表示视频帧的通道数。根据对二维卷积神经网络的研究发现[7]，小的感受野（3×3 卷积核）配合更深的网络架构可以获得最佳结果。因此，在 C3D 的架构搜索研究中，Du Tran 等人将空间感受野固定为 3×3，只改变三维卷积核的时间深度，并且通过实验发现，3×3×3 的卷积核效果最好。

![](img/0e497601c0ca26be98c5399cbd72eb4c_243_1.png)

图 15-4 t-SNE 可视化（插图源自参考文献[1]）（另见彩插图 16）

Du Tran 等人通过在另一数据集上可视化学习到的特征来定性评估 C3D 的通用性，确认这些特征是否适合视频分析。具体来说，他们从 UCF101 数据集随机挑选了 10 万个视频序列，使用 C3D 提取这些序列的特征，并使用 t-SNE[8] 将特征映射到二维空间。图 15-4 展示了结果，同一动作的序列显示相同颜色。结果表明，C3D 提取的特征区分明显，表明其能够有效地提取视频数据中的时空特征。

## 15.4 C3D 代码实现

前面我们已经介绍了三维卷积和 C3D 模型，接下来将开始动手实现一个 C3D 模型并在 UCF101 数据集上进行训练和测试。

```
import torch.nn as nn

class C3D(nn.Module):
    """
    C3D, Convolution 3D, 即三维卷积
    """

    def __init__(self):
        super(C3D, self).__init__()
        # 注意到核函数为三维，符合三维卷积的要求
        self.conv1 = nn.Conv3d(3, 64, kernel_size=(3, 3, 3), padding=(1, 1, 1))
        self.pool1 = nn.MaxPool3d(kernel_size=(1, 2, 2), stride=(1, 2, 2))
        self.conv2 = nn.Conv3d(64, 128, kernel_size=(3, 3, 3), padding=(1, 1, 1))
        self.pool2 = nn.MaxPool3d(kernel_size=(2, 2, 2), stride=(2, 2, 2))
        self.conv3a = nn.Conv3d(128, 256, kernel_size=(3, 3, 3), padding=(1, 1, 1))
        self.conv3b = nn.Conv3d(256, 256, kernel_size=(3, 3, 3), padding=(1, 1, 1))
        self.pool3 = nn.MaxPool3d(kernel_size=(2, 2, 2), stride=(2, 2, 2))
        self.conv4a = nn.Conv3d(256, 512, kernel_size=(3, 3, 3), padding=(1, 1, 1))
        self.conv4b = nn.Conv3d(512, 512, kernel_size=(3, 3, 3), padding=(1, 1, 1))
        self.pool4 = nn.MaxPool3d(kernel_size=(2, 2, 2), stride=(2, 2, 2))
        self.conv5a = nn.Conv3d(512, 512, kernel_size=(3, 3, 3), padding=(1, 1, 1))
        self.conv5b = nn.Conv3d(512, 512, kernel_size=(3, 3, 3), padding=(1, 1, 1))
        self.pool5 = nn.MaxPool3d(kernel_size=(2, 2, 2), stride=(2, 2, 2),
                                  padding=(0, 1, 1))

        self.fc6 = nn.Linear(8192, 4096)
        self.fc7 = nn.Linear(4096, 4096)
        self.fc8 = nn.Linear(4096, 487)

        self.dropout = nn.Dropout(p=0.5)
        self.relu = nn.ReLU()
        self.softmax = nn.Softmax()

    def forward(self, x):
        h = self.relu(self.conv1(x))
        h = self.pool1(h)

        h = self.relu(self.conv2(h))
        h = self.pool2(h)

        h = self.relu(self.conv3a(h))
        h = self.relu(self.conv3b(h))
        h = self.pool3(h)

        h = self.relu(self.conv4a(h))
        h = self.relu(self.conv4b(h))
        h = self.pool4(h)

        h = self.relu(self.conv5a(h))
        h = self.relu(self.conv5b(h))
        h = self.pool5(h)

        h = h.view(-1, 8192)
        h = self.relu(self.fc6(h))
        h = self.dropout(h)
        h = self.relu(self.fc7(h))
        h = self.dropout(h)

        logits = self.fc8(h)
        probs = self.softmax(logits)

        return probs
```

我们将基于 C3D 在 UCF101 数据集中实现视频动作识别。这里将直接调用已经完成的代码，可以在 GitHub 链接中查看详细的项目。

```
!git clone https://github.com/Niki173/C3D
```

在配置好 UCF101 数据集和模型预训练的权重之后，便可以进行 C3D 的训练。

```
!python train.py
```

这里只展示部分训练流程。

```
100%|██████████| 8468/8468 [00:10<00:00, 17.25it/s]
[train] Epoch: 19/20 Loss: 0.1322654498087974 Acc: 0.959744680851064
Execution Time: 498.36248720896976

100%|██████████| 2159/2159 [01:02<00:00, 34.49it/s]
[val] Epoch: 19/20 Loss: 0.115616626699023 Acc: 0.9689671144048171
Execution Time: 62.59326397183543

100%|██████████| 8468/8468 [00:12<00:00, 17.17it/s]
[train] Epoch: 20/20 Loss: 0.12648102252588242 Acc: 0.9601654946335697
Execution Time: 492.609539682134835

100%|██████████| 2159/2159 [01:02<00:00, 34.75it/s]
[val] Epoch: 20/20 Loss: 0.1177748827809089 Acc: 0.9597935664595564
Execution Time: 62.13380773461913
```

最后，我们在测试集上进行测试，精确度可达 96%。

```
100%|██████████| 2701/2701 [01:16<00:00, 35.14it/s]
[test] Epoch: 20/20 Loss: 0.1257139757889785 Acc: 0.9683850425768234
Execution time: 76.86971226181741
```

## 15.5 小结

本章介绍了人体动作识别的基本原理、常用的数据集和评测指标，并详细分析了一个经典的动作识别模型——C3D。C3D 模型利用三维卷积网络来提取视频数据中的时空特征，从而实现高精确度的动作识别。最后，我们展示了如何在 UCF101 数据集上训练和验证 C3D 模型，取得了不错的效果。回顾我们的动手学旅程，从图像的基础处理到图像及视频的语义理解，我们都在不停地贴近真实世界，而真实世界是三维的，因此接下来我们将介绍场景重建，探索计算机视觉算法在三维世界中的应用。

## 习题

- (1) 假设视频数据尺寸为 16×512×512×3，使用一个 3×3×3 的卷积核进行卷积计算，输出的尺寸是多少？
- (2) 在习题（1）中，如果考虑补零和步长为 2，输出的尺寸是多少？
- (3) 三维卷积需要衔接对应的三维池化层。实现一个三维池化层。
- (4) 能否用二维卷积对每一帧图像提取特征，然后按照时间顺序融合起来实现类似三维卷积的效果？
- (5) 我们将在第 17 章中介绍视频的光流计算。光流计算对于动作识别是否有帮助？如果有，如何将光流计算和三维卷积结合起来？

## 15.6 参考文献

- [1] TRAN D, BOURDEV L, FERGUS R, et al. Learning spatiotemporal features with 3D convolutional networks[C]//IEEE International Conference on Computer Vision, 2015:4489-4497.
- [2] SOOMRO K, ZAMIR A R, SHAH M. UCF101: A dataset of 101 human actions classes from videos in the wild[EB/OL]. (2012-12-03)[2024-09-25]. arXiv:1212.0402.
- [3] KARPATHY A, TODERICI G, SHETTY S, et al. Large-scale video classification with convolutional neural networks[C]//IEEE Conference on Computer Vision and Pattern Recognition, 2014:1725-1732.
- [4] KAY W, CARREIRA J, SIMONYAN K, et al. The Kinetics human action video dataset[EB/OL]. (2017-05-19)[2024-09-25]. arXiv:1705.06950.
- [5] CARREIRA J, NOLAND E, BANKI-HORVATH A, et al. A short note about Kinetics-600[EB/OL]. (2018-08-03)[2024-09-25]. arXiv:1808.01340.
- [6] CARREIRA J, NOLAND E, HILLIER C, et al. A short note on the Kinetics-700 human action dataset[EB/OL]. (2020-10-17)[2024-09-25]. arXiv:1907.06987.
- [7] SIMONYAN K, ZISSERMAN A. Very deep convolutional networks for large-scale image recognition[EB/OL]. (2015-04-10)[2024-09-25]. arXiv:1409.1556.
- [8] VAN DER MAATEN L, HINTON G. Visualizing data using t-SNE[J]. Journal of Machine Learning research, 2008, 9(11).

