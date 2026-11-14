图 9-8 中，初始的输入有 5 个，其中 4 个是中间状态，1 个是 x 的序列值。通过一层具有 4 个节点的 RNN 网络，再接一个全连接层输出 0、1 分类。这样序列中的每个 x 都会有一个对应的预测分类值，最终将整个序列 x 生成了预测序列。具体步骤如下。

# 实例描述

构建一组序列，生成其对应的模拟回声序列。使用 TensorFlow 创建一个简单循环神经网络拟合这个回声序列。

# 1. 定义参数生成样本数据

在了解前面样本的规则后，开始编写代码制作样本。

导入 Python 库，定义相关参数，取 50000 个序列样本数据，每个测试数据截取 15 个序列，回声序列的步长为 3，最小批次为 5。定义生成样本函数 generateData，在函数里先随机生成 50000 个 0、1 数据的数组 x，作为原始的序列，令 x 里的数据向右循环移动 3 个位置，生成数据 y，作为 x 的回声序列。因为回声步长是 3，表明回声 y 是从 x 的第 3 个数据开始才出现，所以将 y 的前 3 个数据清零。

# 代码9-2 echo模拟

```
01 import numpy as np
02 import tensorflow as tf
03 import matplotlib.pyplot as plt
04 
05 num_epochs = 5
06 total_series_length = 50000
07 truncated_backprop_length = 15
08 state_size = 4
09 num_classes = 2
10 echo_step = 3
11 batch_size = 5
12 num_batches = total_series_length//batch_size//truncated_backprop_length
13 length
14 def generateData():
15 x = np.array(np.random.choice(2, total_series_length, p=[0.5, #在 0 和 1 中选择total_series_length个数
16 0.5]))
17 y = np.roll(x, echo_step)#向右循环移位，将【1111000】变为【0001111】
18 y[0:echo_step] = 0
19 
20 x = x.reshape((batch_size, -1)) # 5,10000
21 y = y.reshape((batch_size, -1))
22 
23 return (x, y)
```

# 2. 定义占位符处理输入数据

定义 3 个占位符，输入的 batchX_placeholder 原始序列，回声 batchY_placeholder 作为标签，循环节点的初始值 state。如前面介绍的网络结构，x 的原始序列是逐个输入网络的，