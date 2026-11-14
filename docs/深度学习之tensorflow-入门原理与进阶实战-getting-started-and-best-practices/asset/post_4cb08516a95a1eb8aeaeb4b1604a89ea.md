
```
'decoder_h1': tf.Variable(tf.random_normal([n_hidden_4, n_hidden_3])),
'decoder_h2': tf.Variable(tf.random_normal([n_hidden_3, n_hidden_2])),
'decoder_h3': tf.Variable(tf.random_normal([n_hidden_2, n_hidden_1])),
'decoder_h4': tf.Variable(tf.random_normal([n_hidden_1, n_input])),
}

biases = {
'encoder_b1': tf.Variable(tf.zeros([n_hidden_1])),
'encoder_b2': tf.Variable(tf.zeros([n_hidden_2])),
'encoder_b3': tf.Variable(tf.zeros([n_hidden_3])),
'encoder_b4': tf.Variable(tf.zeros([n_hidden_4])),

'decoder_b1': tf.Variable(tf.zeros([n_hidden_3])),
'decoder_b2': tf.Variable(tf.zeros([n_hidden_2])),
'decoder_b3': tf.Variable(tf.zeros([n_hidden_1])),
'decoder_b4': tf.Variable(tf.zeros([n_input])),
}
```

## 2. 定义网络模型

下面的代码是定义编码和解码的网络结构，这里使用了线性解码器。在编码的最后一层，没有进行 Sigmoid 变换，这是因为生成的二维数据其数据特征已经变得极为主要，所以我们希望让它传递到解码器中，少一些变换可以最大化地保存原有的主要特征。当然，这一切也是通过分析之后实际测试得来的结果。

## 代码10-2 自编码进阶（续）

```
def encoder(x):
layer_1 = tf.nn.sigmoid(tf.add(tf.matmul(x, weights['encoder_h1']),
biases['encoder_b1']))
layer_2 = tf.nn.sigmoid(tf.add(tf.matmul(layer_1, weights['encoder_h2']),
biases['encoder_b2']))
layer_3 = tf.nn.sigmoid(tf.add(tf.matmul(layer_2, weights['encoder_h3']),
biases['encoder_b3']))
layer_4 = tf.add(tf.matmul(layer_3, weights['encoder_h4']),
biases['encoder_b4'])
return layer_4

def decoder(x):
layer_1 = tf.nn.sigmoid(tf.add(tf.matmul(x, weights['decoder_h1']),
biases['decoder_b1']))
layer_2 = tf.nn.sigmoid(tf.add(tf.matmul(layer_1, weights['decoder_h2']),
biases['decoder_b2']))
layer_3 = tf.nn.sigmoid(tf.add(tf.matmul(layer_2, weights['decoder_h3']),
biases['decoder_b3']))
layer_4 = tf.nn.sigmoid(tf.add(tf.matmul(layer_3, weights['decoder_h4']), biases['decoder_b4']))
return layer_4
# 构建模型
encoder_op = encoder(x)
y_pred = decoder(encoder_op)         # 784 维度
cost = tf.reduce_mean(tf.pow(y - y_pred, 2))
optimizer = tf.train.AdamOptimizer(learning_rate).minimize(cost)
```

## 3. 开始训练

这一步中还是一次取 256 条数据，将全部数据集迭代 20 次。

## 代码10-2 自编码进阶（续）

```
#训练
training_epochs = 20      # 迭代训练 20 一次
batch_size = 256
display_step = 1

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    total_batch = int(mnist.train.num_examples/batch_size)
    # 启动循环开始训练
    for epoch in range(training_epochs):
        # 遍历全部数据集
        for i in range(total_batch):
            batch_xs, batch_ys = mnist.train.next_batch(batch_size)
            _, c = sess.run([optimizer, cost], feed_dict={x: batch_xs})
        # 显示训练中的详细信息
        if epoch % display_step == 0:
            print("Epoch:", '%04d' % (epoch+1),
                  "cost=", "{:.9f}".format(c))
    print("完成!")
```

输出结果如下：

```
Epoch: 0001 cost= 0.106694221
Epoch: 0002 cost= 0.096146211
Epoch: 0003 cost= 0.089687020
Epoch: 0004 cost= 0.085342437
Epoch: 0005 cost= 0.076942392
Epoch: 0006 cost= 0.077152036
Epoch: 0007 cost= 0.074504733
Epoch: 0008 cost= 0.071438089
Epoch: 0009 cost= 0.070937753
Epoch: 0010 cost= 0.067885153
Epoch: 0011 cost= 0.068935215
Epoch: 0012 cost= 0.067724347
Epoch: 0013 cost= 0.065405361
Epoch: 0014 cost= 0.069433592
Epoch: 0015 cost= 0.068582796
Epoch: 0016 cost= 0.067146875
Epoch: 0017 cost= 0.065363437
Epoch: 0018 cost= 0.066899218
Epoch: 0019 cost= 0.065677144
Epoch: 0020 cost= 0.064701408
完成!
```

