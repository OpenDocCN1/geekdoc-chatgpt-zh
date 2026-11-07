
显示输出类别字典以方便对照，如图 8-43 所示。

```
print(label_dict)
{0: 'airplane', 1: 'automobile', 2: 'bird', 3: 'cat', 4: 'deer', 5: 'dog', 6: 'frog', 7: 'horse', 8: 'ship', 9: 'truck'}
```

图 8-43

导入 Pandas 模块，执行代码建立混淆矩阵，结果如图 8-44 所示。

观察混淆矩阵的结果，对角线是预测正确的数字，我们发现：真实值是 6 “frog”，被正确预测的项数为 786，不容易混淆。非对角线的数字，真实值是 3 “cat”，但被预测是 5 “dog”，项数为 171，猫和狗最容易混淆。

```
import pandas as pd
pd.crosstab(y_label_test.reshape(-1),prediction,
rownames=['label'],colnames=['predict'])
```

| label \ predict | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 775 | 16 | 31 | 14 | 27 | 16 | 9 | 11 | 67 | 34 |
| 1 | 15 | 826 | 4 | 10 | 5 | 11 | 10 | 4 | 24 | 91 |
| 2 | 63 | 4 | 584 | 59 | 142 | 64 | 37 | 27 | 14 | 6 |
| 3 | 17 | 12 | 44 | 547 | 111 | 171 | 53 | 28 | 7 | 10 |
| 4 | 14 | 2 | 38 | 41 | 794 | 31 | 26 | 43 | 9 | 2 |
| 5 | 11 | 2 | 35 | 161 | 72 | 640 | 21 | 48 | 5 | 5 |
| 6 | 4 | 5 | 46 | 65 | 61 | 23 | 786 | 4 | 5 | 1 |
| 7 | 11 | 0 | 19 | 33 | 81 | 60 | 7 | 781 | 4 | 4 |
| 8 | 58 | 36 | 11 | 15 | 19 | 10 | 5 | 2 | 824 | 20 |
| 9 | 33 | 69 | 8 | 22 | 4 | 17 | 8 | 22 | 22 | 795 |

图 8-44

## 8.4 实例三：猫狗识别

要说到深度学习图像分类的经典案例之一，那就是猫狗识别大战了。猫和狗在外观上的差别还是挺明显的，无论是体型、四肢、脸庞和毛发等，都是能通过肉眼很容易进行区分的。那如何让机器来识别猫和狗呢？这就需要用到卷积神经网络，具体实现可以使用 Keras 框架。

### 8.4.1 猫狗数据集介绍

下载猫狗训练集与验证集的压缩包，提取到项目目录下。这个文件夹里面包含训练数据和验证数据集的子目录，而且每个子目录都包含猫和狗的子目录。训练集文件夹 training_set，包含了成千幅猫和狗的图片，每幅图片都含有标签，这个标签是作为文件名的一部分。我们将用这个文件夹来训练和评估模型。

测试集文件夹中每幅图片都以数字来命名。对于数据集中的每幅图片来说，我们的模型都要预测这幅图片上是狗还是猫（1=狗，0=猫）。

猫狗数据可视化的程序代码如下：

```
import os
base_dir = 'cat-and-dog'
# 构造路径存储训练数据，验证数据以及测试数据
train_dir = os.path.join(base_dir, 'training_set')
validation_dir = os.path.join(base_dir, 'validation_set')
train_cats_dir = os.path.join(train_dir, 'cats')
train_dogs_dir = os.path.join(train_dir, 'dogs')
validation_cats_dir = os.path.join(validation_dir, 'cats')
validation_dogs_dir = os.path.join(validation_dir, 'dogs')
train_cat_fnames = os.listdir(train_cats_dir)
train_dog_fnames = os.listdir(train_dogs_dir)
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
# 输出图表的参数，将以 4×4 的配置输出猫狗数据集的部分图片
nrows = 4
ncols = 4
# 迭代图像的当前索引
pic_index = 0
# 设置 matplotlib（Python 的 2D 绘图库）图，并将其设置为适合 4×4 图片大小
fig = plt.gcf()
fig.set_size_inches(ncols * 4, nrows * 4)
pic_index += 8
next_cat_pix = [os.path.join(train_cats_dir, fname)
                for fname in train_cat_fnames[pic_index-8:pic_index]]
next_dog_pix = [os.path.join(train_dogs_dir, fname)
                for fname in train_dog_fnames[pic_index-8:pic_index]]
for i, img_path in enumerate(next_cat_pix+next_dog_pix):
    # 设置子图，子图的索引从 1 开始
    sp = plt.subplot(nrows, ncols, i + 1)
    # 不显示轴（网格线）
    sp.axis('Off')
    img = mpimg.imread(img_path)
    plt.imshow(img)
plt.show()
```

