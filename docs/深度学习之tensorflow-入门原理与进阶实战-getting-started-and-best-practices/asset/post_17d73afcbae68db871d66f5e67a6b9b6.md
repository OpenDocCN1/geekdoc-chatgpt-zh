
## 4. 构建模型的反向传播

这一步和前面一样，需要定义损失函数的节点和优化算法的 OP，代码如下。

### 代码10-8 变分自编码（续）

```
52 # 计算重建loss
53 reconstr_loss = 0.5 * tf.reduce_sum(tf.pow(tf.subtract(reconstruction,
54 x), 2.0))
55 latent_loss = -0.5 * tf.reduce_sum(1 + z_log_sigma_sq
56 - tf.square(z_mean)
57 - tf.exp(z_log_sigma_sq), 1)
58 cost = tf.reduce_mean(reconstr_loss + latent_loss)
59 optimizer = tf.train.AdamOptimizer(learning_rate = 0.001).minimize(cost)
```

上面代码描述了网络的两个优化方向：

- 一个是比较生成的数据分布与标准高斯分布的距离，这里使用 KL 散度的公式(见 latent_loss)。
- 另一个是计算生成数据与原始数据间的损失，这里用的是平方差，也可以用交叉熵。

最后将两种损失值放在一起，通过 Adam 的随机梯度下降算法实现在训练中的优化参数。

## 5. 设置参数，进行训练

这一步与前面类似，设置训练参数，迭代 50 次，在 session 中每次循环取指定批次数据进行训练。

### 代码10-8 变分自编码（续）

```
60 training_epochs = 50
61 batch_size = 128
62 display_step = 3
63
64 with tf.Session() as sess:
65 sess.run(tf.global_variables_initializer())
66
67 for epoch in range(training_epochs):
68 avg_cost = 0.
69 total_batch = int(mnist.train.num_examples/batch_size)
70
71 # 遍历全部数据集
72 for i in range(total_batch):
73 batch_xs, batch_ys = mnist.train.next_batch(batch_size)#取数据
74
75 # 输入数据，运行优化器
76 _,c = sess.run([optimizer,cost], feed_dict={x: batch_xs})
77
78 # 显示训练中的详细信息
79 if epoch % display_step == 0:
80     print("Epoch:", '%04d' % (epoch + 1), "cost=", "{:.9f}".format(c))
81
82 print("完成!")
83
84 # 测试
85 print ("Result:", cost.eval({x: mnist.test.images}))
```

可视化部分这里不再详述，读者可以参考本书的配套代码，最终程序运行的结果输出如下，输出图片如图 10-18 所示。

Epoch: 0001 cost= 2766.966308594
Epoch: 0004 cost= 2503.895507812
Epoch: 0007 cost= 2177.547363281
Epoch: 0010 cost= 2221.667724609
Epoch: 0013 cost= 2110.643798828
Epoch: 0016 cost= 2103.255859375
Epoch: 0019 cost= 2258.502685547
Epoch: 0022 cost= 2231.131347656
Epoch: 0025 cost= 2092.596191406
Epoch: 0028 cost= 2018.563964844
Epoch: 0031 cost= 1993.950439453
Epoch: 0034 cost= 2091.635253906
Epoch: 0037 cost= 1992.461059570
Epoch: 0040 cost= 2018.574462891
Epoch: 0043 cost= 1992.727661133
Epoch: 0046 cost= 2056.166503906
Epoch: 0049 cost= 1939.133544922
完成!
Result: 156414.0

![](img/d841d305683d65f55b29f4e3f25e532c_401_0.png)

图 10-18 变分自编码结果

图 10-18 中，第一行代表原始的样本图片，第二行代表变分自编码重建后生成的图片。可以看到生成的数字中不再一味单纯地学习形状，而是通过数据分布的方式学习规则，对原有图片具有更清晰的修正功能。

仿照前面的可视化代码，将均值和方差代表的二维数据在直角坐标系中展现如下，如图 10-19 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_402_0.png)

## 图 10-19 变分自编码二维可视化

从图 10-19 中可以看出，MNIST 数据集中同一类样本的特征分布还是比较集中的，说明变分自编码也具有降维功能，也可以用它进行分类任务的数据降维预处理。

## 6. 高斯分布取样，生成模拟数据

为了进一步证实模型学到了数据分布的情况，这次在高斯分布抽样中取一些点，将其映射到模型中的的 z，然后通过解码部分还原成真实图片看看效果，代码如下。

