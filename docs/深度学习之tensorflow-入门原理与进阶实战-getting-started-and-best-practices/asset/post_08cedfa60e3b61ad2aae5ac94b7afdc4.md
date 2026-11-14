
19  print (image)
20  result=sess.run(pooling2)
21  print ("pooling2:\n",result)
22  result,mask2=sess.run([encode, mask])
23  print ("encode:\n",result,mask2)

代码运行后，输出如下：

```
image:
[[[ 0.  4.]
  [ 0.  4.]
  [ 0.  4.]
  [ 0.  4.]]

 [[ 1.  5.]
  [ 1.  5.]
  [ 1.  5.]
  [ 1.  5.]]

 [[ 2.  6.]
  [ 2.  6.]
  [ 2.  6.]
  [ 2.  6.]]

 [[ 3.  7.]
  [ 3.  7.]
  [ 3.  7.]
  [ 3.  7.]]]]
pooling2:
[[[ 1.  5.]

  [ 1.  5.]]

 [[ 3.  7.]
  [ 3.  7.]]]]
encode:
[[[ 1.  5.]
  [ 1.  5.]]

 [[ 3.  7.]
  [ 3.  7.]]]] [[[[ 8  9
 [12 13]]

 [[24 25]
 [28 29]]]]
```

可以看到，定义的最大池化与原来的版本输出是一样的。mask 的值是将整个数组 flat（扁平化）后的索引，但却保持与池化结果一致的 shape。

了解这些信息后，就可以接着写代码，定义一个反最大池化的操作了。

### 代码8-13 反池化操作（续）

```
24 def unpool(net, mask, stride):
25
26  ksize = [1, stride, stride, 1]
input_shape = net.get_shape().as_list()
# 计算new shape
output_shape = (input_shape[0], input_shape[1] * ksize[1], input_shape[2] * ksize[2], input_shape[3])
# 计算索引
one_like_mask = tf.ones_like(mask)
batch_range = tf.reshape(tf.range(output_shape[0], dtype=tf.int64), shape=[input_shape[0], 1, 1, 1])
b = one_like_mask * batch_range
y = mask // (output_shape[2] * output_shape[3])
x = mask % (output_shape[2] * output_shape[3]) // output_shape[3]
feature_range = tf.range(output_shape[3], dtype=tf.int64)
f = one_like_mask * feature_range
# 转置索引
updates_size = tf.size(net)
indices = tf.transpose(tf.reshape(tf.stack([b, y, x, f]), [4, updates_size]))
values = tf.reshape(net, [updates_size])
ret = tf.scatter_nd(indices, values, output_shape)
return ret
```

上面代码的大概思路是找到 mask 对应的索引，将 max 的值填到指定地方。这里不做过多解释，读者可以将反向传播函数当成一个工具，以后直接使用即可。

下面调用反池化函数，并将结果打印出来。

### 代码8-13 反池化操作（续）

```
img2 = unpool(encode,mask,2)
with tf.Session() as sess:
    ......
    print ("encode:\n",result,mask2)
    result=sess.run(img2)
    print ("result:\n",result)
```

代码运行后，输出结果如下：

```
result:
[[[ 0.  0.]
  [ 0.  0.]
  [ 0.  0.]
  [ 0.  0.]]]

[[ 1.  5.]
  [ 0.  0.]
  [ 1.  5.]
  [ 0.  0.]]]

[[ 0.  0.]
  [ 0.  0.]
  [ 0.  0.]
  [ 0.  0.]]]

[[ 3.  7.]
 [ 0.  0.]
[ 3. 7.]
[ 0. 0.]]]]
```

可以看到，最大值已经填入对应的位置，其他地方的值为 0。

## 8.6.6 实例 47：演示 gradients 基本用法

这部分内容本来是要放在前面章节讲的，考虑到读者直接了解梯度相关的知识有些生硬，而且一时也用不上，所以就将这部分内容移到了本节中，这样，通过例子中引出的知识点，会使读者学习起来更加通顺。

### 实例描述

通过定义两个矩阵变量相乘来演示使用 gradients 求梯度。

在反向传播过程中，神经网络需要对每一个 loss 对应的学习参数求偏导，算出的这个值也叫梯度，用来乘以学习率然后更新学习参数使用的。它是通过 tf.gradients 函数来实现的。tf.gradients 函数里第一个参数为求导公式的结果，第二个参数为指定公式中的哪个变量来求偏导。下面通过例子介绍 tf.gradients 函数的用法。

### 代码8-14 gradients0

```
import tensorflow as tf

w1 = tf.Variable([[1,2]])
w2 = tf.Variable([[3,4]])

y = tf.matmul(w1, [[9],[10]])
grads = tf.gradients(y,[w1])    # 求 w1 的梯度

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    gradval = sess.run(grads)
    print(gradval)
```

运行后输出结果如下：

[array([[ 9, 10]])]

上面例子中，由于 y 是由 w1 与[[9],[10]]相乘而来，所以其导数也就是[[9],[10]]（即斜率）。

