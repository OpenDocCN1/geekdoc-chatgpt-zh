
从图中我们可以看到出现了过拟合的现象，训练准确率随着时间线性增加，而验证准确率则基本停留在 70%~72%，验证损失在 5 轮后就达到最小值，然后保持不变，而训练损失则一直线性下降，直到接近于 0。

因为训练样本相对来说比较少（3000 个），所以过拟合是一个十分重要的问题。过拟合的原因是学习样本太少，导致无法训练出能够泛化到新数据的模型。如果拥有大量的数据，那么模型就能观察到数据分布的所有内容，这样就不会出现过拟合。

## 8.4.6 进行预测

调用训练的模型，对猫和狗的图片进行预测。预测时的图片大小需要统一为模型建立时训练集的大小，否则会因为输入图片不符合模型要求而出错。由于示例模型构建时样本大小统一为 150×150，故在预测时对图片大小也做了相应调整。预测猫、狗图片的代码如下：

```python
import os
from keras.models import load_model
from keras.preprocessing import image
import matplotlib.pyplot as plt
import numpy as np
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'
# 单幅图片的识别
model = load_model('cats_and_dogs_cnn.h5')
filename='CatorDog.jpg'
img = image.load_img(filename, target_size=(150, 150))
plt.imshow(img)
plt.show()
# 将图片转化为 4d tensor 形式
x = image.img_to_array(img)
print(x.shape) #(224, 224, 3)
x = np.expand_dims(x, axis=0)
print(x.shape) #(1, 224, 224, 3)
pres = model.predict(x)
print(int(pres[0][0]))
if int(pres[0][0]) > 0.5:
    print('识别的结果是狗')
else:
    print('识别的结果是猫')
########################################
```

运行代码，识别结果如图 8-56 所示。

![](img/470b883072d48f1f2917a784b39d4327_154_0.png)

图 8-56

## 8.4.7 模型的改进优化

首先，在 3000 个训练样本上训练一个简单的小型卷积神经网络，不做任何正则化，此时主要的问题在于过拟合。我们改进优化方法，采用数据增强（Data Augmentation）的方法来降低过拟合。数据增强是针对计算机视觉处理过拟合的新方法，在计算机视觉领域是一种非常强大的降低过拟合的技术，在深度学习处理图像时几乎都会用到。数据增强的原理是从现有的训练样本中生成更多的训练数据，其方法是利用多种能够生成可信图像的随机变换来增加样本，其目标是让模型在训练时不会两次查看完全相同的图像。这让模型能够观察到数据的更多内容，从而具有更好的泛化能力。

在 Keras 中，我们可以通过对 ImageDataGenerator 实例读取的图像执行多次随机变换，来实现数据增强。

示例代码如下：

```python
from keras.preprocessing.image import ImageDataGenerator
# 使用数据增强，对 ImageDataGenerator 实例读取的图像执行多次随机变换来实现
train_datagen = ImageDataGenerator(
    rescale=1./255,
    rotation_range=40,
    width_shift_range=0.2,
    height_shift_range=0.2,
    shear_range=0.2,
    zoom_range=0.2,
    horizontal_flip=True,)
```

ImageDataGenerator 中几个参数的含义如下：

- rescale: 将像素值缩放，这里是将像素值（0~255）缩放到 [0, 1] 区间。
- rotation_range: 角度值（0~180），表示图像随机旋转的角度范围。
- width_shift_range 和 height_shift_range: 图像在水平或垂直方向上平移的范围（相对于总宽度或总高度的比例）。
- shear_range: 随机错切变换的角度。
- zoom_range: 图像随机缩放的范围。
- horizontal_flip: 随机将一半图像水平翻转。如果没有水平不对称的假设（比如真实世界的图像），这种做法是有意义的。

完整的程序代码如下：