> 注意：代码中 norm.pdf 函数的作用是从按照百分比由大到小排列后的标准高斯分布中取值。np.linspace(0.05, 0.95, n)的意思是，将整个高斯分布数据集从大到小排列，取出前 0.05%到 0.95%区间，并且分成 n 份，每份对应的点的具体数值。
> norm 代表标准高斯分布，ppf 代表累积分布函数的反函数。累积分布的意思是，在一个集合里所有小于 a 的值出现的概率的和。例如，x = ppf(0.05) 就代表在集合里有个 x，集合中每个小于 x 的数在集合里出现的概率的总和等于 0.05。

### 代码10-8 变分自编码（续）

```
86    n = 15    # 15×15 的 figure
87    digit_size = 28
88    figure = np.zeros((digit_size * n, digit_size * n))
89    grid_x = norm.ppf(np.linspace(0.05, 0.95, n))
90    grid_y = norm.ppf(np.linspace(0.05, 0.95, n))
91
92    for i, yi in enumerate(grid_x):
93        for j, xi in enumerate(grid_y):
94            z_sample = np.array([[xi, yi]])
95            x_decoded = sess.run(reconstructionout, feed_dict={zinput:z_sample})
96
97 digit = x_decoded[0].reshape(digit_size, digit_size)
98 figure[i * digit_size: (i + 1) * digit_size,
99     j * digit_size: (j + 1) * digit_size] = digit
100
101 plt.figure(figsize=(10, 10))
102 plt.imshow(figure, cmap='Greys_r')
103 plt.show()
```

运行以上代码，生成图片如图 10-20 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_403_0.png)

## 图 10-20 变分自编码生成模拟数据

可以看到，在神经网络的世界里，从左下角到右上角显示了网络是按照图片的形状变化而排列的，并不像人类一样，把数字按照 1 到 9 的顺序排列，因为机器学习的只是图片，而人类对数字的理解更多的是其代表的意思。

## 10.9.3 练习题

读者可以自己试试在初始化时将所有的 w 设为 0 和设为 truncated_normal，不指定 stddev 的效果，想想为什么？

## 10.10 条件变分自编码

前面的变分自编码是为了本书条件变分自编码器做铺垫的，在实际中条件变分自编码的应用更广泛一些，下面来介绍条件变分自编码器。

### 10.10.1 什么是条件变分自编码

变分自编码存在一个问题——虽然可以生成一个样本，但只能输出与输入图片相同类别的样本。虽然也可以随机从符合模型生成的高斯分布中取数据来还原成样本，但是这样的话我们并不知道生成的样本属于哪个类别。条件变分解码则可以解决这个问题，让网络按指定的类别生成样本。

在变分自编码的基础上，再去理解条件变分自编码会很容易。主要的改动是，在训练、测试时加入一个 one-hot 向量，用于表示标签向量。其实，就是给变分自编码网络加入了一个条件，让网络学习图片分布时加入了标签因素，这样可以按照标签的数值生成指定的图片。

### 10.10.2 实例 8-3: 使用标签指导变分自编码网络生成 MNIST 数据

了解完原理，再来介绍下具体做法。在编码阶段需要在输入端添加标签对应的特征，在解码阶段同样也需要将标签加入输入，这样，在解码的结果向原始的输入样本不断逼近，最终得到的模型将会把输入的标签特征当成 MNIST 数据的一部分，从而实现通过标签生成 MNIST 数据。

在输入端添加标签时，一般是通过一个全连接层的变换将得到的结果使用 concat 函数连接到原始输入的地方，在解码阶段也将标签作为样本输入，与高斯分布的随机值一并运算，生成模拟样本。条件变分自编码结构如图 10-21 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_404_0.png)

图 10-21 条件变分自编码结构

具体代码步骤如下。

### 实例描述

使用条件变分自编码模型，通过指定标签输入生成对应类别的 MNIST 模拟数据。

### 1. 添加标签占位符

在 “10-8 变分自编码.py” 代码基础上修改，添加占位符 y。

### 代码10-9 条件变分自编码

```
01 ......
02 y = tf.placeholder(tf.float32, [None, n_labels])
03 ......
```

### 2. 添加输入全连接权重

添加全连接层的权重'wlab1'与'blab1'，作为输入标签的特征转换。这里输入的标签也转换成 256 个输出，因为最终要连接到原始的图片全连接输出，所以到第二层全连接时，输入就变成了 256×2，因此也需要将 mean_w1 和 log_sigma_w1 的输入修改成 n_hidden_1×2。

### 代码10-9 条件变分自编码（续）

```
04 weights = {
05
06     'w1': tf.Variable(tf.truncated_normal([n_input, n_hidden_1],
07         stddev=0.001)),
08     'b1': tf.Variable(tf.zeros([n_hidden_1])),
09
10     'wlab1': tf.Variable(tf.truncated_normal([n_labels, n_hidden_1],
11         stddev=0.001)),
12     'blab1': tf.Variable(tf.zeros([n_hidden_1])),
13     'mean_w1': tf.Variable(tf.truncated_normal([n_hidden_1 * 2, n_hidden_2],
14         stddev=0.001)),
15     'log_sigma_w1': tf.Variable(tf.truncated_normal([n_hidden_1 * 2, n_hidden_2],
16         stddev=0.001)),
17 ......
```

