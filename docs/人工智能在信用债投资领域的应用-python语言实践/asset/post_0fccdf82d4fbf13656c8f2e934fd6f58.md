
```
iris=datasets.load_iris()
X=iris.data[:, :2]
y=iris.target
y_30=np.copy(y)
y_30[rng.rand(len(y))<0.3]=-1    # 去掉已有的标注(随机数小于 0.3 的索引)
y_50=np.copy(y)
y_50[rng.rand(len(y))<0.5]=-1    # 去掉已有的标注(随机数小于 0.5 的索引)
ls30=label_propagation.LabelSpreading(kernel='knn', alpha=0.8)
ls30.fit(X, y_30)                # 训练模型
ls50=label_propagation.LabelSpreading(kernel='knn', alpha=0.8)
ls50.fit(X, y_50)                # 训练模型
predicted_labels_ls30=ls30.transduction_    # 输出预测的标注
predicted_labels_ls50=ls50.transduction_    # 输出预测的标注
```

本节简单介绍了三类机器学习技术的基本原理，分别是监督机器学习、无监督机器学习和半监督机器学习，并给出了使用相关算法的简单示例。为了便于梳理、理解机器学习的技术体系，我们用图 2.5 所示的分类图给本节作一个总结。

![](img/bef5d57039b9e3819bf07153f102e5b8_21_0.png)

图 2.5 机器学习技术概述

## 2.2 深度学习

深度学习是机器学习的一个重要分支，其用到的核心技术是人工神经网络。当提到深度学习的概念时，很多初学者会问：一个机器学习模型中，使用几层神经网络才能视为深度学习模型呢？这个问题看似有道理，实际上并没有深入理解深度学习的基本原理，而是将深度学习看成神经网络层次的简单叠加。实际上，深度学习模型和一般神经网络模型相比在原理上有很大不同，这主要表现在特征提取和处理顺序与方式上。深度学习处理问题的顺序和方式跟人类思考问题的方式比较接近。例如，深度学习在图像分类问题中的一般流程是首先提取图片的基础特征——图片像素，然后将图片像素特征输入神经网络的第一层，并提取线条特征，紧接着将线条特征输入到神经网络的第二层，提取图片中物体的简单形状特征，然后将简单形状特征输入神经网络的第三层，提取图片中物体的复杂形状特征。然后再将提取的这些多层复杂特征进行权重学习，并将得到的模型用于图片分类预测。深度学习和传统机器学习方法处理问题的一般流程，如图 2.6 所示。

![](img/bef5d57039b9e3819bf07153f102e5b8_22_0.png)

图 2.6 深度学习和传统机器学习方法处理问题的一般流程

显然，图片分类问题深度学习的过程，和人类学习绘画的过程比较类似。幼儿学习绘画时，首先认识颜料，然后学习简笔画，最后学习复杂绘画并涂色。那么，什么是深度学习呢？简单地说，如果模型使用分层特征学习——首先识别较低级别的特征，然后建立在它们之上以识别更高级的特征（如通过卷积滤波器等），那么它就是一个深度学习模型。如果没有分层特征学习，那么无论你的模型有多少层，都不是真正的深度学习模型。即我们不是因为深度模型（多层神经网络模型）而将其称为深度学习，而是为了实现层次化学习，模型需要深度，深度是实现分层特征学习的副产品。因此，深度学习是使用多层神经网络技术来实现分层特征学习的一种算法。

为了实现深度学习模型中的分层特征学习，我们需要用到神经网络的模型结构，神经网络最基本的结构单元称神经元，这是由生物学引出一个概念。生物学中一个神经元通常具有多个树突，主要用来接收传入的信息；而轴突只有一条。轴突尾端有许多轴突末梢可以给其他多个神经元传递信息；轴突末梢和其他神经元的树突产生连接，从而传递信息。为了模拟生物神经元的结构和信息传递方式，人工神经网络中的神经元结构也基本类似，如图 2.7 所示。

