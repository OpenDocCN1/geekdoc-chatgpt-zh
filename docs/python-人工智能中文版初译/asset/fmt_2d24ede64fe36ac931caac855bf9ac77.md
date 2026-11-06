
# 使用感知器构建分类器
在本章中，我们将看到如何使用这种基本结构进行机器学习。在后面的章节中，我们将看到更复杂的示例以及神经网络的一些有趣应用。许多神经网络的核心，无论它们多么复杂，都利用了感知器的简单概念。这就是为什么对这个主题有一个透彻的了解很重要的原因。让我们看看如何使用 `NeuroLab` 构建基于感知器的分类器。

创建一个新的 `Python` 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt
import neurolab as nl 
```

从提供给您的文本文件 `data_perceptron.txt` 中加载输入数据。每行包含用空格分隔的数字，其中前两个数字是特征，最后一个数字是标签：

```
# Load input data
text = np.loadtxt('data_perceptron.txt') 
```

将文本分为数据点和标签：

```
# Separate datapoints and labels
data = text[:, :2]
labels = text[:, 2].reshape((text.shape[0], 1)) 
```

绘制数据点：

```
# Plot input data
plt.figure()
plt.scatter(data[:,0], data[:,1])
plt.xlabel('Dimension 1')
plt.ylabel('Dimension 2')
plt.title('Input data') 
```

定义每个大小可以采用的最大值和最小值：

```
# Define minimum and maximum values for each dimension
dim1_min, dim1_max, dim2_min, dim2_max = 0, 1, 0, 1 
```

由于数据分为两类，我们只需要一位即可代表输出。因此，输出层将包含单个神经元。

```
# Number of neurons in the output layer
num_output = labels.shape[1] 
```

我们有一个数据集，其中的数据点是二维的。让我们定义一个具有两个输入神经元的感知器，在其中为每个维度分配一个神经元。

```
# Define a perceptron with 2 input neurons (because we 
# have 2 dimensions in the input data)
dim1 = [dim1_min, dim1_max]
dim2 = [dim2_min, dim2_max]
perceptron = nl.net.newp([dim1, dim2], num_output) 
```

用训练数据训练感知器：

```
# Train the perceptron using the data
error_progress = perceptron.train(data, labels, epochs=100, show=20, lr=0.03) 
```

使用误差度量绘制训练进度：

```
# Plot the training progress
plt.figure()
plt.plot(error_progress)
plt.xlabel('Number of epochs')
plt.ylabel('Training error')
plt.title('Training error progress')
plt.grid()
plt.show() 
```

文件 `perceptron_classifier.py` 中提供了完整代码。如果运行代码，您将获得两个输出屏幕截图。第一个屏幕截图显示了输入数据点：

![](Images/d56f4c57250f4aeda1d4d50b80b24f0b.png)

图 2：训练进度图

第二张屏幕截图使用误差指标表示训练进度：

![](Images/55265ceab9e449d2bdf6cfa6d33a7af3.png)

图 3：训练误差图

我们可以从前面的屏幕截图中观察到，在第四个周期结束时，误差降至 `0`，这正是我们想要发生的情况。如果误差为 `0`，则无法进一步改善。在下一部分中，我们将增强模型并创建单层神经网络。

## 构建单层神经网络
建立一个带有几个感知器的模型是一个好的开始，它使我们对这个令人兴奋的概念有了基本的了解，但是要真正解决问题，这种简单的模型是不够的。人脑大约有 850 亿个神经元。我们不会建立具有这么多节点的神经网络，但是这个数字使您了解解决复杂问题所需的方法。在建立具有数十亿个节点的模型之前，让我们进行下一步以建立具有单层的网络。这个单层神经网络由作用于输入数据以产生输出的独立神经元组成。让我们开始吧。

创建一个新的 `Python` 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt
import neurolab as nl 
```

我们将使用提供给您的文件 `data_simple_nn.txt` 中的输入数据。该文件中的每一行都包含四个数字。前两个数字构成数据点，后两个数字是标签。为什么我们需要为标签分配两个数字？因为我们在数据集中有四个不同的类，所以我们需要两位来表示它们。让我们继续加载数据：

```
# Load input data
text = np.loadtxt('data_simple_nn.txt') 
```

将数据分为数据点和标签：

```
# Separate it into datapoints and labels
data = text[:, 0:2]
labels = text[:, 2:] 
```

