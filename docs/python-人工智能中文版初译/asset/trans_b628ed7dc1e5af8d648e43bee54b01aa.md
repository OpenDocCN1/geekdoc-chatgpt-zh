

# 线性回归训练

```
# Define the gradient descent optimizer
optimizer = tf.train.GradientDescentOptimizer(0.5)
train = optimizer.minimize(loss) 
```

所有变量均已就位，但尚未初始化。 让我们这样做：

```
# Initialize all the variables
init = tf.initialize_all_variables() 
```

启动 `TensorFlow` 会话并使用初始化器运行它：

```
# Start the tensorflow session and run it
sess = tf.Session()
sess.run(init) 
```

开始训练过程：

```
# Start iterating
num_iterations = 10
for step in range(num_iterations):
    # Run the session
    sess.run(train) 
```

打印训练过程的进度。 随着迭代的进行，`loss`参数将继续减小：

```
 # Print the progress
    print('\nITERATION', step+1)
    print('W =', sess.run(W)[0])
    print('b =', sess.run(b)[0])
    print('loss =', sess.run(loss)) 
```

绘制生成的数据并将预测模型覆盖在顶部。 在这种情况下，模型是一行：

```
 # Plot the input data 
    plt.plot(x_data, y_data, 'ro')
    # Plot the predicted output line
    plt.plot(x_data, sess.run(W) * x_data + sess.run(b)) 
```

设置图的参数：

```
 # Set plotting parameters
    plt.xlabel('Dimension 0')
    plt.ylabel('Dimension 1')
    plt.title('Iteration ' + str(step+1) + ' of ' + str(num_iterations))
    plt.show() 
```

完整代码在文件`linear_regression.py`中给出。 如果运行代码，则应该看到以下屏幕截图：

![](Images/2f93ed2baaa3d4f96b34ef3aeae2b2ad.png)

图 5：输入数据图

如果关闭此窗口，您将看到训练过程。 第一次迭代如下所示：

![](Images/6f47115ac47d07055ae528055bfd8a02.png)

图 6：训练过程的第一次迭代图

如我们所见，行完全关闭。 关闭此窗口以转到下一个迭代：

![](Images/5a9ae8059627c6df93e0764191ff4739.png)

图 7：训练过程的后续迭代图

行似乎更好，但是仍然关闭。 让我们关闭此窗口，然后继续迭代：

![](Images/ab6e06e3b3e3828cce54d5769563fdbc.png)

图 8：训练过程的另一个后续迭代的图

看起来行越来越接近实际模型。 如果继续这样迭代，模型将变得更好。 第八次迭代如下所示：

![](Images/dfeece8fb387abb867b57ef5469e947a.png)

图 9：训练过程的第八次迭代图

该行似乎非常适合该数据。 然后，您应该将其打印出来：

![](Images/f3c391b94fe68448b530ab66a32b3c36.png)

图 10：周期的初始输出

完成训练后，您将看到以下内容：

![](Images/12d33040d3717ccd27fe7c26fc12a46a.png)

图 11：周期的最终输出

我们可以看到`w`和`b`的值是如何不断调整的，我们还可以看到损失如何持续减小直到损失很小的程度，以至于我们不再看到它减小。 有趣的是，我们能够很快取得良好的结果，但是我们为我们的网络解决了一个相当简单的问题。 让我们开始吧。

## 使用单层神经网络构建图像分类器

让我们看看如何使用 `TensorFlow` 创建单层神经网络，并使用它来构建图像分类器。 我们将使用 `MNIST` 图像数据集来构建我们的系统。 它是一个包含手写数字图像的数据集。 我们的目标是建立一个可以正确识别每个图像中数字的分类器。

创建一个新的 Python 文件并导入以下包：

```
import tensorflow as tf
from tensorflow.examples.tutorials.mnist import input_data 
```

提取 MNIST 图像数据。 `one_hot`标志指定我们将在标签中使用单热编码。 这表示，如果我们有`n`类，则给定数据点的标签将是长度为`n`的数组。 该数组中的每个元素对应于一个给定的类。 要指定一个类，相应索引处的值将设置为`1`，其他所有值都将为`0`：

