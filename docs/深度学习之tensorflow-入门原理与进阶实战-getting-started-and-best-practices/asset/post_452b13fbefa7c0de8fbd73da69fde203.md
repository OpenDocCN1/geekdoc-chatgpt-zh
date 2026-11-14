
## 11. 模式自编码网络验证

这次我们暂时略过对第 3 层网络模型的单独验证，直接去验证整个分类模型，将 MNIST 数据输入进去，看看栈式自编码器的分类效果如何。

代码10-6 自编码综合（续）

```
175 # 测试 model
176     correct_prediction = tf.equal(tf.argmax(pred, 1), tf.argmax(l3y, 1))
177     # 计算准确率
178     accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))
179     print ("Accuracy:", accuracy.eval({x: mnist.test.images, l3y: mnist.test.labels}))
```

执行上面的代码，生成如下信息：

Accuracy: 0.9213

可以看出，直接将每层的训练参数堆起来，网络会有很好的表现。为了进一步优化，来看看下面的步骤。

## 12. 级联微调

下面进入微调阶段，将网络模型联起来进行分类训练，这部分的测试代码与前面一样，所以这里只把训练部分的代码贴出来。

代码10-6 自编码综合（续）

```
180 with tf.Session() as sess:
181     sess.run(tf.global_variables_initializer())
182
183     print ("开始训练")
184     for epoch in range(epochs):
185         num_batch = int(mnist.train.num_examples/batch_size)
186         total_cost = 0.
187         for i in range(num_batch):
188             batch_xs, batch_ys = mnist.train.next_batch(batch_size)
189             feeds = {x: batch_xs, l3y: batch_ys}
190             sess.run(optm3, feed_dict=feeds)
191             total_cost += sess.run(cost3, feed_dict=feeds)
192         # 输出 cost
193         if epoch % disp_step == 0:
194             print ("Epoch %02d/%02d average cost: %.6f"
195             % (epoch, epochs, total_cost/num_batch))
196
197     print ("完成 级联 训练")
```

执行上面的代码，生成如下信息：

开始训练
Epoch 00/50 average cost: 1.003439
Epoch 10/50 average cost: 0.035012
Epoch 20/50 average cost: 0.001034
Epoch 30/50 average cost: 0.000112
Epoch 40/50 average cost: 0.000039
完成 级联训练

可以看到，由于网络模型中各层的初始值已经训练好了，所以开始就是很低的错误率，错误率随着每次的迭代都有很大幅度的下降。到此这个例子就算是完成了，该例已经非常接近真实工作中的场景了，读者学习时在跟着做例子的同时更要理解所使用的方法，它可以将任何复杂的任务化简。

提示：搭建网络模型时，层数过多，参数也会随之增加，所以逻辑一定要清楚，一个好的命名规范可以让你在这个问题上轻松许多。

## 10.8.2 实例 81: 添加模型存储支持分布训练

栈式自编码的另一个优点就是可以将神经网络模块化，便于分工，非常适合团队合作。在实际中，为了得到更好的模型，需要将上述的每个环节分别单独训练，由不同的小组或人员来分步工作。

小组或人员之间的对接则需要通过模型文件来完成，这就要求每个环节的参数都必须保存下来，在自己的步骤开始之前先把上个步骤的环境加载进去。

### 实例描述

对自编码模型进行分布式模型存储与载入，使每一层都可以单个环节逐一训练。可以修改上述代码，在训练开始前定义模型保存参数。

### 代码10-7 分布自编码综合

```
01 savedir = "d:/python/log/"
02 saver = tf.train.Saver(max_to_keep=1) #保存一个模型
03 load_epoch = 49
04 print (savedir)
05 with tf.Session() as sess:
06 ......
```

在每个训练迭代之后都将模型保存起来。

### 代码10-7 分布自编码综合（续）

```
07 ......
08 if epoch % disp_step == 0:
09     print ("Epoch %02d/%02d average cost: %.6f"
10         % (epoch, epochs, total_cost/num_batch))
11    saver.save(sess, savedir + 'stacked_auto_encoder.ckpt', global_step=epoch)
12     print ("完成 级联 训练")
```

在每次训练的开始将模型读入（第一个网络不需要读入）。

### 代码10-7 分布自编码综合（续）

```
13 with tf.Session() as sess:
14 sess.run(tf.global_variables_initializer())
15 saver.restore(sess, savedir +"stacked_auto_encoder.ckpt-" + str(load_epoch))
16 ......
```

注意：这里给出的代码是按照迭代保存的。这是一种好习惯，尤其在训练海量数据时，由于某种意外导致训练终止，使所有的训练结果丢失，还得需要花大量的时间重新训练。这种方法可以恢复到上一次迭代的结果。

加完这些代码之后，就可以将上面的代码放在一个文件里，通过注释的方式，按照步骤一步步往下进行了（每次只打开一个模型训练的代码）。

## 10.8.3 小心分布训练中的“坑”