Python 深度学习从零开始学

输出的实际效果如图 8-45 所示，每次重新运行都会查看到新的一批图片。

![](img/470b883072d48f1f2917a784b39d4327_148_0.png)

图 8-45

观察这些图片，可以发现图片种类各异，分辨率也各不相同。图片中的猫和狗的形状、所处位置、体表颜色各不一样；它们的姿态不同，有的坐着有的站着；它们的情绪可能是开心的也可能是伤心的；猫可能在睡觉，而狗可能在汪汪叫着；照片可能以任一焦距从任意角度拍下。这些图片有着无限种可能，对于我们人类来说，在一系列不同种类的照片中识别出一个场景中的宠物自然是毫不费力的事情，然而这对于一台机器来说这可不是一件小事。实际上，如果要机器实现自动分类，那么我们需要知道如何强有力地描绘出猫和狗的特征，也就是说为什么我们认为这幅图片中的是猫，而那幅图片中的却是狗。这需要描绘每个动物的内在特征。深度神经网络在图像分类任务上效果很好的原因是，它们有着能够自动学习多重抽象层的能力，这些抽象层在给定一个分类任务后，又可以对每个类别给出更简单的特征表示。

使用很少的数据来训练一个图像分类模型，这是很常见的情况。猫狗数据集中包含 4000 幅猫和狗的图像（2000 幅猫的图像，2000 幅狗的图像）。我们将 3000 幅图像用于训练（猫 1500 幅，狗 1500 幅），1000 幅图像（猫和狗各 500 幅）用于验证，代码执行结果如图 8-46 所示。

```
In [1]:
import os
base_dir = 'cat-and-dog'
#构造路径存储训练数据，验证数据以及测试数据
train_dir = os.path.join(base_dir, 'training_set')
os.makedirs(train_dir, exist_ok = True)
validation_dir = os.path.join(base_dir, 'validation_set')
os.makedirs(validation_dir, exist_ok = True)
train_cats_dir = os.path.join(train_dir, 'cats')
train_dogs_dir = os.path.join(train_dir, 'dogs')
validation_cats_dir = os.path.join(validation_dir, 'cats')
validation_dogs_dir = os.path.join(validation_dir, 'dogs')
#我们来检查一下，看看每个分组（训练／验证）中分别包含多少张图像
print('total training cat images: ', len(os.listdir(train_cats_dir)))
print('total training dog images: ', len(os.listdir(train_dogs_dir)))
print('total validation cat images: ', len(os.listdir(validation_cats_dir)))
print('total validation dog images: ', len(os.listdir(validation_dogs_dir)))
```

total training cat images: 1500
total training dog images: 1500
total validation cat images: 500
total validation dog images: 500

图 8-46

### 8.4.2 建立模型

我们基于 Keras 深度学习框架创建卷积神经网络模型来识别猫和狗。在以前 MNIST 手写数字识别示例中，我们创建了一个小型卷积神经网络，现在将复用相同的总体结构，即卷积神经网络由卷积层（使用 ReLU 激活函数）和最大池化层交替堆叠构成。

但由于这里要处理的是更大的图像和更复杂的问题，因此需要相应地增大网络，即再增加一个卷积层 + 卷积层最大池化层的组合。这样既可以增大网络容量，也可以进一步减小特征图的尺寸，使其在连接平坦层时尺寸不会太大。本例中初始输入的尺寸为 150×150，最后在平坦层之前的特征图大小为 7×7。

通过 Sequential 对象创建卷积神经网络模型的程序代码如图 8-47 所示。

