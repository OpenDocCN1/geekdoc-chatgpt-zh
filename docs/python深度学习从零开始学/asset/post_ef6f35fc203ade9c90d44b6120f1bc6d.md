
运行结果如图 8-8 所示，可以看到 MNIST 数据集中包含了书写笔画各异的数字图像。

![](img/470b883072d48f1f2917a784b39d4327_136_0.png)

图 8-8

## 8.2.2 数据预处理

我们必须先做一些数据预处理，才能使用卷积神经网络模型进行训练和预测。

首先，由于 MNIST 数据集是灰度图，图像是 28×28 矩阵，而 CNN 的输入是四维的张量，所以需要做形状变换。如图 8-9 所示，将数字图像特征值转换为 60000×28×28×1 的四维矩阵。

```
# 将数据reshape, CNN的输入是4维的张量（可看做多维的向量）
#第一维是样本数目，第二维和第三维是长度和宽度，第四维是像素通道
X_train = X_train.reshape(X_train.shape[0], 28, 28,1).astype('float32')
X_test = X_test.reshape(X_test.shape[0], 28, 28,1).astype('float32')
print('X_train:',X_train.shape)
print('X_test:',X_test.shape)
X_train: (60000, 28, 28, 1)
X_test: (10000, 28, 28, 1)
```

图 8-9

然后还要将数字图像特征值做标准化处理，因为原本图像中每个像素的取值是一个 0-255 的整数，当图片输入卷积神经网络模型，一般要转化为 0~1 的数。因此，将输入数据统一除以 255，如图 8-10 所示。

```
#将输入特征缩放到0~1范围的数
X_train = X_train / 255
X_test = X_test / 255
```

图 8-10

另外对于输出数据，不再简单地用一个数字来表示，而是采用独热编码作为输出，即对于训练数据与测试数据的标签进行独热编码转换，这里可以使用 Keras 提供的函数 np_utils.

to_categorical 来完成。如图 8-11 所示，查看训练数据标签字段前 5 项训练数据，我们可以看到是 0~9 的数字，使用 np_utils.to_categorical 分别传入参数 y_train_label（训练数据标签）和 y_test_label（测试数据标签），进行独热编码转换后，再查看训练数据标签字段的前 5 项数据，全部转换为了由 0 和 1 组成的矩阵，例如第 1 项数据，原来的真实值是 5，经过独热编码转换后是 000010000，即只有第 5 个数字是 1，其余都是 0。

```
from keras.utils import np_utils
# 类别标签采用独热编码one-hot
# 类别标签采用独热编码one-hot
y_train_onehot = np_utils.to_categorical(y_train)
y_test_onehot = np_utils.to_categorical(y_test)
print(y_train[:5])
print(y_train_onehot[:5])
```

[5 0 4 1 9]
[[0. 0. 0. 0. 0. 1. 0. 0. 0. 0.]
 [1. 0. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 1. 0. 0. 0. 0. 0.]
 [0. 1. 0. 0. 0. 0. 0. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 1.]]

图 8-11

## 8.2.3 建立模型

处理完数据以后，开始搭建卷积神经网络模型，使用 Keras 中 Sequential 模型搭建一个基础的卷积神经网络。该网络的架构如下：

- 卷积层 1：16 个 5×5 的卷积核，输入图像形状为 28×28 的单色图像，使用 ReLU 作为激活函数。
- 池化层 1：2×2 大小的池化核，对图像缩减采样，但不会改变图像的数量。
- 卷积层 2：执行第 2 次卷积运算，36 个 5×5 的卷积核，使用 ReLU 作为激活函数。
- 池化层 2：2×2 大小的池化核，再次对图像缩减采样。
- 加入 Dropout 层避免过拟合，每次训练过程，会随机放弃一定数量神经元。
- 平坦层：将数据形状转为向量。
- 全连接层（隐藏层）：维度为 128，即 128 个神经元，使用 ReLU 作为激活函数。
- 加入 Dropout 层避免过拟合，每次训练过程，会随机放弃一定数量神经元。
- 全连接层（输出层）：维度为 10（共有 10 个神经元，对应 0~9 共 10 个数字），使用 softmax 作为激活函数，输出每个分类的概率。对于分类问题，最后一层往往会使用一个维度与类别数量相同、激活函数为 softmax 的层作为全连接层。

程序代码如图 8-12 所示，Keras 提供了非常方便的搭建卷积神经网络的方法，建立一个 Sequential 线性堆叠模型，后续只需要使用 model.add() 方法将各个神经网络层加入模型即可。

查看这个卷积神经网络模型摘要，显示结果如图 8-13 所示。这个卷积神经网络包含了输入层、卷积层 1、池化层 1、卷积层 2、池化层 2、平坦层、全连接层（隐藏层）、全连接层（输出层），并且加入 Dropout 层来避免过拟合。