如果读者是使用 Anaconda 在 Windows 下运行，那么这里有个“坑”需要填一下。当加上保存模型的功能之后，在一步一步训练过程中，如果运行两次读取模型则会报错。更奇怪的事情是，在保存模型过程中，如果你第一次运行一半停止了，第二次运行成功并且也生成了模型，但是当你从模型里载入时，会是错误的参数。

原因是，在 Anaconda 中的 py 程序默认都是在同一个图中运行的，即除非关掉 Anaconda，否则运行两次时，这两次的代码是在一个图中，那么会有什么影响呢？

在定义变量时，在同一个图中，同一句代码 tf.Variable 所生成的变量是不同的名字，例如在代码中添加打印信息，输出变量的名字：

```
# 权重
weights = {
 #网络1 784-256-784
 'h1': tf.Variable(tf.random_normal([n_input, n_hidden_1])),
 ......
}
print (weights['h1'].name)
```

执行上面的代码，生成如下信息：

Variable:0
Variable:14

这时会发现，运行两次 weights[h1]有了两个不同的名字，在内存里会有两套变量。所以当运行两次后保存模型时，其实是保存了两套，但是读取时只是读取了第一套。同理。当运行两次读取时，第一次的模型可以与变量对应上，第二次会新生成另外一套变量，并且模型里找对应关系，但因为找不到所以就报错了。

解决方法：只需要在变量定义之前加上下面一行代码。让所有的图环境重置，即可解决问题。

```
tf.reset_default_graph()
```

## 10.8.4 练习题

- 使用栈式自编码对 MNIST 数据集逐层训练压缩特征，直到压缩成二维数据（如 512、256、128、64、32、16、2），再将其图示出来。

## 10.9 变分自编码

前面所描述的自编码可以降维重构样本，在这个基础上我们来学习一个更强大的自编码网络。

## 10.9.1 什么是变分自编码

变分自编码学习的不再是样本的个体，而是学习样本的规律。这样训练出来的自编码不单具有重构样本的功能，还具有仿照样本的功能。

听起来这么强大的功能，到底是怎么做到的呢？下面我们来讲讲它的原理。

变分自编码，其实就是在编码过程中改变了样本的分布（“变分”可以理解为改变分布）。前面所说的“学习样本的规律”，具体指的就是样本的分布，假设我们知道样本的分布函数，就可以从这个函数中随便取一个样本，然后进行网络解码层前向传导，这样就可以生成一个新的样本。

为了得到这个样本的分布函数，模型训练的目的将不再是样本本身，而是通过加一个约束项，将网络生成一个服从于高斯分布的数据集，这样按照高斯分布里的均值和方差规则就可以任意取相关的数据，然后通过解码层还原成样本。

## 10.9.2 实例 82：使用变分自编码模拟生成 MNIST 数据

对于变分自编码，好多文献都给出了一堆晦涩难懂的公式，其实里面真正的公式只有一个——KL 离散度的计算。而它也属于成熟的式子，就跟交叉熵一样，直接拿来用就可以。

公式本来是语言的高度概括，而如果一篇文章全是公式没有语言就会令人难以理解，本书会通过代码加上语言描述，让这部分知识学习起来不会感觉晦涩难懂。

本例共分如下几个步骤，下面就来——操作。

### 实例描述

使用变分自编码模型进行模拟 MNIST 数据的生成。

### 1. 引入库，定义占位符

本例建立的网络与之前略有不同，编码器为两个全连接层，第一个全连接层由 784 个维度的输入变化 256 个维度的输出；第二个全连接层并列连接了两个输出网络（mean 与 log_var），每个网络都输出了两个维度的输出。然后将两个输出通过一个公式的计算，输入到以一个 2 节点为开始的解码部分，接着后面为两个全连接层的解码器，第一层由两个维度的输入到 256 个维度的输出，第二层由 256 个维度的输入到 784 个维度的输出，如图 10-17 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_397_0.png)

图 10-17 变分自编码器层次

具体的计算公式，后面会有详细介绍。

在下面的代码中与前面代码不同，引入了一个 scipy 库，在后面可视化时会用到。头文件引入之后，定义操作符 x 和 z。x 用于原始的图片输入，z 用于中间节点解码器的输入。

### 代码10-8 变分自编码

```
01 import numpy as np
02 import tensorflow as tf
03 import matplotlib.pyplot as plt
04 from scipy.stats import norm
05 
06 from tensorflow.examples.tutorials.mnist import input_data
07 mnist = input_data.read_data_sets("/data/")    #, one_hot=True)
08 
09 n_input = 784
10 n_hidden_1 = 256
11 n_hidden_2 = 2
12 
13 x = tf.placeholder(tf.float32, [None, n_input])
14 
15 zinput = tf.placeholder(tf.float32, [None, n_hidden_2])
```

zinput 是个占位符，在后面要通过它输入分布数据，用来生成模拟样本数据。

### 2. 定义学习参数

由于这次的网络结构不同，所以定义的参数也有变化， mean_w1 与 mean_b1 是生成 mean 的权重， log_sigma_w1 与 log_sigma_b1 是生成 log_sigma 的权重。