![](img/bef5d57039b9e3819bf07153f102e5b8_23_0.png)

图 2.7 生物神经元与人工神经网络结构对比图

在如图 2.7 右侧所示的人工神经元中，输入为 x1, x2, …, xn，每个输入的权重为 w1, w2, …, wn，将这些输入的加权求和作为激活函数的输入，并计算后得到该神经元的输出，即该神经元的输出为 y = f(w1 × x1 + w2 × x2 + … + wn × xn)。显然，各输入节点的加权求和是一个线性表达式，为处理非线性的分类问题，激活函数通常选择非线性函数，这样训练得到的深度学习模型就可以处理非线性分类问题了。训练深度学习模型的过程，就是计算每个人工神经元输入的权重过程。

随着神经网络层次的增多和每个神经元激活函数的不同，训练得到的神经网络模型处理复杂问题的能力一般也会逐步提升，如图 2.8 所示。

![](img/bef5d57039b9e3819bf07153f102e5b8_23_1.png)

图 2.8 神经网络分类能力不断增强

此处，我们以一个具体的实例来说明深度神经网络的构建、训练和验证过程。为了进行深度学习，本书采用 Keras 深度学习库（关于 Keras 的安装、配置与用法请见本书第 3 章的详细介绍）。

```
#需要指出的是，该深度学习代码只能在 Linux 平台下并安装 Keras 深度学习库后，才能正常运行。详细的安装与配置方法，请见 3.2 节中的详细介绍。
#载入所需要的包
import pandas as pd
from keras.datasets import fashion_mnist
import numpy as np
from keras.utils import to_categorical
import matplotlib.pyplot as plt
%matplotlib inline
#获取测试数据，包括训练集和测试集
(train_X,train_Y),(test_X,test_Y)=fashion_mnist.load_data() #使用 Keras 内置函数下载数据
#由于数据量较大、运行时较长，读者也可以在开源平台 GitHub 自行下载数据集，网址为：https://github.com/zalandoresearch/fashion-mnist，如图 2.9 所示，单击 Download 即可下载程序运行所需数据集。
```

![](img/bef5d57039b9e3819bf07153f102e5b8_24_0.png)

图 2.9 Fashion-MNIST 训练样本

```
print('Training data shape : ', train_X.shape, train_Y.shape) #输出训练样本的结构，有 60 000 个训练样本，每个样本均由 28×28 的数组构成
print('Testing data shape : ', test_X.shape, test_Y.shape) #输出测试样本的结构，有 10 000 个测试样本，每个样本均由 28×28 的数组构成
#获取训练样本的类别
classes=np.unique(train_Y)
nClasses=len(classes)
print('Total number of outputs : ', nClasses) #输出类别，共计 10 类
print('Output classes : ', classes) #输出类别，分别用 0~9 的数字表示，如
```

表 2.2 所示。

### 表 2.2 样本的类别

| 类别标记 | 描述 | 类别标记 | 描述 |
|---|---|---|---|
| 0 | T-shirt/top | 5 | Sandal |
| 1 | Trouser | 6 | Shirt |
| 2 | Pullover | 7 | Sneaker |
| 3 | Dress | 8 | Bag |
| 4 | Coat | 9 | Ankle boot |

```
# 查看训练和测试样本的第一幅图,如图 2.10 所示
plt.figure(figsize=[5,5])
plt.subplot(121)
plt.imshow(train_X[0,:,:], cmap='gray')
plt.title("Class Number: {}".format(train_Y[0]))
plt.subplot(122)
plt.imshow(test_X[0,:,:], cmap='gray')
plt.title("Class Number: {}".format(test_Y[0]))
```

![](img/bef5d57039b9e3819bf07153f102e5b8_25_0.png)

图 2.10 训练和测试样本的第一幅图