可以看出，通过自编码网络将 784 维的数据压缩成了二维，用二维数据来代替 784 维，这就是自编码网络的神奇之处！

## 4. 对比输入和输出

同样我们再添加一些代码将效果显示出来。随意取出 10 张图片，并将图片输入模型中，得到输出图片。同时比对一下输入与输出的图片。

## 代码10-2 自编码进阶（续）

```
# 可视化结果
show_num = 10
encode_decode = sess.run(
    y_pred, feed_dict={x: mnist.test.images[:show_num]})
# 将自编码输出结果和原始样本显示出来
f, a = plt.subplots(2, 10, figsize=(10, 2))
for i in range(show_num):
    a[0][i].imshow(np.reshape(mnist.test.images[i], (28, 28)))
    a[1][i].imshow(np.reshape(encode_decode[i], (28, 28)))
plt.show()
```

执行上面的代码，生成如图 10-5 所示图片。

![](img/d841d305683d65f55b29f4e3f25e532c_374_0.png)

### 图 10-5 自编码进阶实例结果 1

## 5. 显示数据的二维特征

接着就是比较好玩的事情了。我们要把数据压缩后的二维特征显示出来。

## 代码10-2 自编码进阶（续）

```
aa = [np.argmax(l) for l in mnist.test.labels]#将 onehot 转成一般编码
encoder_result = sess.run(encoder_op, feed_dict={x: mnist.test.images})
plt.scatter(encoder_result[:, 0], encoder_result[:, 1], c=aa)
plt.colorbar()
plt.show()
```

执行上面的代码，生成如图 10-6 所示图片。

![](img/d841d305683d65f55b29f4e3f25e532c_375_0.png)

### 图 10-6 自编码进阶实例结果 2

这样看这是不是直观多了！看了这个图你会有什么感觉， 聚类？ K 均值？ softmax？ 没错，一般来讲用自编码网络将数据降维之后的数据更有利于进行分类处理。

注意：上面代码中的 aa 是将 mnist.test.labels 里面的 one_hot 转换成一般的数字，然后进行图像显示。这个代码建议读者把它收集起来，因为在深度学习过程中 one_hot 的互转会经常用到。另外，one_hot 转码有多种方法，细心的读者可以在前面章节中找到更简洁的转换代码。

当然也可以不用这句代码，那么在最前面引入 MNIST 时就必须把 onehot 关掉，将

```
mnist = input_data.read_data_sets("/data/", one_hot=True)
```

改成：

```
mnist = input_data.read_data_sets("/data/", one_hot=False)
```

同时将倒数第三句改成使用 mnist 的测试标签：

```
plt.scatter(encoder_result[:,0],encoder_result[:,1],c=mnist.test.labels)
```

## 10.3.4 实例 78: 实现卷积网络的自编码

自编码结构不仅只用在全连接网络上，还可用在卷积网络上。下面举例实现一个卷积网络的自编码。代码变化不大，是在原有的基础上将全连接改成卷积，具体改动步骤如下。

### 实例描述

在自编码网络中使用卷积网络完成 MNIST 的自编码功能。

### 1. 修改网络权重定义

保持 b 不变，将原来的 w 改成卷积核的定义如下。

```
#代码10-3 卷积网络自编码
......
#学习参数
weights = {
'encoder_conv1': tf.Variable(tf.truncated_normal([5, 5, 1, n_conv_1],stddev=0.1)),
'encoder_conv2': tf.Variable(tf.random_normal([3, 3, n_conv_1, n_conv_2],stddev=0.1)),
'decoder_conv1': tf.Variable(tf.random_normal([5, 5, 1, n_conv_1],stddev=0.1)),
'decoder_conv2': tf.Variable(tf.random_normal([3, 3, n_conv_1, n_conv_2],stddev=0.1))
}
......
```

### 2. 改变编码和解码结构

原来的编码器和解码器分别由全连接改成卷积和反卷积操作，通过外层的池化与反池化将整个网络贯穿起来。在网络入口处还要将输入的维度改成[1,28,28,1]。