### 代码10-8 变分自编码（续）

```
weights = {
    'w1': tf.Variable(tf.truncated_normal([n_input, n_hidden_1],
                                              stddev=0.001)),
    'b1': tf.Variable(tf.zeros([n_hidden_1])),
    'mean_w1': tf.Variable(tf.truncated_normal([n_hidden_1, n_hidden_2],
                                               stddev=0.001)),
    'log_sigma_w1': tf.Variable(tf.truncated_normal([n_hidden_1, n_hidden_2],
                                                   stddev=0.001)),
    'w2': tf.Variable(tf.truncated_normal([n_hidden_2, n_hidden_1],
                                           stddev=0.001)),
    'b2': tf.Variable(tf.zeros([n_hidden_1])),
    'w3': tf.Variable(tf.truncated_normal([n_hidden_1, n_input],
                                           stddev=0.001)),
    'b3': tf.Variable(tf.zeros([n_input])),
    'mean_b1': tf.Variable(tf.zeros([n_hidden_2])),
    'log_sigma_b1': tf.Variable(tf.zeros([n_hidden_2]))
}
```

> 注意：这里初始化 w 的权重与以往不同，使用了很小的值（方差为 0.001 的 truncated_normal）。这里设置得非常小心，由于在算 KL 离散度时计算的是与标准高斯分布的距离，如果网络初始生成的模型均值方差都很大，那么与标准高斯分布的距离就会非常大，这样会导致模型训练不出来，生成 NAN 的情况。

### 3. 定义网络结构

按照图 10-17 的描述，网络节点可以按照以下代码来定义，在变分解码器中为训练的中间节点赋予了特殊的意义，让它们代表均值和方差，并将它们所代表的数据集向着标准高斯分布数据集靠近（也就是原始数据是样本，高斯分布数据是标签），然后可以使用 KL 离散度公式，来计算它所代表的集合与标准的高斯分布集合（均值是 0，方差为 1 的正态分布）间的距离，将这个距离当成误差差，让它最小化从而优化网络参数。

这里的方差节点不是真正意义的方差，是取了 log 之后的，所以会有 tf.exp(z_log_sigma_sq) 的变换，是取得方差的值，再通过 tf.sqrt 将其开平方得到标准差。用符合标准正态分布的一个数乘以标准差加上均值，就使这个数成为符合（z_mean,sigma）数据分布集合里的一个点（z_mean 是指网络生成均值，sigma 是指网络生成的 z_log_sigma_sq 变换后的值）。

注意：输出的值当成任意一个意义，并通过训练得到对应的关系。具体做法为：将具有代表该意义的值代入相应的公式（该公式要求必须能够支持反向传播），计算公式输出值与目标值的误差，并将误差放到优化器里，然后通过多次迭代的方式进行训练即可。

到此，完成了编码阶段。将原始数据编码输出 3 个值：

- 一个是表示该数据分布的均值。
- 一个是表示该数据分布的方差。
- 还有一个是得到了该数据分布中的一个实际的点 z。

注意：这里的变换对应的知识点是：假如一个符合高斯分布的数据集均值、标准差为（m, sigma），其里面的某个点，可以通过一个符合标准高斯分布（0，1）中的点 x，通过 m+x × sigma 的方式转化而成。但这是在一个假设背景下完成的，假设数据分布属于高斯分布。我们现在无法保证转换后的数据分布符合高斯分布，则可以通过测量输出值代表的数据集与标准高斯分布数据集之间的差距，利用神经网络来将其训练成符合高斯分布的数据集。

### 代码10-8 变分自编码（续）

```
40 hl=tf.nn.relu(tf.add(tf.matmul(x, weights['w1']), weights['b1']))
41 z_mean = tf.add(tf.matmul(hl, weights['mean_w1']), weights['mean_b1'])
42 z_log_sigma_sq = tf.add(tf.matmul(hl, weights['log_sigma_w1']), weights['log_sigma_b1'])
43
44 # 高斯分布样本
45 eps = tf.random_normal(tf.stack([tf.shape(hl)[0], n_hidden_2]), 0, 1, dtype = tf.float32)
46 z = tf.add(z_mean, tf.multiply(tf.sqrt(tf.exp(z_log_sigma_sq)), eps))
47 h2=tf.nn.relu( tf.matmul(z, weights['w2'])+ weights['b2'])
48 reconstruction = tf.matmul(h2, weights['w3'])+ weights['b3']
49
50 h2out=tf.nn.relu( tf.matmul(zinput, weights['w2'])+ weights['b2'])
51 reconstructionout = tf.matmul(h2out, weights['w3'])+ weights['b3']
```

得到了符合原数据集上的一个具体点 z 之后，就可以通过神经网络这个点 z 还原成原始数据 reconstruction 了。解码部分和前面一样，参照编码的网络逐层还原回去即可。

h2out 和 reconstructionout 两个节点不属于训练中的结构，是为了生成指定数据时用的。