```
# 训练和测试样本均为灰度图,将它们转化为 28×28×1 的矩阵存储
train_X=train_X.reshape(-1, 28,28, 1)
test_X=test_X.reshape(-1, 28,28, 1)
train_X.shape, test_X.shape # 查看样本转换后的 shape,输出结果为 ((60 000, 28, 28, 1), (10 000, 28, 28, 1))
# 原样本中存储的数据类型为整数,需要转换为浮点数,并做归一化处理
train_X=train_X.astype('float32')
test_X=test_X.astype('float32')
train_X=train_X / 255.
```

```
test_X=test_X / 255.
#由于机器学习算法不能直接处理类别(0~9),因此需要进行1位热编码(one-hot encoding)。例如类别9的1位热编码为[0 0 0 0 0 0 0 0 1 0],即用一个布尔型数组,只有第9位为1,其他都为0。
train_Y_one_hot=to_categorical(train_Y)
test_Y_one_hot=to_categorical(test_Y)
#查看将类别(0~9)进行1位热编码转换前后的变化
print('Original label:', train_Y[0])
print('After conversion to one-hot:', train_Y_one_hot[0])
#将训练样本集分为训练集和测试集,训练集用于训练模型,测试集用于检验模型。此处将原本样本集中的80%用于训练模型,20%用于测试模型
from sklearn.model_selection import train_test_split
train_X,valid_X,train_label,valid_label=train_test_split(train_X,
train_Y_one_hot, test_size=0.2, random_state=13)
#检查训练集和测试集的shape
train_X.shape,valid_X.shape,train_label.shape,valid_label.shape
#加载构建深度学习模型所需要的包
import keras
from keras.models import Sequential,Input,Model
from keras.layers import Dense, Dropout, Flatten
from keras.layers import Conv2D, MaxPooling2D
from keras.layers.normalization import BatchNormalization
from keras.layers.advanced_activations import LeakyReLU
batch_size=64 #进行梯度下降时每个batch包含的样本数,训练时一个batch的样本会被计算一次梯度下降,使目标函数优化一步
epochs=20 #模型训练次数
num_classes=10#使用Keras中的序列模型(Sequential)构建卷积神经网络
fashion_model=Sequential()
fashion_model.add(Conv2D(32, kernel_size=(3,3), activation='linear',
input_shape=(28,28,1), padding='same'))#向序列模型中添加第一层二维卷积层,卷积核的数目为32(输出的维度为32维),卷积核的大小为3×3,激活函数为linear,输入数据的shape为28×28×1,same代表保留边界处的卷积结果
fashion_model.add(LeakyReLU(alpha=0.1)) #为第1层卷积神经网络的输出添加激活函数
fashion_model.add(MaxPooling2D((2, 2), padding='same'))#二维最大值池化,使图片在两个维度上均变为原长的一半
fashion_model.add(Conv2D(64, (3, 3), activation='linear', padding=
'same')) #向序列模型中添加第2层二维卷积层,卷积核的数目为64(输出的维度为64维),卷积核的大小为3×3
fashion_model.add(LeakyReLU(alpha=0.1)) #激活函数
fashion_model.add(MaxPooling2D(pool_size=(2, 2), padding='same'))
#二维最大值池化,使图片在两个维度上均变为原长的一半
```

```
fashion_model.add(Conv2D(128, (3, 3), activation='linear',padding='same')) #向序列模型中添加第 3 层二维卷积层,卷积核的数目为 128(输出的维度为 128维),卷积核的大小为 3×3
fashion_model.add(LeakyReLU(alpha=0.1)) #激活函数
fashion_model.add(MaxPooling2D(pool_size=(2, 2),padding='same')) #二维最大值池化,使图片在两个维度上均变为原长的一半
fashion_model.add(Flatten()) #用于将输入“压平”,即把多维的输入一维化,用于卷积层到全连接层的过渡
fashion_model.add(Dense(128, activation='linear')) #全连接层,激活函数为 linear
fashion_model.add(LeakyReLU(alpha=0.1)) #激活函数
fashion_model.add(Dense(num_classes, activation='softmax')) #全连接层,使得输出数据维数跟样本类别维数相同(此处为 num_classes=10)
fashion_model.compile(loss=keras.losses.categorical_crossentropy, optimizer=keras.optimizers.Adam(),metrics=['accuracy']) #编译模型,深度学习网络构建完成后,需要编译后才能运行
fashion_model.summary() #查看深度学习模型的结构,如图 2.11 所示。
```

