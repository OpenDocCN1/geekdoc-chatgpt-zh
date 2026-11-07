
## 8.3.1 CIFAR-10 图像数据集介绍

CIFAR-10 数据集是 60000 个 32×32 的彩色图像，分为 10 类，分别是 airplane、automobile、bird、cat、deer、dog、frog、horse、boat、ship、truck，如图 8-21 所示。其中 50000 幅用作训练图像，10000 幅用作测试图像。

![](img/470b883072d48f1f2917a784b39d4327_141_0.png)

图 8-21

Keras 提供了 cifar10.load_data() 方法，用于下载并读取 CIFAR-10 数据，如图 8-22 所示。第一次调用 cifar10.load_data() 方法，程序会检查是否存在 cifar-10-batches-py.tar.gz 文件，如果没有，就会下载该文件，并且解压缩下载的文件。

```
from keras.datasets import cifar10
import numpy as np
(x_img_train,y_label_train), (x_img_test,y_label_test)=cifar10.load_data()
Downloading data from https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz
```

图 8-22

下载的 CIFAR-10 数据文件如图 8-23 所示。

![](img/470b883072d48f1f2917a784b39d4327_141_1.png)

图 8-23

如图 8-24 所示，可以看到训练集中有 50000 个大小为 32×32 的三原色图，而测试集有 10000 个大小为 32×32 的三原色图，数字 3 代表图像是一个 RGB 三原色图。

## 8.3.2 数据预处理

我们必须先做些数据预处理，才能使用卷积神经网络模型进行训练和预测。
首先，将图像的数字进行标准化，因为原本图像中每个像素的取值是一个0~255 的整数，当图片输入卷积神经网络模型时，一般要转化为 0~1 的数，因此将输入数据统一除以 255，如图 8-25 所示。

```
print("train data:",'images:',x_img_train.shape,
      "labels:",y_label_train.shape)
print("test data:",'images:',x_img_test.shape ,
      "labels:",y_label_test.shape)

train data: images: (50000, 32, 32, 3)  labels: (50000, 1)
test  data: images: (10000, 32, 32, 3)  labels: (10000, 1)
```

图 8-24

```
x_img_train_normalize = x_img_train.astype('float32') / 255.0
x_img_test_normalize = x_img_test.astype('float32') / 255.0
```

图 8-25

另外，对于 CIFAR-10 数据集，我们希望预测图像的类型，可以将图像的标签以一位有效编码进行转换，即转换为 10 个由 0 和 1 组成的组合，分别代表 10 个不同的分类。例如，0000000000 代表 airplane，0100000000 代表 automobile，等等。10 个数字正好对应输出层的 10 个神经元。进行独热编码转换的代码如图 8-26 所示。

```
from keras.utils import np_utils
y_label_train_OneHot = np_utils.to_categorical(y_label_train)
y_label_test_OneHot = np_utils.to_categorical(y_label_test)
```

图 8-26

查看转换后的结果，如图 8-27 所示，第 1 项数据原来的真实值是 6，执行独热编码转换后变成 000001000。

```
print(y_label_train_OneHot.shape)
print(y_label_train_OneHot[:5])

(50000, 10)
[[0. 0. 0. 0. 1. 0. 0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 1.]
 [0. 0. 0. 0. 0. 0. 0. 0. 1.]
 [0. 0. 0. 1. 0. 0. 0. 0. 0.]
 [0. 1. 0. 0. 0. 0. 0. 0.]]
```

图 8-27

## 8.3.3 建立模型

处理完数据以后，开始搭建卷积神经网络模型。在模型中我们交替增加两个卷积层、两个池化层，进行两次卷积运算，然后再增加全连接层（包括一个平坦层、两个隐藏层和一个输出层），并且在每个卷积层和池化层之间、两个隐藏层之间都增加一个 Dropout 层，其目的是选择性地放弃一些神经元，防止模型过拟合。

建立卷积神经网络的代码如图 8-28 所示。

```
```python
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D, ZeroPadding2D
model = Sequential()
#建立卷积层1, 输入图像大小32*32
model.add(Conv2D(filters=32,kernel_size=(3,3),input_shape=(32, 32,3),
activation='relu',padding='same'))
#加入Dropout
model.add(Dropout(rate=0.25))
#建立池化层1
model.add(MaxPooling2D(pool_size=(2, 2)))
#建立卷积层2
model.add(Conv2D(filters=64, kernel_size=(3, 3), activation='relu', padding='same'))
#加入Dropout
model.add(Dropout(0.25))
#建立池化层2
model.add(MaxPooling2D(pool_size=(2, 2)))
#建立平坦层
model.add(Flatten())
#加入Dropout
model.add(Dropout(rate=0.25))
#建立隐藏层
model.add(Dense(1024, activation='relu'))
#加入Dropout
model.add(Dropout(rate=0.25))
#建立输出层
model.add(Dense(10, activation='softmax'))
```
```

图 8-28