```python
######################################
import os
base_dir = 'cat-and-dog'
# 构造路径存储训练数据、校验数据以及测试数据
train_dir = os.path.join(base_dir, 'training_set')
os.makedirs(train_dir, exist_ok = True)
validation_dir = os.path.join(base_dir, 'validation_set')
os.makedirs(validation_dir, exist_ok = True)
train_cats_dir = os.path.join(train_dir, 'cats')
train_dogs_dir = os.path.join(train_dir, 'dogs')
validation_cats_dir = os.path.join(validation_dir, 'cats')
validation_dogs_dir = os.path.join(validation_dir, 'dogs')
# 检查一下每个分组（训练 / 验证 ）中分别包含多少幅图像
print('total training cat images: ', len(os.listdir(train_cats_dir)))
print('total training dog images: ', len(os.listdir(train_dogs_dir)))
print('total validation cat images: ', len(os.listdir(validation_cats_dir)))
print('total validation dog images: ', len(os.listdir(validation_dogs_dir)))
from keras import layers
from keras import models
model = models.Sequential()
model.add(layers.Conv2D(32, (3, 3), activation='relu',
                input_shape=(150, 150, 3)))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(128, (3, 3), activation='relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(128, (3, 3), activation='relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Flatten())
model.add(layers.Dropout(0.5))
model.add(layers.Dense(512, activation='relu'))
model.add(layers.Dense(1, activation='sigmoid'))
print(model.summary())
from keras import optimizers
model.compile(loss='binary_crossentropy', optimizer=optimizers.RMSprop(lr=1e-4),
metrics=['acc'])
from keras.preprocessing.image import ImageDataGenerator
train_datagen = ImageDataGenerator(
    rescale=1./255,
    rotation_range=40,
    width_shift_range=0.2,
    height_shift_range=0.2,
    shear_range=0.2,
    zoom_range=0.2,
    horizontal_flip=True,)
#Note that the validation data should not be augmented! (注意不能增强验证数据)
test_datagen = ImageDataGenerator(rescale = 1. / 255)
train_generator = train_datagen.flow_from_directory(
    train_dir, target_size=(150, 150), # 将所有图像大小调整为150*150
    batch_size=20,
    class_mode = 'binary')
validation_generator = test_datagen.flow_from_directory(
    validation_dir,target_size = (150, 150),batch_size = 20,
    class_mode = 'binary')
train_history = model.fit_generator(train_generator,
                                    steps_per_epoch = 150,
                                    epochs = 30,
                                    validation_data = validation_generator,
                                    verbose=2,
                                    validation_steps = 100)
try:
    model.save('cats_and_dogs_cnn2.h5')
    print('保存模型成功！')
except:
    print('保存模型失败！')
import matplotlib.pyplot as plt
acc = train_history.history['acc']
val_acc = train_history.history['val_acc']
loss = train_history.history['loss']
val_loss = train_history.history['val_loss']
epochs = range(1, len(acc) + 1)
# 绘制模型对训练数据和校验数据判断的准确率
plt.plot(epochs, acc, 'bo', label = 'training acc')
plt.plot(epochs, val_acc, 'b', label = 'validation acc')
plt.title('Training and validation accuracy')
plt.legend()
plt.show()
plt.figure()
# 绘制模型对训练数据和校验数据判断的错误率
plt.plot(epochs, loss, 'bo', label = 'Training loss')
plt.plot(epochs, val_loss, 'b', label = 'Validation loss')
plt.title('Training and validation loss')
plt.legend()
plt.show()
################################
```

运行程序代码，这个卷积神经网络模型如图 8-57 所示。代码中 Dropout(0.5) 的功能是，每次训练迭代时会随机地在神经网络中放弃 50% 的神经元，以避免过拟合。
部分训练结果如图 8-58 所示。

| Layer (type) | Output Shape | Param # |
|---|---|---|
| conv2d_1 (Conv2D) | (None, 148, 148, 32) | 896 |
| max_pooling2d_1 (MaxPooling2D) | (None, 74, 74, 32) | 0 |
| conv2d_2 (Conv2D) | (None, 72, 72, 64) | 18496 |
| max_pooling2d_2 (MaxPooling2D) | (None, 36, 36, 64) | 0 |
| conv2d_3 (Conv2D) | (None, 34, 34, 128) | 73856 |
| max_pooling2d_3 (MaxPooling2D) | (None, 17, 17, 128) | 0 |
| conv2d_4 (Conv2D) | (None, 15, 15, 128) | 147584 |
| max_pooling2d_4 (MaxPooling2D) | (None, 7, 7, 128) | 0 |
| flatten_1 (Flatten) | (None, 6272) | 0 |
| dropout_1 (Dropout) | (None, 6272) | 0 |
| dense_1 (Dense) | (None, 512) | 3211776 |
| dense_2 (Dense) | (None, 1) | 513 |
| Total params: 3,453,121 | Trainable params: 3,453,121 | Non-trainable params: 0 |

图 8-57

```
Epoch 16/30
- 476s - loss: 0.5105 - acc: 0.7440 - val_loss: 0.4577 - val_acc: 0.7940
Epoch 17/30
- 473s - loss: 0.5131 - acc: 0.7437 - val_loss: 0.4411 - val_acc: 0.7940
Epoch 18/30
- 470s - loss: 0.5094 - acc: 0.7520 - val_loss: 0.4387 - val_acc: 0.7840
Epoch 19/30
- 466s - loss: 0.5031 - acc: 0.7480 - val_loss: 0.4511 - val_acc: 0.8080
Epoch 20/30
- 464s - loss: 0.5053 - acc: 0.7520 - val_loss: 0.4514 - val_acc: 0.7880
Epoch 21/30
- 497s - loss: 0.4959 - acc: 0.7553 - val_loss: 0.4305 - val_acc: 0.8110
Epoch 22/30
- 473s - loss: 0.4910 - acc: 0.7607 - val_loss: 0.4176 - val_acc: 0.8160
Epoch 23/30
- 467s - loss: 0.4832 - acc: 0.7647 - val_loss: 0.4234 - val_acc: 0.8140
Epoch 24/30
- 469s - loss: 0.4911 - acc: 0.7687 - val_loss: 0.4122 - val_acc: 0.8230
Epoch 25/30
- 492s - loss: 0.4898 - acc: 0.7633 - val_loss: 0.4306 - val_acc: 0.8070
Epoch 26/30
- 502s - loss: 0.4989 - acc: 0.7527 - val_loss: 0.4135 - val_acc: 0.8170
Epoch 27/30
- 544s - loss: 0.4908 - acc: 0.7647 - val_loss: 0.4203 - val_acc: 0.8150
Epoch 28/30
- 556s - loss: 0.4823 - acc: 0.7723 - val_loss: 0.4462 - val_acc: 0.7770
Epoch 29/30
- 548s - loss: 0.4808 - acc: 0.7737 - val_loss: 0.4339 - val_acc: 0.8130
Epoch 30/30
- 461s - loss: 0.4748 - acc: 0.7830 - val_loss: 0.4199 - val_acc: 0.7990
```