```
```python
from keras import layers
from keras import models
model = models.Sequential()
#输入图片大小是150*150 3表示图像像素用(R,G,B)表示
model.add(layers.Conv2D(32, (3,3), activation='relu', input_shape=(150, 150, 3)))
model.add(layers.MaxPooling2D((2,2)))

model.add(layers.Conv2D(64, (3,3), activation='relu'))
model.add(layers.MaxPooling2D((2,2)))

model.add(layers.Conv2D(128, (3,3), activation='relu'))
model.add(layers.MaxPooling2D((2,2)))

model.add(layers.Conv2D(128, (3,3), activation='relu'))
model.add(layers.MaxPooling2D((2,2)))

model.add(layers.Flatten())
model.add(layers.Dense(512, activation='relu'))
model.add(layers.Dense(1, activation='sigmoid'))
Using TensorFlow backend.
```
```

图 8-47

通过 print(model.summary()) 输出我们创建的整个卷积神经网络模型的框架，如图 8-48 所示，网络中特征图的深度在逐渐增大（从 32 增大到 128），而特征图的尺寸在逐渐减小（从 148×148 减小到 7×7），这几乎是所有卷积神经网络的模式。因为面对的是一个二分类问题，所以网络最后一层是使用 sigmoid 激活的单一单元（大小为 1 的链接层）。这个单元将对某个类别的概率进行编码。

![](img/470b883072d48f1f2917a784b39d4327_150_0.png)

图 8-48

### 8.4.3 数据预处理

图片不能直接放入神经网络中进行学习，学习之前应该把数据格式化为经过预处理的浮点数张量。数据预处理大致分为 4 个步骤；

- 步骤 01 读取图像文件。
- 步骤 02 将 JPEG 文件解码为 RGB 像素网格。
- 步骤 03 将这些像素网格转换为浮点数张量。
- 步骤 04 将像素值 (0~255) 缩放到 [0,1] 区间（神经网络喜欢处理较小的数据输入值）。

这些处理步骤可能看起来有点多，但幸运的是，Keras 中的 keras.preprocessing.image 包含图像处理辅助工具 ImageDataGenerator 类，它可以快速创建 Python 生成器，能够将硬盘上的图像文件自动转换为预处理好的张量批量。程序代码如图 8-49 所示。

```
from keras.preprocessing.image import ImageDataGenerator
#图像像素的值域为0~255，缩放至0到1之间
train_datagen = ImageDataGenerator(rescale = 1. / 255)
test_datagen = ImageDataGenerator(rescale = 1. / 255)
#generator 实际上是将数据批量载入内存,被代码类似for in 的方式方便的访问
#使用flow_from_directory()方法可以实现一个针对图像batch的生成器
train_generator = train_datagen.flow_from_directory(
    train_dir, target_size=(150, 150), # 将所有图像大小调整为150*150
    batch_size=20,
    class_mode = 'binary') #因为使用了binary_crossentropy损失，所以需要使用二进制标签
validation_generator = test_datagen.flow_from_directory(
    validation_dir, target_size = (150, 150), batch_size = 20,
    class_mode = 'binary')

Found 3000 images belonging to 2 classes.
Found 1000 images belonging to 2 classes.
```

图 8-49

执行代码生成了由 150×150 的 RGB 图像 [形状为 (20, 150, 150, 3)] 与二进制标签 [形状为 (20, )] 组成的批量，每个批量中包含 20 个样本（批量大小）。

### 8.4.4 进行训练

搭建完卷积神经网络模型，就可以使用反向传播法进行训练。在训练模型之前，我们需要用 compile 方法对训练模型进行设置。如图 8-50 所示，使用交叉熵损失函数（binary_crossentropy）训练我们的模型，优化器算法使用 adam，评估指标使用 accuracy。

```
# 对训练模型进行设置
#使用adam优化器，使用交叉熵为损失函数
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
```

图 8-50