Keras 提供了非常方便搭建卷积神经网络的方法，建立一个 Sequential 线性堆叠模型，后续只需要使用 model.add() 方法将各个神经网络层加入模型即可。通过 print(model.summary()) 查看这个卷积神经网络模型摘要，结果如图 8-29 所示，这个卷积神经网络包含了输入层、卷积层 1、池化层 1、卷积层 2、池化层 2、平坦层、隐藏层、隐藏层（输出层），并且把 Dropout 层加入了模型。Dropout(0.25) 表示每次训练迭代时，会随机地在神经网络中放弃 25% 的神经元，避免过拟合。

| Layer (type) | Output Shape | Param # |
|---|---|---|
| conv2d_1 (Conv2D) | (None, 32, 32, 32) | 896 |
| dropout_1 (Dropout) | (None, 32, 32, 32) | 0 |
| max_pooling2d_1 (MaxPooling2D) | (None, 16, 16, 32) | 0 |
| conv2d_2 (Conv2D) | (None, 16, 16, 64) | 18496 |
| dropout_2 (Dropout) | (None, 16, 16, 64) | 0 |
| max_pooling2d_2 (MaxPooling2D) | (None, 8, 8, 64) | 0 |
| flatten_1 (Flatten) | (None, 4096) | 0 |
| dropout_3 (Dropout) | (None, 4096) | 0 |
| dense_1 (Dense) | (None, 1024) | 4195328 |
| dropout_4 (Dropout) | (None, 1024) | 0 |
| dense_2 (Dense) | (None, 10) | 10250 |
| Total params: 4,224,970 | Trainable params: 4,224,970 | Non-trainable params: 0 |
| None | | |

图 8-29

## 8.3.4 进行训练

搭建完卷积神经网络模型，就可以使用反向传播法进行训练了。在训练模型之前，我们需要用 compile 方法对训练模型进行设置，如图 8-30 所示。

```
# 对训练模型进行设置
#使用adam优化器，使用交叉熵做为损失函数
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
```

图 8-30

Keras 提供 fit 函数用于训练卷积神经网络，如图 8-31 所示，将训练的输入与输出传给 fit 函数，设置训练与验证数据比例 validation_split，指定批量大小 batch_size 为 128，训练轮数 epochs 为 10，训练过程存储在 train_history 变量中。

```
train_history=model.fit(x_img_train_normalize, y_label_train_OneHot,
validation_split=0.2,
epochs=10, batch_size=128, verbose=1)
```

图 8-31

程序运行结果如图 8-32 所示。使用 50000×（1-0.2）=40000 项训练数据进行训练，每一批次为 128 项，共分为 40000/128=313 个批次进行训练，共执行 10 个训练周期。训练完成后，计算每一个训练周期的准确率与误差。

```
train_history=model.fit(x_img_train_normalize, y_label_train_OneHot,
validation_split=0.2,
epochs=10, batch_size=128, verbose=1)
Train on 40000 samples, validate on 10000 samples
Epoch 1/10
40000/40000 [===================] - 221s 6ms/step - loss: 1.5936 - acc: 0.4280 - val_loss: 1.3579 - val_acc: 0.5429
Epoch 2/10
40000/40000 [===================] - 208s 5ms/step - loss: 1.1990 - acc: 0.5767 - val_loss: 1.1924 - val_acc: 0.5960
Epoch 3/10
40000/40000 [===================] - 213s 5ms/step - loss: 1.0538 - acc: 0.6270 - val_loss: 1.0416 - val_acc: 0.6666
Epoch 4/10
40000/40000 [===================] - 210s 5ms/step - loss: 0.9395 - acc: 0.6669 - val_loss: 0.9944 - val_acc: 0.6869
Epoch 5/10
40000/40000 [===================] - 210s 5ms/step - loss: 0.8509 - acc: 0.7020 - val_loss: 0.9693 - val_acc: 0.6788
Epoch 6/10
40000/40000 [===================] - 227s 6ms/step - loss: 0.7640 - acc: 0.7309 - val_loss: 0.8799 - val_acc: 0.7174
Epoch 7/10
40000/40000 [===================] - 183s 5ms/step - loss: 0.6901 - acc: 0.7579 - val_loss: 0.8488 - val_acc: 0.7192
Epoch 8/10
40000/40000 [===================] - 191s 5ms/step - loss: 0.6184 - acc: 0.7821 - val_loss: 0.8215 - val_acc: 0.7263
Epoch 9/10
40000/40000 [===================] - 196s 5ms/step - loss: 0.5427 - acc: 0.8103 - val_loss: 0.8005 - val_acc: 0.7247
Epoch 10/10
40000/40000 [===================] - 206s 5ms/step - loss: 0.4784 - acc: 0.8318 - val_loss: 0.7651 - val_acc: 0.7418
```

图 8-32

每一个训练周期的准确率与误差记录在 train_history 变量中，可以使用如图 8-33 所示的程序代码读取 train_history，以图表显示训练过程。

```
import matplotlib.pyplot as plt
def show_train_history(train_acc, test_acc):
plt.plot(train_history.history['train_acc'])
plt.plot(train_history.history['test_acc'])
plt.title('Train History')
plt.ylabel('Accuracy')
plt.xlabel('Epoch')
plt.legend(['train', 'test'], loc='upper left')
plt.show()
```