绘制输入数据：

```
# Plot input data
plt.figure()
plt.scatter(data[:,0], data[:,1])
plt.xlabel('Dimension 1')
plt.ylabel('Dimension 2')
plt.title('Input data') 
```

提取每个维度的最小值和最大值（我们不需要像上一节中那样对其进行硬编码）：

```
# Minimum and maximum values for each dimension
dim1_min, dim1_max = data[:,0].min(), data[:,0].max()
dim2_min, dim2_max = data[:,1].min(), data[:,1].max() 
```

定义输出层中的神经元数量：

```
# Define the number of neurons in the output layer
num_output = labels.shape[1] 
```

使用以上参数定义单层神经网络：

```
# Define a single-layer neural network
dim1 = [dim1_min, dim1_max]
dim2 = [dim2_min, dim2_max]
nn = nl.net.newp([dim1, dim2], num_output) 
```

使用训练数据训练神经网络：

```
# Train the neural network
error_progress = nn.train(data, labels, epochs=100, show=20, lr=0.03) 
```

绘制训练进度：

```
# Plot the training progress
plt.figure()
plt.plot(error_progress)
plt.xlabel('Number of epochs')
plt.ylabel('Training error')
plt.title('Training error progress')
plt.grid()
plt.show() 
```

定义一些样本测试数据点，并在这些点上运行网络：

```
# Run the classifier on test datapoints
print('\nTest results:')
data_test = [[0.4, 4.3], [4.4, 0.6], [4.7, 8.1]]
for item in data_test:
    print(item, '-->', nn.sim([item])[0]) 
```

完整代码在文件 `simple_neural_network.py` 中给出。如果运行代码，您将获得两个屏幕截图。第一个屏幕截图表示输入数据点：

![](Images/941f3f7437cc2a10125d1a21a25ddc90.png)

图 4：数据点图

第二张屏幕截图显示了训练进度：

![](Images/a6c702daf163cb529b09471a973e4da3.png)

图 5：训练进度图

关闭图形后，您将看到以下输出：

![](Images/ca7e60421c07431b95c3e8aa136170fb.png)

图 6：训练周期

正如我们在“图 5”中看到的那样，误差迅速开始减少，这表明我们的训练有效地创造了越来越好的预测。在这种情况下，误差不会降为零。但是如果我们让模型再运行几个周期，我们预计误差将继续减少。如果将这些测试数据点定位在 2D 图形上，则可以直观地验证预测输出的正确性。

## 构建多层神经网络
因此，我们将模型从几个节点增强到了一个单层，但距离 850 亿个节点还差得很远。我们也不会在本节中谈到这一点，但让我们朝着正确的方向迈出又一步。人脑不使用单层模型。一些神经元的输出变成其他神经元的输入，依此类推。具有这种特征的模型被称为多层神经网络。这种类型的架构可产生更高的精度，并且使我们能够解决更复杂，更多样化的问题。让我们看看如何使用 `NeuroLab` 构建多层神经网络。