```
# Get the MNIST data
mnist = input_data.read_data_sets("./mnist_data", one_hot=True) 
```

数据库中的图像是`28x28`。 我们需要将其转换为一维数组以创建输入层：

```
# The images are 28x28, so create the input layer
# with 784 neurons (28x28=784)
x = tf.placeholder(tf.float32, [None, 784]) 
```

创建具有权重和偏差的单层神经网络。 数据库中有 10 个不同的数字。 输入层中神经元的数量为`784`，输出层中神经元的数量为`10`：

```
# Create a layer with weights and biases.
# There are 10 distinct 
# digits, so the output layer should have 10 classes
W = tf.Variable(tf.zeros([784, 10]))
b = tf.Variable(tf.zeros([10])) 
```

创建用于训练的方程式：

```
# Create the equation for 'y' using y = W*x + b
y = tf.matmul(x, W) + b 
```

定义损失函数和梯度下降优化器：

```
# Define the entropy loss and the gradient descent optimizer
y_loss = tf.placeholder(tf.float32, [None, 10])
loss = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(logits=y, labels=y_loss))
optimizer = tf.train.GradientDescentOptimizer(0.5).minimize(loss) 
```

初始化所有变量：

```
# Initialize all the variables
init = tf.initialize_all_variables() 
```

创建一个 `TensorFlow` 会话并运行它：

```
# Create a session
session = tf.Session()
session.run(init) 
```

开始训练过程。 我们将使用批量进行训练，在该批量中，我们在当前批量上运行优化器，然后继续进行下一个批量以进行下一次迭代。 每次迭代的第一步是获取下一批要训练的图像：

```
# Start training
num_iterations = 1200
batch_size = 90
for _ in range(num_iterations):
    # Get the next batch of images
    x_batch, y_batch = mnist.train.next_batch(batch_size) 
```

在这批图像上运行优化器：

```
 # Train on this batch of images
    session.run(optimizer, feed_dict = {x: x_batch, y_loss: y_batch}) 
```

训练过程结束后，使用测试数据集计算准确率：

```
# Compute the accuracy using test data
predicted = tf.equal(tf.argmax(y, 1), tf.argmax(y_loss, 1))
accuracy = tf.reduce_mean(tf.cast(predicted, tf.float32))
print('\nAccuracy =', session.run(accuracy, feed_dict = {
        x: mnist.test.images, 
        y_loss: mnist.test.labels})) 
```

完整代码在文件`single_layer.py`中给出。 如果运行代码，它将把数据下载到当前文件夹中名为`mnist_data`的文件夹中。 这是默认选项。 如果要更改它，可以使用输入参数进行更改。 运行代码后，将及以下输出：

![](Images/c7af6804ec1f62a8aa40aa61474ae8b6.png)

图 12：精度输出

如输出所示，该模型的准确率为 92.1%。 这是一个相当低的分数。 让我们看看如何使用 CNN 来改善。

## 使用卷积神经网络构建图像分类器

上一节部分中的图像分类器效果不佳。 在 `MNIST` 数据集上获得 92.1% 相对容易。 让我们看看如何使用 `CNN` 来获得更高的准确率。 我们将使用相同的数据集构建图像分类器，但使用 `CNN` 而不是单层神经网络。

创建一个新的 Python 文件并导入以下包：

```
import argparse
import tensorflow as tf
from tensorflow.examples.tutorials.mnist import input_data 
```

定义一个函数来为每个层中的权重创建值：

```
def get_weights(shape):
    data = tf.truncated_normal(shape, stddev=0.1)
    return tf.Variable(data) 
```

定义一个函数来为每个层中的偏差创建值：

```
def get_biases(shape):
    data = tf.constant(0.1, shape=shape)
    return tf.Variable(data) 
```

定义一个函数，根据输入形状创建一个层：

```
def create_layer(shape):
    # Get the weights and biases
    W = get_weights(shape)
    b = get_biases([shape[-1]])
    return W, b 
```

定义函数以执行 2D 卷积：