| Layer (type) | Output Shape | Param # |
|---|---|---|
| conv2d_1 (Conv2D) | (None, 28, 28, 32) | 320 |
| leaky_re_lu_1 (LeakyReLU) | (None, 28, 28, 32) | 0 |
| max_pooling2d_1 (MaxPooling2) | (None, 14, 14, 32) | 0 |
| conv2d_2 (Conv2D) | (None, 14, 14, 64) | 18496 |
| leaky_re_lu_2 (LeakyReLU) | (None, 14, 14, 64) | 0 |
| max_pooling2d_2 (MaxPooling2) | (None, 7, 7, 64) | 0 |
| conv2d_3 (Conv2D) | (None, 7, 7, 128) | 73856 |
| leaky_re_lu_3 (LeakyReLU) | (None, 7, 7, 128) | 0 |
| max_pooling2d_3 (MaxPooling2) | (None, 4, 4, 128) | 0 |
| flatten_1 (Flatten) | (None, 2048) | 0 |
| dense_1 (Dense) | (None, 128) | 262272 |
| leaky_re_lu_4 (LeakyReLU) | (None, 128) | 0 |
| dense_2 (Dense) | (None, 10) | 1290 |
| Total params: 356,234 |
| Trainable params: 356,234 |
| Non-trainable params: 0 |

图 2.11 深度学习模型的结构

```
fashion_train=fashion_model.fit(train_X,train_label,batch_size=batch_size,epochs=epochs,verbose=1,validation_data=(valid_X, valid_label)) #训练神经网络模型,共计训练 20 次
test_eval=fashion_model.evaluate(test_X, test_Y_one_hot, verbose=0) #计算模型误差
```

```
print('Test loss:', test_eval[0]) #输出测试误差结果 丢失率 loss (44.46%)
print('Test accuracy:', test_eval[1]) #输出测试准确率 accuracy (91.54%)
```

#绘制准确率和丢失率曲线,以判断模型是否过拟合,如图 2.12 所示。

![](img/bef5d57039b9e3819bf07153f102e5b8_28_0.png)

图 2.12 训练和测试样本的 loss/accuracy 曲线

#图 2.12 中横坐标表示训练次数,从中可以看出随着训练次数的不断增加训练样本的丢失率不断减少、准确率不断提高,而测试样本却出现了相反的结果。这表明,训练得到的模型出现了过拟合现象。

```
accuracy=fashion_train.history['acc']
val_accuracy=fashion_train.history['val_acc']
loss=fashion_train.history['loss']
val_loss=fashion_train.history['val_loss']
epochs=range(len(accuracy))
plt.plot(epochs, accuracy, 'bo', label='Training accuracy')
plt.plot(epochs, val_accuracy, 'b', label='Validation accuracy')
plt.title('Training and validation accuracy')
plt.legend()
plt.figure()
plt.plot(epochs, loss, 'bo', label='Training loss')
plt.plot(epochs, val_loss, 'b', label='Validation loss')
plt.title('Training and validation loss')
plt.legend()
plt.show()
```

#为了防止模型出现过拟合现象,我们需要在神经网络的每一层后添加 Dropout 层,这样即可有效降低过拟合现象。

```
batch_size=64
epochs=20
num_classes=10
fashion_model=Sequential()
fashion_model.add(Conv2D(32,kernel_size=(3,3),activation='linear',
padding='same',input_shape=(28,28,1)))
```