同样，对于生成的 z 也要与 label 连接后输入加码器，所以 w2 的输入维度需要被改成 n_hidden_2+n_labels。

### 3. 修改模型，将标签输入接入编码

定义新节点 hlab1 为输入标签的输出，接着使用 concat 函数将它与原来的 h1 合并到一起，变成 hall1。此时，hall1 的 shape 为[batch_size,n_hidden_1×2]。接着，将合成好的 hall1 代替原来的 h1 输入 z_mean 与 z_log_sigma_sq 中。

### 代码10-9 条件变分自编码（续）

```
20 h1 = tf.nn.relu(tf.add(tf.matmul(x, weights['w1']), weights['b1']))
21
22 hlabel = tf.nn.relu(tf.add(tf.matmul(y, weights['wlab1']), weights['blab1']))
23
24 hall1 = tf.concat([h1, hlabel], 1) # 256*2
25
26 z_mean = tf.add(tf.matmul(hall1, weights['mean_w1']), weights['mean_b1'])
27 z_log_sigma_sq = tf.add(tf.matmul(hall1, weights['log_sigma_w1']), weights['log_sigma_b1'])
```

### 4. 修改模型将标签投入解码器

这一步里中间的 z 不用变化，在 z 之后同样连接上 y 的特征，一起输入到解码器中。

这里需要同时修改 reconstruction 和 reconstructionout 节点，一个用来训练，一个用来生成。

### 代码10-9 条件变分自编码（续）

```
28 ......
29 zall = tf.concat([z, y], 1)
30 h2 = tf.nn.relu(tf.matmul(zall, weights['w2']) + weights['b2'])
31 reconstruction = tf.matmul(h2, weights['w3']) + weights['b3']
32
33 zinputall = tf.concat([zinput, y], 1)
34 h2out = tf.nn.relu(tf.matmul(zinputall, weights['w2']) + weights['b2'])
35 reconstructionout = tf.matmul(h2out, weights['w3']) + weights['b3']
```

### 5. 修改 session 中的 feed 部分

优化器不用变化，直接修改 session 的 feed 部分即可，在 feed 中加入标签占位符及对应的数据。

### 代码10-9 条件变分自编码（续）

```
36 with tf.Session() as sess:
37 sess.run(tf.global_variables_initializer())
38
39 for epoch in range(training_epochs):
40 avg_cost = 0.
41 total_batch = int(mnist.train.num_examples/batch_size)
42
43 # 遍历全部数据集
44 for i in range(total_batch):
45 batch_xs, batch_ys = mnist.train.next_batch(batch_size)#取数据
46
47 # 输入数据，运行优化器
48 _,c = sess.run([optimizer,cost], feed_dict={x: batch_xs,
49 y: batch_ys})
50
51 # 显示训练中的详细信息
52 if epoch % display_step == 0:
53 print("Epoch:", '%04d' % (epoch + 1), "cost=", "{:.9f}".format(c))
54 print("完成!")
55 # 测试
56 print ("Result:", cost.eval({x: mnist.test.images, y: mnist.test.labels}))
```

### 6. 运行模型生成模拟数据

这一步是最有意思的部分了。随意生成一个高斯分布随机数，并通过指定的 one_hot 输入标签，就可以命令模型生成指定的 MNIST 图片数据了。

### 代码10-9 条件变分自编码（续）

```
......
# 根据 label 模拟生产图片可视化结果
show_num = 10
z_sample = np.random.randn(10, 2)
pred = sess.run(
    reconstructionout, feed_dict={zinput: z_sample, y: mnist.test.labels[:show_num]})
f, a = plt.subplots(2, 10, figsize=(10, 2))
for i in range(show_num):
    a[0][i].imshow(np.reshape(mnist.test.images[i], (28, 28)))
    a[1][i].imshow(np.reshape(pred[i], (28, 28)))
plt.draw()
```

上面代码取了 10 个测试样本数据，将样本数据的 label 随高斯分布值 z_sample 一起生成了模拟的 MNIST 数据。运行代码，生成如图 10-22 和图 10-23 所示的数据。

![](img/d841d305683d65f55b29f4e3f25e532c_407_0.png)

图 10-22 根据原数据生成模拟数据

![](img/d841d305683d65f55b29f4e3f25e532c_407_1.png)

图 10-23 根据标签生成模拟数据
