# 8.2 实例一：手写数字识别

本节通过手写数字识别这一案例来介绍卷积神经网络。

# 8.2.1 MNIST 手写数字识别数据集介绍

MNIST 数据集来自美国国家标准与技术研究所（National Institute of Standards and Technology, NIST）。该数据集中包含的训练集来自 250 个不同人手写的数字，其中 50% 来自高中学生，50% 来自人口普查局（Census Bureau）的工作人员。数据集是大量手写的从 0 到 9 的黑白数字图像，尺寸为 28×28（784）像素。

下面我们利用代码对 MNIST 数据集进行可视化。

首先导入一些必需的库，如 NumPy 支持数组与矩阵运算，Matplotlib 是一个 Python 的 2D 绘图库，其中的 pyplot 包封装了很多画图的函数，Keras 的 mnist 模块可以帮助我们下载并读取 MNIST 数据库，代码如图 8-4 所示。

```
import numpy as np
from matplotlib import pyplot as plt
from keras.datasets import mnist
Using TensorFlow backend.
```

图 8-4

下载 MNIST 后获取数据就很方便，只需保持互联网畅通即可。使用 Keras 的 mnist 模块下载 MNIST 数据集，调用 mnist.load_data() 方法，就可在第一次调用时自动下载 MNIST 数据集至用户的 ~/.keras/datasets 文件夹下，文件名是 mnist.npz，如图 8-5 所示。

```
(X_train, y_train), (X_test, y_test) = mnist.load_data()
Downloading data from https://s3.amazonaws.com/img-datasets/mnist.npz
11493376/11490434 [==================] - 4s 0us/step
```

图 8-5

下载的 MNIST 数据集已经被分为训练数据与测试数据，分别包含了输入 x（image 是单色的数字图像）和输出 y（labels 是数字图像的真实值数字）。训练数据和测试数据如图 8-6 所示，训练数据中包含 60000 个数据点，其中输入 x 是 60000 幅用 28×28=784 像素组成的图像，由于是灰度图，所以每个像素仅由一个数字表示；输出 y 是 60000 个数字，代表了每一幅图像对应的数字。而测试数据则包含 1000 个数据点。

我们将训练数据中的前 10 幅图像画出来，代码如图 8-7 所示。

```
print('X_train:',X_train.shape)
print('y_train:',y_train.shape)
print('X_test:',X_test.shape)
print('y_test:',y_test.shape)

X_train: (60000, 28, 28)
y_train: (60000,)
X_test: (10000, 28, 28)
y_test: (10000,)
```

图 8-6

```
fig,axes = plt.subplots(10,5,figsize=(8,8)) #新建一个包含50个子图的10行5列的画布
for i in range(10): #对数字每一个数字
    indice = np.where(y_train==i)[0] # 找到标签为数字i的图像下标
    for j in range(5): #输出前5张图像
        axes[i][j].imshow(X_train[indice[j]],cmap='gray_r')
        #将X_train_image的第i张图像放在第i个子图上，这里使用灰度图原，数字越大颜色越深
        axes[i][j].set_xticks([]) #隐藏图像的x轴刻度
        axes[i][j].set_yticks([]) #隐藏图像的y轴刻度
plt.tight_layout() #采用更紧凑美观的布局方式
plt.show() #显示图像
```

图 8-7