图 8-33

如图 8-34 所示，绘制出准确率执行的结果和误差的执行结果。

在 Epoch 训练后期，“loss 训练的误差” 比 “val_loss 验证的误差” 小，如图 8-35 所示。

![](img/470b883072d48f1f2917a784b39d4327_145_0.png)

![](img/470b883072d48f1f2917a784b39d4327_145_1.png)

## 8.3.5 模型评估

完成了模型的训练，现在使用测试数据集来评估模型的准确度，如图 8-36 所示。

可以看到训练的准确率不是很高，想要提高准确率可能要增加卷积核的数目，或者是卷积层的数目，等等。

```
scores = model.evaluate(x_img_test_normalize,
    y_label_test_OneHot, verbose=0)
scores[1]
0.7352
```

图 8-36

## 8.3.6 进行预测

使用模型进行预测，可以使用 model.predict_classes 函数，输入测试数据的图像来进行预测。查看预测结果的前 10 项数据，可以看到第 1 项预测的结果是 3，第 2 项是 8，等等，如图 8-37 所示。

```
prediction=model.predict_classes(x_img_test_normalize)
prediction[:10]
array([3, 8, 8, 0, 6, 6, 1, 6, 3, 1], dtype=int64)
```

图 8-37

要进行预测，首先以 Python 字典 dict 定义每一个数字所代表的图像类别的名称，定义 plot_images_labels_prediction 函数显示前 10 项预测结果，传入测试数据图像、label（真实值）及 prediction（预测结果），代码如图 8-38 所示。

其次，定义 show_Predicted_Probability 函数，显示预测每一种类别的概率，显示真实值与预测结果，显示图像，显示预测概率，如图 8-39 所示。

如图 8-40 所示，查看第 0 项数据预测的概率，可从知道预测为 cat 的概率最高，所以最后预测结果是 cat，预测正确。

如图 8-41 所示，查看第 20 项数据预测概率，可以知道预测为 deer 的概率最高，所以预测结果是 deer，但真实值是 horse，此项预测是错误的。

## Python 深度学习从零开始学

```
label_dict={0:"airplane",1:"automobile",2:"bird",3:"cat",4:"deer",5:"dog",6:"frog",7:"horse",8:"ship",9:"truck"}

import matplotlib.pyplot as plt
def plot_images_labels_prediction(images,labels,prediction, idx,num=10):
    fig = plt.gcf()
    fig.set_size_inches(12,14)
    if num>25: num=25
    for i in range(0,num):
        ax=plt.subplot(5,5,1+i)
        ax.imshow(images[idx],cmap='binary')
        title=str(i)+': '+label_dict[labels[i][0]]
        if len(prediction)>0:
            title+=' => '+label_dict[prediction[i]]
        ax.set_title(title,fontsize=10)
        ax.set_xticks([]); ax.set_yticks([])
        idx+=1
    plt.show()
```

图 8-38

```
Predicted_Probability=model.predict(x_img_test_normalize)

def show_Predicted_Probability(y,prediction, x_img,Predicted_Probability,i):
    print('label:',label_dict[y[i][0]],
          'predict:',label_dict[prediction[i]])
    plt.figure(figsize=(2,2))
    plt.imshow(np.reshape(x_img_test[i],(32,32,3)))
    plt.show()
    for j in range(10):
        print(label_dict[j]+
              'Probability:%1.9f'%(Predicted_Probability[i][j]))
```

图 8-39

```
show_Predicted_Probability(y_label_test,prediction, x_img_test,Predicted_Probability,0)

label: cat predict: cat

airplane Probability:0.001212501
automobile Probability:0.005048339
bird Probability:0.002015128
cat Probability:0.819681168
deer Probability:0.001312125
dog Probability:0.148376912
frog Probability:0.001076718
horse Probability:0.000923077
ship Probability:0.018333757
truck Probability:0.002020348
```

图 8-40

```
show_Predicted_Probability(y_label_test,prediction, x_img_test,Predicted_Probability,20)

label: horse predict: deer

airplane Probability:0.084685601
automobile Probability:0.085753739
bird Probability:0.025651244
cat Probability:0.219731825
deer Probability:0.273784131
dog Probability:0.032510079
frog Probability:0.000138847
horse Probability:0.260789543
ship Probability:0.006445847
truck Probability:0.010869170
```

图 8-41

我们想要进一步知道所建立的模型中哪些图像类别的预测准确率最高，哪些图像类别最容易混淆。但是 pd.crosstab 的输入都必须是一维数组，预测结果也是一维数组，而 y_label_test 真实值的形状是二维数组，所以要使用 reshape(-1) 将 y_label_test 转换为一维数组，如图 8-42 所示。

```
print(prediction.shape)
(10000,)

print(y_label_test.shape)
(10000, 1)

print(y_label_test)
[[3]
 [8]
 [8]
 ...
 [5]
 [1]
 [7]]

print(y_label_test.reshape(-1))
[3 8 8 ... 5 1 7]
```

图 8-42