⚠注意：如果求梯度的式子中没有要求偏导的变量，系统会报错。例如，写成 grads = tf.gradients(y,[w1,w2])。

## 8.6.7 实例 48：使用 gradients 对多个式子求多变量偏导

tf.gradients 函数还可以同时对多个式子求关于多个变量的偏导，见如下代码

### 实例描述

有两个 OP，4 个参数，演示使用 gradients 同时为两个式子 4 个参数求梯度。

### 代码8-15 gradients1

```
import tensorflow as tf

tf.reset_default_graph()
w1 = tf.get_variable('w1', shape=[2])
w2 = tf.get_variable('w2', shape=[2])

w3 = tf.get_variable('w3', shape=[2])
w4 = tf.get_variable('w4', shape=[2])

y1 = w1 + w2+ w3
y2 = w3 + w4
# grad_ys 求梯度的输入值
gradients = tf.gradients([y1,y2],[w1,w2,w3,w4],grad_ys=[tf.convert_to_tensor([1.,2.]),tf.convert_to_tensor([3.,4.])])

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print(sess.run(gradients))
```

运行代码，输出结果如下：

```
[array([1.,2.], dtype=float32), array([1.,2.], dtype=float32), array([ 4., 6.], dtype=float32), array([ 3., 4.], dtype=float32)]
```

这里使用了 tf.gradients 函数的第三个参数，即给定公式结果的值，来求参数偏导，这里相当于 y1 为[1.,2.]、y2 为[3.,4.]。对于 y1 来讲，求关于 w1 的偏导时，会认为 w2 和 w3 为常数，所以 w1 和 w2 的导数为 0，即 w1 的梯度就为[1.,2.]。同理可以得出 w2 和 w3 均为[1.,2.]，接着求 y2 的偏导数，得到 w3 与 w4 均为[3.,4.]。然后将两个式子中的 w3 结果累加起来，所以 w3 就为[4.,6.]。

## 8.6.8 实例 49: 演示梯度停止的实现

### 实例描述

演示梯度停止的用法，并观察当变量设置梯度停止后，对其求梯度的结果。

对于反向传播过程中某种特殊情况需要停止梯度的运算时，在 TensorFlow 中提供了一个 tf.stop_gradient 函数，被它定义过的节点将没有梯度运算功能。

例如，在前面代码中加入 y3 结点。通过 gradients2 来计算其相关变量的梯度。

### 代码8-16 gradients2

```
01 ......
02 a = w1+w2
03 a_stoped = tf.stop_gradient(a)      #令 a 梯度停止
04 y3= a_stoped+w3
05
06 gradients = tf.gradients([y1,y2],[w1,w2,w3,w4], grad_ys=[tf.convert_to_tensor([[1.,2.]]),
tf.convert_to_tensor([[3.,4.]]))
08
09 gradients2 = tf.gradients(y3,[w1,w2,w3], grad_ys=tf.convert_to_tensor([[1.,2.]]))
10 print(gradients2)
11
12 with tf.Session() as sess:
13 sess.run(tf.global_variables_initializer())
14 print(sess.run(gradients))
15 print(sess.run(gradients2))
```

运行代码，结果如下：

```
[None, None, <tf.Tensor 'gradients_1/add_4_grad/Reshape_1:0' shape=(2,) dtype=float32>
Traceback (most recent call last):
......
```

可以看到程序运行出错，并且在出错前显示了 gradients2 的内容，w1 和 w2 对应的位置都为 None，这是由于梯度被停止了。后面的程序试图去求一个 None 的梯度，所以报错了。

再定义一个 gradients3，只求存在的梯度，同时将 print(sess.run(gradients2))注释掉，代码如下。

### 代码8-16 gradients2 (续)

```
16 gradients3 = tf.gradients(y3, [ w3], grad_ys=tf.convert_to_tensor([[1.,2.]]))
17
18 with tf.Session() as sess:
19 sess.run(tf.global_variables_initializer())
20 print(sess.run(gradients))
21 #print(sess.run(gradients2)) #程序试图去求一个 None 的梯度，所以报错
22 print(sess.run(gradients3))
```

运行代码，输出结果如下：

```
[None, None, <tf.Tensor 'gradients_1/add_4_grad/Reshape_1:0' shape=(2,) dtype=float32>
[array([ 1., 2.], dtype=float32), array([ 1., 2.], dtype=float32),
array([ 4., 6.], dtype=float32), array([ 3., 4.], dtype=float32)]
[array([ 1., 2.], dtype=float32)]
```

这时就可以正常运算了。

## 8.7 实例 50: 用反卷积技术复原卷积网络各层图像

在了解了反卷积神经网络之后，下面通过一个例子将前面的卷积神经网络里的卷积层可视化出来，看看每一层到底学到了什么信息。

### 实例描述

将代码“8-9 cifar 卷积.py”文件中的每层卷积结果进行反卷积并输出，通过 tensorboard 观察其结果。

改写代码“8-9 cifar 卷积.py”，将每层的卷积内容可视化并在 TensorBoard 中查看，具体步骤如下。