## Python 深度学习从零开始学

```
```python
from keras.models import Sequential
from keras.layers import Dense
from keras.layers import Dropout
from keras.layers import Flatten
from keras.layers.convolutional import Conv2D
from keras.layers.convolutional import MaxPooling2D
def baseline_model():
    model = Sequential()
    model.add(Conv2D(filters=16,kernel_size=(5, 5), input_shape=(28, 28,1), padding='same',activation='relu'))
    model.add(MaxPooling2D(pool_size=(2, 2)))
    model.add(Conv2D(filters=36,kernel_size=(5, 5), padding='same', activation='relu'))
    model.add(MaxPooling2D(pool_size=(2, 2)))
    model.add(Dropout(0.25))
    model.add(Flatten())
    model.add(Dense(128, activation='relu'))
    model.add(Dropout(0.5))
    model.add(Dense(10, activation='softmax'))
    return model
# 建立模型
model = baseline_model()
print(model.summary())#查看模型概况
```
```

图 8-12

| Layer (type) | Output Shape | Param # |
|---|---|---|
| conv2d_1 (Conv2D) | (None, 28, 28, 16) | 416 |
| max_pooling2d_1 (MaxPooling2) | (None, 14, 14, 16) | 0 |
| conv2d_2 (Conv2D) | (None, 14, 14, 36) | 14436 |
| max_pooling2d_2 (MaxPooling2) | (None, 7, 7, 36) | 0 |
| dropout_1 (Dropout) | (None, 7, 7, 36) | 0 |
| flatten_1 (Flatten) | (None, 1764) | 0 |
| dense_1 (Dense) | (None, 128) | 225920 |
| dropout_2 (Dropout) | (None, 128) | 0 |
| dense_2 (Dense) | (None, 10) | 1290 |
| Total params: 242,062 | Trainable params: 242,062 | Non-trainable params: 0 |
| None |  |  |

图 8-13

示例模型说明如下：

- 输入层输入二维的图像，一个 28×28 的矩阵。
- 在卷积层 1，采用 16 个由过滤器随机产生的 5×5 的零一矩阵和输入层的 28×28 的矩阵相乘后相加，变成 16 个 28×28 的矩阵图像。卷积层的作用就是提取输入图像的特征，如边缘、线条和角。
- 在池化层 1，将卷积层 1 输出的 16 个矩阵图像进行最大池化缩减采样，每 4 个单元选出最大值进行缩减，变成 14×14 的矩阵图像，共 16 个。缩减采样可以减少所需处理的数据点，让图像位置差异变小，参数的数量和计算量下降。
- 在卷积层 2，将池化层 1 输入的 16 个 14×14 矩阵图像与采用 36 个由过滤器随机生成的 5×5 的零一矩阵相乘后相加，产生 36 个 14×14 的矩阵图像。
- 在池化层 2，将卷积层 2 产生的 36 个矩阵图像进行最大池化缩减采样，每 4 个单元选出最大值进行缩减，变成 7×7 的矩阵图像，共 36 个。
- 在平坦层，作为神经网络的输入部分，有 36×7×7=1764 个神经元。
- 隐藏层有 128 个神经元。
- 输出层有 10 个神经元。

## 8.2.4 进行训练

搭建完卷积神经网络模型，就可以使用反向传播法进行训练，神经网络的目的就是通过训练使分布逼近真实分布。在训练模型之前，我们需要用 compile 方法对训练模型进行设置。如图 8-14 所示，设置 loss 为 categorical_crossentropy，设置 optimizer 为 adam，设置 metrics 为 accuracy。

```
# 对训练模型进行设置
#使用adam优化器，使用交叉熵作为损失函数
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
```

图 8-14

Keras 提供 fit 函数用于训练卷积神经网络，如图 8-15 所示，将训练的输入与输出传给 fit 函数，设置训练数据与验证数据比例 validation_split（Keras 会自动按比例将数据分成训练数据和验证数据），指定批量大小 batch_size 为 300，训练轮数 epochs 为 10，verbose=2 表示显示训练过程，训练过程存储在 train_history 变量中。

```
train_history=model.fit(x=X_train, y=y_train_onehot, validation_split=0.2, epochs=10, batch_size=300, verbose=2)
```

图 8-15

程序运行结果如图 8-16 所示。使用 60000×（1-0.2）=48000 项训练数据进行训练，每一批次为 300 项，所以共分为 48000/300=160 个批次进行训练，共执行 10 个训练周期。训练完成后，计算每一个训练周期的准确率与误差，可以发现无论是使用训练数据还是验证数据，结果都是误差越来越小，准确率越来越高。