```
#代码10-3 卷积网络自编码 (续)
x_image = tf.reshape(x, [-1,28,28,1])
# 编码
def encoder(x):
h_conv1 = tf.nn.relu(conv2d(x, weights['encoder_conv1']) + biases['encoder_conv1'])
h_conv2 = tf.nn.relu(conv2d(h_conv1, weights['encoder_conv2']) + biases['encoder_conv2'])
return h_conv2,h_conv1

# 解码
def decoder(x,conv1):
t_conv1 = tf.nn.conv2d_transpose(x-biases['decoder_conv2'], weights['decoder_conv2'], conv1.shape,[1,1,1,1])
t_x_image = tf.nn.conv2d_transpose(t_conv1-biases['decoder_conv1'], weights['decoder_conv1'], x_image.shape,[1,1,1,1])
return t_x_image

#输出的节点
encoder_out,conv1 = encoder(x_image)
h_pool2, mask = max_pool_with_argmax(encoder_out, 2)

h_upool = unpool(h_pool2, mask, 2)
pred = decoder(h_upool,conv1)
```

上面代码中用到的卷积函数和反池化函数，可以在 8.4 节和 8.6 节中查看具体实现过程。

### 3. 测试及可视化部分改动

因为反池化的函数要求输入图片的一个维度不能为 None，所以，需要把评估部分也改一下。

## 代码10-3 卷积网络自编码（续）

```
# 测试
batch_xs, batch_ys = mnist.train.next_batch(batch_size)
print ("Error:", cost.eval({x: batch_xs}))

# 可视化结果
show_num = 10
reconstruction = sess.run(
    pred, feed_dict={x: batch_xs})

f, a = plt.subplots(2, 10, figsize=(10, 2))
for i in range(show_num):
    a[0][i].imshow(np.reshape(batch_xs[i], (28, 28)))
    a[1][i].imshow(np.reshape(reconstruction[i], (28, 28)))
plt.draw()
```

运行上面的代码，可以看到如下信息，生成的图片如图 10-7 所示。

```
Epoch: 0001 cost = 0.026543943
Epoch: 0006 cost = 0.538754463
Epoch: 0011 cost = 0.006631755
Epoch: 0016 cost = 0.003391982
完成!
error: 0.0214768
```

![](img/d841d305683d65f55b29f4e3f25e532c_377_0.png)

### 图 10-7 卷积自编码网络实例

## 10.3.5 练习题

仿照 10.3.1 节的例子，试着建立一个更深层的自编码，分为 3 层将图片压缩成 512、256、128 维度，然后再将其还原（可以参考本书配套代码的代码“10-4 自编码练习题.py”文件）。

## 10.4 去噪自编码

要想取得好的特征只靠重构输入数据是不够的，在实际应用中，还需要让这些特征具有抗干扰的能力，即当输入数据发生一定程度的扰动时，生成的特征仍然保持不变。这时需要添加噪声来为模型增加更大的困难。在这种情况下训练出来的模型才会有更好的鲁棒性，于是就有了本节所介绍的去噪自动编码器。

去噪自动编码器（Denoising Autoencoder，DA），是在自动编码的基础上，训练数据加入噪声，输出的标签仍是原始的样本（没有加过噪声的），这样自动编码器必须学习去除噪声而获得真正的没有被噪声污染过的输入特征。因此，这就迫使编码器去学习输入信号的更加鲁棒的特征表达，即具有更加强悍的泛化能力。

在实际训练中，人为加入的噪声有两种途径：
（1）在选择训练数据集时，额外选择一些样本集以外的数据。
（2）改变已有的样本数据集中的数据（使样本个体不完整，或通过噪声与样本进行的加减乘除之类的运算，使样本数据发生变化）。

## 10.5 去噪自编码网络的代码实现

下面进入实例环节，通过例子来构建一个去噪自编码网络。

## 10.5.1 实例 79: 使用去噪自编码网络提取 MNIST 特征

本节做一个更简单的自编码模型，让 784 维只通过一层压缩成 256 维。与前面例子唯一不不同的是，将原始的数据进行一些变换，每个像素点都乘以一个高斯噪声，然后在输出的位置仍然使用原始的输入样本，这样迫使网络在提取特征的同时将噪声去掉。为了防止其过拟合，还需要在其中加入 Dropout 层。

在这个例子中分为如下几个步骤来编写代码。

### 实例描述

对 MNIST 集原始输入图片加入噪声，在自编码网络中进行训练，以得到抗干扰更强的特征提取模型。

- 1. 引入头文件，创建网络模型及定义学习参数变量

## 代码10-5 去噪声自编码

```
import numpy as np
import tensorflow as tf
```