- 1. 替换Maxpool池化函数

这里不再使用自己定义的 max_pool_2x2 池化函数，改成新加入的带 mask 返回值的 max_pool_with_argmax 函数，具体代码如下。

### 代码8-17 cifar反卷积

```
01 .....
02 x_image = tf.reshape(x, [-1,24,24,3])
03 
04 h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)
05 h_pool1, mask1 = max_pool_with_argmax(h_conv1, 2)
06 
07 W_conv2 = weight_variable([5, 5, 64, 64])
08 b_conv2 = bias_variable([64])
09 
10 h_conv2 = tf.nn.relu(conv2d(h_pool1, W_conv2) + b_conv2)
11 ##################################################################
12 h_pool2, mask = max_pool_with_argmax(h_conv2, 2)#(128, 6, 6, 64)
13 print(h_pool2.shape)
```

> 注意：上面代码的最后一行是将 h_pool2 的形状打印出来，这也是在组建网络结构时常用的一种调试方法。反卷积和反池化对形状都很敏感（尤其层数太多时），这种方法可以让我们不用花费太多精力来推导到底当前的输入是什么形状。

- 2. 反卷积第二层卷积结果

以第二池化输出的变量 h_pool2 为开始部分，沿着 h_pool2 生成的方式反向操作一层一层推导，直到生成原始图 t1_x_image。

如图 8-22 所示，上半部分是 h_pool2 卷积的过程，下半部分为反卷积过程。为了便于分析，下半部分的名字与代码中的变量一致。

![](img/d841d305683d65f55b29f4e3f25e532c_215_0.png)

### 图 8-22 反卷积例子

因为在卷积过程中，每个卷积后都要加上权重 b，所以在反卷积过程中就要将 b 减去。由于 Relu 函数基本上是恒等变化（除了小于 0 的部分），所以在反向时不需要可逆操作，可以直接略去。

### 代码8-17 cifar反卷积（续）

```
14 t_conv2 = unpool(h_pool2, mask, 2)#(128, 12, 12, 64)
15 t_pool1 = tf.nn.conv2d_transpose(t_conv2-b_conv2, W_conv2, h_pool1.shape,[1,1,1,1])#(128, 24, 24, 64)
16 print(t_conv2.shape,h_pool1.shape,t_pool1.shape)
17 t_conv1 = unpool(t_pool1, mask1, 2)
18 t_x_image = tf.nn.conv2d_transpose(t_conv1-b_conv1, W_conv1, x_image.shape,[1,1,1,1])
```

- 3. 反卷积第一层卷积结果

参考第二层的反卷积，第一层会更为简单，代码如下。

### 代码8-17 cifar反卷积（续）

```
19 #第一层卷积还原
20 t1_conv1 = unpool(h_pool1, mask1, 2)
21 t1_x_image = tf.nn.conv2d_transpose(t1_conv1-b_conv1, W_conv1, x_image.shape,[1,1,1,1])
```

- 4. 合并还原结果，并输出给TensorBoard输出

这次是将结果通过 TensorBoard 进行展示，所以将生成的第一层图片和第二层图片与原始图片合在一起，统一放入 tf.summary.image 里，这样在 TensorBoard 的 image 里就能看到了。

### 代码8-17 cifar反卷积（续）

```
22 # 生成最终图像
23 stitched_decodings = tf.concat((x_image, t1_x_image,t_x_image), axis=2)
24 decoding_summary_op = tf.summary.image('source/cifar', stitched_decodings)
```

- 5. session中写入log

按照前面介绍过的 TensorBoard 步骤，在 session 中建立一个 summary_writer，然后在代码结尾处通过 session.run 运行前面的 tf.summary.image 操作，使用 summary_writer 将得出的结果写入 log。

### 代码8-17 cifar反卷积（续）

```
......
cross_entropy = -tf.reduce_sum(y*tf.log(y_conv)) + (tf.nn.l2_loss(W_conv1)+tf.nn.l2_loss(W_conv2)+tf.nn.l2_loss(W_conv3))
train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)

correct_prediction = tf.equal(tf.argmax(y_conv,1), tf.argmax(y,1))
accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))
sess = tf.Session()
sess.run(tf.global_variables_initializer())
summary_writer = tf.summary.FileWriter('./log/', sess.graph)
......
decoding_summary = sess.run(decoding_summary_op, feed_dict={x:image_batch, y: label_b})
summary_writer.add_summary(decoding_summary)
```

这里在计算 cross_entropy 时，对所有的 w 权重用了一次 Loss2 正则化。

- 6. Tensorboard中查看结果

运行以上代码后，就可以在 TensorBoard 中查看结果了。

上面的 log 是写在本代码同级目录下的 log 文件夹内，启动 TensorBoard 的步骤可以参考前面的介绍，这里不再多讲（一定要把路径要找对）。

上面的代码中，image 定义的路径是 source/cifar，所以在 TensorBoard 中单击 image 就会看到 source，点开后就能看到如图 8-23 所示的图片。

![](img/d841d305683d65f55b29f4e3f25e532c_216_0.png)