```
Train on 48000 samples, validate on 12000 samples
Epoch 1/10
- 68s - loss: 0.5045 - acc: 0.8419 - val_loss: 0.1015 - val_acc: 0.9687
Epoch 2/10
- 67s - loss: 0.1399 - acc: 0.9590 - val_loss: 0.0645 - val_acc: 0.9807
Epoch 3/10
- 71s - loss: 0.1010 - acc: 0.9690 - val_loss: 0.0529 - val_acc: 0.9848
Epoch 4/10
- 77s - loss: 0.0796 - acc: 0.9764 - val_loss: 0.0423 - val_acc: 0.9874
Epoch 5/10
- 67s - loss: 0.0657 - acc: 0.9799 - val_loss: 0.0385 - val_acc: 0.9881
Epoch 6/10
- 66s - loss: 0.0595 - acc: 0.9827 - val_loss: 0.0401 - val_acc: 0.9874
Epoch 7/10
- 66s - loss: 0.0528 - acc: 0.9846 - val_loss: 0.0350 - val_acc: 0.9909
Epoch 8/10
- 67s - loss: 0.0489 - acc: 0.9859 - val_loss: 0.0332 - val_acc: 0.9902
Epoch 9/10
- 68s - loss: 0.0420 - acc: 0.9869 - val_loss: 0.0323 - val_acc: 0.9912
Epoch 10/10
- 69s - loss: 0.0402 - acc: 0.9874 - val_loss: 0.0346 - val_acc: 0.9899
```

图 8-16

## 8.2.5 模型保存和评估

完成训练后保存模型，代码如图 8-17 所示。
现在使用测试数据集来评估模型的准确度，如图 8-18 所示。

```
```python
# 保存模型
filename='keras_mnistcnn demomodel.h5'
model.save(filename)
```
```

图 8-17

```
```python
# 模型评估
scores = model.evaluate(X_test, y_test_onehot)
# 输出在测试集的准确度
print(scores[1])
```
10000/10000 [==================] - 6s 588us/step
0.9915
```

图 8-18

## 8.2.6 进行预测

至此我们建立了模型，完成了模型的训练，接下来将使用该模型进行预测。如图 8-19 所示，使用 model.predict_classes 输入参数（已标准化测试数据的数字图像）进行预测。

为了显示混淆矩阵，我们导入 Pandas 模块，执行代码结果如图 8-20 所示。观察此混淆矩阵，对角线是预测正确的数字，我们发现：真实值是 1，被正确预测为 1 的项数最高，为 1134 项；非对角线的数字，真实值是 9 的数字被预测为 4 的项数为 13，最容易混淆。

```
```python
prediction=model.predict_classes(X_test)
print('测试数据前10项的真实值：',y_test[:10])
print('测试数据前10项的预测值：',prediction[:10])
print('测试数据第340项的真实值：',y_test[340])
print('测试数据第340项的预测值：',prediction[340])
print('测试数据第341项的真实值：',y_test[341])
print('测试数据第341项的预测值：',prediction[341])
print('测试数据第342项的真实值：',y_test[342])
print('测试数据第342项的预测值：',prediction[342])
```
测试数据前10项的真实值：[7 2 1 0 4 1 4 9 5 9]
测试数据前10项的预测值：[7 2 1 0 4 1 4 9 5 9]
测试数据第340项的真实值：5
测试数据第340项的预测值：5
测试数据第341项的真实值：6
测试数据第341项的预测值：6
测试数据第342项的真实值：1
测试数据第342项的预测值：1
```

图 8-19

```python
import pandas as pd
pd.crosstab(y_test, prediction, rownames=['label'], colnames=['predict'])
```
| predict | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
| label |  |  |  |  |  |  |  |  |  |  |
| 0 | 975 | 1 | 0 | 0 | 0 | 0 | 3 | 1 | 0 | 0 |
| 1 | 0 | 1134 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 2 | 1 | 1 | 1027 | 0 | 0 | 0 | 0 | 3 | 0 | 0 |
| 3 | 0 | 0 | 1 | 1005 | 0 | 1 | 0 | 1 | 2 | 0 |
| 4 | 0 | 0 | 0 | 0 | 980 | 0 | 0 | 0 | 0 | 2 |
| 5 | 2 | 0 | 0 | 5 | 0 | 880 | 4 | 0 | 0 | 1 |
| 6 | 3 | 3 | 0 | 0 | 1 | 0 | 950 | 0 | 1 | 0 |
| 7 | 0 | 3 | 4 | 1 | 0 | 0 | 0 | 1018 | 1 | 1 |
| 8 | 3 | 0 | 1 | 0 | 0 | 0 | 0 | 2 | 965 | 3 |
| 9 | 1 | 4 | 0 | 1 | 13 | 2 | 0 | 6 | 1 | 981 |

图 8-20

## 8.3 实例二：CIFAR-10 图像识别

我们还是使用 Keras 建立卷积神经网络模型，并且训练模型，然后用训练完成的模型来识别 CIFAR-10 图像数据集，并且会进行更多次的卷积和池化来提高识别准确率。
