识看看可以用几种方法来实现（可参考本书源代码中的代码“7-4 异或 one_hot.py”文件）。

# 7.3 实例 31：利用全连接网络将图片进行分类

本例使用全连接网络，将第 5 章中的例子重新实现一遍，将 MNIST 图像用多层神经网络来分类。

# 实例描述

构建一个简单的多层神经网络，以拟合 MNIST 样本特征完成分类任务。

# 1. 定义网络参数

在输入和输出之间使用两个隐藏层，每层各 256 个节点，学习率使用 0.001。

代码7-5 MNIST多层分类

```
01 # 定义参数
02 learning_rate = 0.001
03 training_epochs = 25
04 batch_size = 100
05 display_step = 1
06
07 # 设置网络模型参数
08 n_hidden_1 = 256 # 第一个隐藏层节点个数
09 n_hidden_2 = 256 # 第二个隐藏层节点个数
10 n_input = 784 # MNIST 共 784 (28×28)维
11 n_classes = 10 # MNIST 共 10 个类别 (0~9)
```

# 2. 定义网络结构

multilayer_perceptron 函数为封装好的网络模型函数，第一层与第二层均使用 Relu 激活函数，loss 使用 softmax 交叉熵。具体代码如下。

代码7-5 MNIST多层分类（续）

```
12 #定义占位符
13 x = tf.placeholder("float", [None, n_input])
14 y = tf.placeholder("float", [None, n_classes])
15
16 # 创建 model
17 def multilayer_perceptron(x, weights, biases):
18 # 第一层隐藏层
19 layer_1 = tf.add(tf.matmul(x, weights['h1']), biases['b1'])
20 layer_1 = tf.nn.relu(layer_1)
21 # 第二层隐藏层
22 layer_2 = tf.add(tf.matmul(layer_1, weights['h2']), biases['b2'])
23 layer_2 = tf.nn.relu(layer_2)
24 # 输出层
```