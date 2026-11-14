# 10.8.1 实例 80: 实现去噪自编码

这次我们把前面所学的知识全部用上一起做一个综合的实例。首先建立一个去噪自编码，然后再对第一层的输出做一次简单的自编码压缩，然后再将第二层的输出做一个 softmax 的分类，最后，把这 3 个网络里的中间层拿出来，组成一个新的网络进行微调。下面就来一一操作。

# 1. 引入头文件，创建网络模型及定义学习参数变量

# 实例描述

对 MNIST 集中的原始输入图片加入噪声，在自编码网络中进行训练，得到抗干扰更强的特征提取模型。

引入头文件，创建 MNIST 数据集。

# 代码10-6 自编码综合

```
01 import numpy as np
02 import tensorflow as tf
03 import matplotlib.pyplot as plt
04 from tensorflow.examples.tutorials.mnist import input_data
05 mnist = input_data.read_data_sets("/data/", one_hot=True)
06
07 train_X = mnist.train.images
08 train_Y = mnist.train.labels
09 test_X = mnist.test.images
10 test_Y = mnist.test.labels
```

# 2. 定义占位符

最终训练的网络为一个输入、一个输出和两个隐藏层，结构如图 10-14 所示。

![](bbox=[0.24, 0.67, 0.72, 0.76])

# 图 10-14 自编码综合实例结构

在这个例子中要建立 4 个网络：每一层都用一个网络来训练，于是我们需要训练 3 个网络，最后再把训练好的各个层组合到一起，形成第 4 个网络。

# 代码10-6 自编码综合（续）

```
11 # NETOWRK PARAMETERS
12 n_input = 784
13 n_hidden_1 = 256
```