创建一个新的 `Python` 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt
import neurolab as nl 
```

在前两节中，我们看到了如何使用神经网络作为分类器。在本节中，我们将看到如何使用多层神经网络作为回归器。根据公式 `y = 3x ^ 2 + 5` 生成一些样本数据点，然后将这些点归一化：

```
# Generate some training data
min_val = -15
max_val = 15
num_points = 130
x = np.linspace(min_val, max_val, num_points)
y = 3 * np.square(x) + 5
y /= np.linalg.norm(y) 
```

重塑前面的变量以创建训练数据集：

```
# Create data and labels
data = x.reshape(num_points, 1)
labels = y.reshape(num_points, 1) 
```

绘制输入数据：

```
# Plot input data
plt.figure()
plt.scatter(data, labels)
plt.xlabel('Dimension 1')
plt.ylabel('Dimension 2')
plt.title('Input data') 
```

定义具有两个隐藏层的多层神经网络。您可以根据需要自由设计神经网络。对于这种情况，我们在第一层使用 `10` 神经元，在第二层使用 `6` 神经元。我们的任务是预测值，因此输出层将包含单个神经元：

```
# Define a multilayer neural network with 2 hidden layers;
# First hidden layer consists of 10 neurons
# Second hidden layer consists of 6 neurons
# Output layer consists of 1 neuron
nn = nl.net.newff([[min_val, max_val]], [10, 6, 1]) 
```

将训练算法设置为梯度下降：

```
# Set the training algorithm to gradient descent
nn.trainf = nl.train.train_gd 
```

使用生成的训练数据训练神经网络：

```
# Train the neural network
error_progress = nn.train(data, labels, epochs=2000, show=100, goal=0.01) 
```

在训练数据点上运行神经网络：

```
# Run the neural network on training datapoints
output = nn.sim(data)
y_pred = output.reshape(num_points) 
```

绘制训练进度：

```
# Plot training error
plt.figure() 
plt.plot(error_progress)
plt.xlabel('Number of epochs')
plt.ylabel('Error')
plt.title('Training error progress') 
```

绘制预测输出：

```
# Plot the output
x_dense = np.linspace(min_val, max_val, num_points * 2)
y_dense_pred = nn.sim(x_dense.reshape(x_dense.size,1)).reshape(x_dense.size)
plt.figure()
plt.plot(x_dense, y_dense_pred, '-', x, y, '.', x, y_pred, 'p')
plt.title('Actual vs predicted')
plt.show() 
```

完整代码在文件 `multilayer_neural_network.py` 中给出。如果运行代码，您将获得三个屏幕截图。第一个屏幕截图显示了输入数据：

![](Images/d3409c3af8d00c4dd47380349769896a.png)

图 7：输入数据图

第二张屏幕截图显示了训练进度：

![](Images/a48e8b591399ed2525b6b079aa0e1bc5.png)

图 8：训练进度图

第三个屏幕截图显示了覆盖在输入数据之上的预测输出：

![](Images/4d6f17d74415f10c9bbce33234d6940c.png)

图 9：覆盖输入数据的输出图

预测的输出似乎在一定程度上接近实际输入。如果继续训练网络并减少误差，您将看到预测输出将与输入曲线相匹配，即使重新仍很准确。

您还应该看到打印出以下内容：

![](Images/8b872a6632c79d552d4c3b6c630c5011.png)

图 10：训练周期

在前面的部分中，我们学习了如何构建基本的神经网络以及对基础知识的牢固掌握和理解。在下一节中，我们将继续学习如何构建神经网络。现在，我们将学习如何使用向量量化器构建神经网络。

## 建立向量量化器
`向量量化`是一种量化技术，其中输入数据由固定数量的代表点表示。它是 `N` 维的四舍五入数字。此技术通常用于多个领域，例如语音/图像识别，语义分析和图像/语音压缩。最佳向量量化理论的历史可以追溯到 1950 年代的贝尔实验室，在那里进行了研究以使用离散化程序优化信号传输。向量量化器神经网络的一个优点是它们具有很高的解释性。让我们看看如何构建向量。

由于当前版本的 `NeuroLab`（v0.3.5）的某些问题，运行以下代码将引发错误。幸运的是，有了此修复程序，但其中涉及对 `NeuroLab` 包进行更改。将 `NeuroLab` 包（`layer_out.np['w'][n][st:i].fill(1.0)`）中 `net.py` 文件的 **179 行**更改为 `layer_out.np['w'][n][int(st):int(i)].fill(1.0))` 应该可以解决此问题。要求读者使用此替代方法，直到在 `Neuro Neuro` 官方包中实现修复为止。

创建一个新的 `Python` 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt
import neurolab as nl 
```

从文件 `data_vector_quantization.txt` 加载输入数据。该文件中的每一行都包含六个数字。前两个数字形成数据点，后四个数字形成单热编码标签。总体上有四个类别。

```
# Load input data
text = np.loadtxt('data_vector_quantization.txt') 
```

将文本分为数据和标签：

```
# Separate it into data and labels
data = text[:, 0:2]
labels = text[:, 2:] 
```

定义一个具有两层的神经网络，其中输入层有 `10` 神经元，输出层有 `4` 神经元：

```
# Define a neural network with 2 layers:
# 10 neurons in input layer and 4 neurons in output layer 
num_input_neurons = 10
num_output_neurons = 4
weights = [1/num_output_neurons] * num_output_neurons
nn = nl.net.newlvq(nl.tool.minmax(data), num_input_neurons, weights) 
```

使用训练数据训练神经网络：

```
# Train the neural network
_ = nn.train(data, labels, epochs=500, goal=-1) 
```

为了可视化输出集群，让我们创建一个点网格：