图 8-58

绘制训练过程中模型在训练数据和验证数据上的损失和准确率，如图 8-59 所示。

我们可以发现，使用数据增强和 Dropout 之后，模型过拟合情况大大减少，比之前没有使用正则化和数据增强时的效果要好。

![](img/470b883072d48f1f2917a784b39d4327_157_0.png)

图 8-59

## 第 9 章

## IMDB 电影评论情感分析

情感分析是自然语言处理中很重要一个方向，目的是让计算机理解文本中包含的情感信息。情感分析有很多的应用场景，比如做一个电商网站，卖家需要时刻关心用户对于商品的评论是否是正面的。再比如做一个电影的宣传和策划，电影在观众中的口碑也至关重要。互联网上任何一个事件或物品，都有可能产生成千上万的文本评论，如何定义每一个文本的情绪是正面还是负面的，是一件很有挑战的事情。本章将通过 IMDB（Internet Movie Database，互联网电影资料库）收集的电影评论数据集，分析某部电影是一部受到好评的电影还是一部受到差评的电影，借此研究情感分析问题。

## 9.1 IMDB 电影数据集和影评文字处理介绍

IMDB 是一个与电影相关的在线数据库，它包含了 25000 部电影的评价信息，该数据库是由斯坦福大学的研究院整理的。

IMDB 电影数据集含有 50000 个电影评论，训练数据与测试数据各 25000 项，每一项电影评论都被标记为“正面评价”和“负面评价”两类。我们希望建立一个模型，在经过大量的电影评论文字训练后，这个模型可以用来预测电影评论是正面评价或是负面评价。

因为深度学习模型是无法处理文字的，必须将文字转换成可以计算的数字，所以需要将“电影评论文字”转换成“数字列表”并建立一一对应关系。

比如，提取最常用的前 2000 个高频词语建立 token 字典，依照英文单词在所有影评中出现的次数进行排序，排序前 2000 名的英文单词会列入字典中，建立的字典如图 9-1 所示。

```
'the': 1, 'and': 2, 'a': 3, 'of': 4, 'to': 5, 'is': 6, 'in': 7, 'it': 8, 'i': 9, 'this': 10, 'that': 11, 'was': 12, 'as': 13, 'for': 14,
'with': 15, 'movie': 16, 'but': 17, 'film': 18, 'on': 19, 'not': 20, 'you': 21, 'are': 22, 'his': 23, 'have': 24, 'be': 25, 'he': 26, 'one': 27, 'all': 28, 'at': 29, 'by': 30, 'an': 31, 'they': 32, 'who': 33, 'so': 34, 'from': 35, 'like': 36, 'her': 37, 'or': 38, 'just': 39,
'about': 40, 'it\'s': 41, 'out': 42, 'has': 43, 'if': 44, 'some': 45, 'there': 46, 'what': 47, 'good': 48, 'more': 49, 'when': 50, 'very': 51, 'up': 52, 'no': 53, 'time': 54, 'she': 55, 'even': 56, 'my': 57, 'would': 58, 'which': 59, 'only': 60, 'story': 61, 'really': 62, 'see': 63, 'their': 64, 'had': 65, 'can': 66, 'were': 67, 'me': 68, 'well': 69, 'than': 70, 'we': 71, 'such': 72, 'been': 73, 'get': 74, 'bad': 75, 'will': 76, 'also': 77, 'do': 78, 'into': 79, 'people': 80, 'other': 81, 'first': 82, 'because': 83, 'great': 84, 'how': 85, 'him': 86, 'most': 87, 'don\'t': 88, 'made': 89, 'its': 90, 'then': 91, 'way': 92, 'make': 93, 'them': 94, 'too': 95, 'could': 96, 'any': 97, 'movies': 98, 'after': 99, 'think': 100, 'characters': 101, 'watch': 102, 'two': 103, 'films': 104, 'character': 105, 'seen': 106, 'many':
```

图 9-1

如果单词不在字典中，那这个单词就不用转换，我们只在乎在“电影评论文字”常用字典中出现的单词，因为最常用的单词对情感分析是最为重要的。