如图 8-51 所示，使用 fit_generator 方法来拟合数据（在生成器上的效果和 fit 函数一样），它的第一个参数应该是 Python 生成器。因为数据是不断生成的，所以 Keras 模型需要知道每一轮需要从生成器中抽取多少个样本，这就用到了 step_per_epoch 参数。从生成器中抽取 steps_per_epoch 个批量后（即运行了 steps_per_epoch 次梯度下降），拟合过程将进入下一个轮次。我们还可以向里面传入一个 validation_data 参数，这个参数可以是一个生成器，也可以是 NumPy 数组组成的元组，如果是生成器的话，还需要指定 validation_steps 参数用来说明需要从验证的生成器中抽取多少个批次用于评估。

```
train_history = model.fit_generator(train_generator, steps_per_epoch = 150,
    epochs = 30, validation_data = validation_generator,
    verbose=2, validation_steps = 100)
```

图 8-51

部分训练结果如图 8-52 所示。

```
Epoch 17/30
 - 469s - loss: 0.2059 - acc: 0.9170 - val_loss: 0.5607 - val_acc: 0.7700
Epoch 18/30
 - 476s - loss: 0.1914 - acc: 0.9277 - val_loss: 0.4539 - val_acc: 0.8320
Epoch 19/30
 - 466s - loss: 0.1632 - acc: 0.9390 - val_loss: 0.5326 - val_acc: 0.8070
Epoch 20/30
 - 463s - loss: 0.1442 - acc: 0.9467 - val_loss: 0.9897 - val_acc: 0.6970
Epoch 21/30
 - 481s - loss: 0.1206 - acc: 0.9543 - val_loss: 0.5666 - val_acc: 0.8190
Epoch 22/30
 - 489s - loss: 0.1098 - acc: 0.9620 - val_loss: 0.5575 - val_acc: 0.8190
Epoch 23/30
 - 467s - loss: 0.0863 - acc: 0.9743 - val_loss: 0.5466 - val_acc: 0.8330
Epoch 24/30
 - 467s - loss: 0.0839 - acc: 0.9707 - val_loss: 0.5636 - val_acc: 0.8330
Epoch 25/30
 - 483s - loss: 0.0644 - acc: 0.9793 - val_loss: 0.6034 - val_acc: 0.8290
Epoch 26/30
 - 498s - loss: 0.0556 - acc: 0.9823 - val_loss: 0.6696 - val_acc: 0.8240
Epoch 27/30
 - 520s - loss: 0.0496 - acc: 0.9857 - val_loss: 0.7360 - val_acc: 0.8120
Epoch 28/30
 - 569s - loss: 0.0452 - acc: 0.9857 - val_loss: 0.7220 - val_acc: 0.8300
Epoch 29/30
 - 553s - loss: 0.0336 - acc: 0.9913 - val_loss: 0.8264 - val_acc: 0.8040
Epoch 30/30
 - 557s - loss: 0.0325 - acc: 0.9907 - val_loss: 0.7702 - val_acc: 0.8250
```

图 8-52

### 8.4.5 模型保存和评估

在训练完成后保存模型，这是一种良好习惯，保存模型的代码如图 8-53 所示。接下来我们分别绘制训练过程中模型在训练数据和验证数据上的损失和准确率，程序代码如图 8-54 所示。

```
try:
    model.save('cats_and_dogs_cnn.h5')
    print('保存模型成功！')
except:
    print('保存模型失败！')
保存模型成功！
```

图 8-53

执行代码后，结果如图 8-55 所示。

```
import matplotlib.pyplot as plt
acc = train_history.history['acc']
val_acc = train_history.history['val_acc']
loss = train_history.history['loss']
val_loss = train_history.history['val_loss']
epochs = range(1, len(acc) + 1)
#绘制模型对训练数据和验证数据判断的准确率
plt.plot(epochs, acc, 'bo', label = 'trainning acc')
plt.plot(epochs, val_acc, 'b', label = 'validation acc')
plt.title('Trainning and validation accuary')
plt.legend()
plt.show()
plt.figure()
#绘制模型对训练数据和验证数据判断的错误率
plt.plot(epochs, loss, 'bo', label = 'Trainning loss')
plt.plot(epochs, val_loss, 'b', label = 'Validation loss')
plt.title('Trainning and validation loss')
plt.legend()
plt.show()
```

图 8-54

![](img/470b883072d48f1f2917a784b39d4327_152_0.png)

图 8-55