```
def convolution_2d(x, W):
    return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], 
            padding='SAME') 
```

定义一个函数来执行`2×2`最大池化操作：

```
def max_pooling(x):
    return tf.nn.max_pool(x, ksize=[1, 2, 2, 1], 
            strides=[1, 2, 2, 1], padding='SAME') 
```

提取 MNIST 图像数据：

```
# Get the MNIST data
mnist = input_data.read_data_sets(args.input_dir, one_hot=True) 
```

使用`784`神经元创建输入层：

```
# The images are 28x28, so create the input layer 
# with 784 neurons (28x28=784)
x = tf.placeholder(tf.float32, [None, 784]) 
```

我们将使用利用图像 2D 结构的卷积神经网络。 因此，让我们将`x`重塑为 4D 张量，其中第二维和第三维指定图像大小：

```
# Reshape 'x' into a 4D tensor
x_image = tf.reshape(x, [-1, 28, 28, 1]) 
```

创建第一个卷积层，它将为图像中的每个`5×5`面片提取`32`特征：

```
# Define the first convolutional layer 
W_conv1, b_conv1 = create_layer([5, 5, 1, 32]) 
```

将图像与上一步中计算的权重张量进行卷积，然后向其添加偏差张量。 然后，我们需要将`ReLU`函数应用到输出：

```
# Convolve the image with weight tensor, add the 
# bias, and then apply the ReLU function
h_conv1 = tf.nn.relu(convolution_2d(x_image, W_conv1) + b_conv1) 
```

将`2×2`最大池化运算符应用于上一步的输出：

```
# Apply the max pooling operator
h_pool1 = max_pooling(h_conv1) 
```

创建第二个卷积层以为每个`5×5`补丁计算`64`特征：

```
# Define the second convolutional layer 
W_conv2, b_conv2 = create_layer([5, 5, 32, 64]) 
```

将上一层的输出与在上一步中计算的权重张量进行卷积，然后向其添加偏差张量。 然后，我们需要将 `ReLU` 函数应用于输出：

```
# Convolve the output of previous layer with the 
# weight tensor, add the bias, and then apply
# the ReLU function
h_conv2 = tf.nn.relu(convolution_2d(h_pool1, W_conv2) + b_conv2) 
```

将`2×2`最大池化运算符应用于上一步的输出：

```
# Apply the max pooling operator
h_pool2 = max_pooling(h_conv2) 
```

现在将图像大小减小为`7×7`。 用`1024`神经元创建一个完全连接的层：

```
# Define the fully connected layer
W_fc1, b_fc1 = create_layer([7 * 7 * 64, 1024]) 
```

重塑上一层的输出：

```
# Reshape the output of the previous layer
h_pool2_flat = tf.reshape(h_pool2, [-1, 7`7`64]) 
```

将上一层的输出与完全连接的层的权重张量相乘，然后向其添加偏置张量。 然后，我们将 `ReLU` 函数应用于输出：

```
# Multiply the output of previous layer by the 
# weight tensor, add the bias, and then apply 
# the ReLU function
h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_fc1) + b_fc1) 
```

为了减少过拟合，我们需要创建一个丢弃层。 让我们为概率值创建一个 `TensorFlow` 占位符，该占位符指定在删除过程中保留神经元输出的概率：

```
# Define the dropout layer using a probability placeholder 
# for all the neurons
keep_prob = tf.placeholder(tf.float32)
h_fc1_drop = tf.nn.dropout(h_fc1, keep_prob) 
```

使用与我们数据集中的 10 个类别相对应的`10`输出神经元定义读出层。 计算输出：

```
# Define the readout layer (output layer)
W_fc2, b_fc2 = create_layer([1024, 10])
y_conv = tf.matmul(h_fc1_drop, W_fc2) + b_fc2 
```

定义`loss`函数和`optimizer`函数：

```
# Define the entropy loss and the optimizer
y_loss = tf.placeholder(tf.float32, [None, 10])
loss = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(y_conv, y_loss))
optimizer = tf.train.AdamOptimizer(1e-4).minimize(loss) 
```

定义精度的计算方法：
