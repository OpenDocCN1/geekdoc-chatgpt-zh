
```
 1 import os
 2 
 3 import shutilimport random
 4 
 5 train= '../data/dog_cat/train/'
 6 
 7 dogs=[train + i for i in os.listdir(train) if 'dog'in i]
 8 cats=[train + i for i in os.listdir(train)] if 'cat' in i]
 9 
10 print len(dogs)，len(cats)
11 
12 
13 target = '../data/dog_cat/arrange/'
14 
15 random.shuffle(dogs)
16 random.shuffle(cats)
17 
18 def ensure_dir(dir_path) :
19   if not os.path.exists(dir_path):
20   try:
21     os.makedirs(dir_path)
22   except OSError:
23     pass
24 
25 ensure_dir(target + 'train/dog')
26 ensure_dir(target + 'train/cat')
27 ensure_dir(target + 'validation/dog')
28 ensure_dir(target + 'validation/cat')
29 
30 for dog_file, cat_file in zip(dogs, cats)[:1000]:
31   shutil.copyfile(dog_file, target + 'train/dog/' + os.path.basename (dog_file))
32   shutil.copyfile(cat_file, target + 'train/cat/' + os.path.basename (cat_file))
33 
34 for dog_file, cat_f1le in zip(dogs, cats)[1000:1500]:
35   shutil.copyfile(dog_file, target + 'validation/dog/' + os.path.basename(dog_file))
36   shutil.copyfile(cat_file, target + 'validation/cat/' + os.path.basename(cat_file))
```

代码中首先读取狗与猫图片的训练集，然后调用`ensure_dir`确保图片目录路径都存在，接着遍历训练集，调用`copyfile`将文件复制到训练集目录和测试集目录中。然后完成数据收集。

### 8.2.4 数据预处理

数据集准备完毕，接下来预处理样本。这里我们将用一些图片变形手段，将原始样本变形。在提升数据丰富度的同时，通过训练学习变形事物让模型提取出更抽象的事物特征。生成变形图片样本代码如代码清单8-2所示。

#### 代码清单8-2 图片预处理代码
```
 1 from keras.preprocessing.image import ImageDataGenerator
 2 
 3 img_width, img_height = 128，128
 4 input_shape = (img, width, 1img height, 3)
 5 
 6 train_data_dir = target + 'train'
 7 validation_data_dir = target + 'validation'
 8 
 9 train_pic_gen = ImageDataGenerator(
10       rescale=1. / 255,
11       rotation_range=20,
12       width_shift_range=0.2,
13       height_shift_range=0.2,
14       shear_range=0.2,
15       zoom_range=0.5，
16       horizontal_flip=True,
17       fill_mode='nearest')
18 
19 validation_pic_gen = ImageDataGenerator(rescale=1, / 255)
```

接下来生成数据流。由于这里是二分类问题，标签非0即1。所以不需要`Keras`当中的`np.utils.to_categorical`函数对样本标签进行`One-Hot`编码，我们现在只需要按文件夹生成训练集流和标签，进行二分类即可，如代码清单8-3所示。

#### 代码清单8-3 数据流生成代码
```
 1 train_flow= train_pic_gen.flow_from_directory(
 2       train_data_dir,
 3       target_size=(img_width, img_height),
 4       batch_size=32，
 5       class_mode='binary'
 6 )
 7 
 8 validation_flow = validation_pic_gen.flow_from_directory(
 9       validation_data_dir,
10       target_size=(img_width, img_height),
11       batch_size=32，
12       class_mode='binary'
13 )
14
```

第1行，调用`train_pic_gen`的`flow_from_directory`生成训练数据流，数据目录是`train_data_dir`，数据大小为图片的宽度和高度，图片的批次数量是32。

第8行，调用`validation_pic_gen`的`flow_from_directory`生成验证数据流，数据目录是`validation_data_dir`，数据大小为图片的宽度和高度，图片的批次数量是32。

### 8.2.5 数据训练

数据预处理结束后我们需要准备训练模型，定义模型我们已经轻车熟路了，只需要如代码清单8-4即可。

#### 代码清单8-4 模型定义
```
 1 from keras.models import Sequential
 2 from keras:layers import Convolution2D, MaxPooling2D
 3 from keras.layers import Activation, Dropout, Flatten, Dense
 4 
 5 nb_train_samples = 2000
 6 nb_validation_samples = 1000
 7 nb_epoch = 50
 8 
 9 model = Sequential([
10     Convolution2D(
11         32, 3, 3, input_shape=input_shape,
12         activation=' relu'),
13     MaxPooling2D(pool_size=(2, 2)),
14     Convolution2D(64, 3，3, activation='relu'),
15     MaxPooling2D(pool size=(2, 21)),
16     Flatten(),
17     Dense(64，activation='relu'),
18     Dropout(0.51),
19     Dense(1, activation='sigmoid'),
20 ])
21 
22 model.compile(Loss='binary_crossentropy', optimizer='rmsprop', metrics=[' accuracy'])
23 
24 model.fit_generator(
25     train_flow,
26     samples_per_epoch=nb_train_samples,
27     nb_epoch=nb_epoch,
28     validation_data=validation_flow,
29     nb_val_samples=nb_validation_samples)
30 
31 ensure_dir(target + 'weights')
32 model.save_weights(target + 'weights/' + '1.h5')
```

第9～20行，调用`Sequential`创建一个网络，网络层依次为卷积层（`Convolution2D`）、最大池化层（`MaxPooling2D`）、卷积层、最大池化层，然后利用`Flatten`将数据变成平坦向量，接着是全连接层、`Dropout`层（随机丢弃参数）和全连接层。所有的激活函数使用的都是`ReLU`。

第22～29行，执行模型训练。这里我们选择二元交叉熵作为损失函数，使用`rmsprop`作为参数优化器，使用准确度作为模型效果的衡量标准。训练循环次数为`nb_epoch`次。

第31～32行，将模型参数保存到文件中，文件名是`1.h5`。

这样我们就可以得到一个保存的模型，模型保存在`weights`目录下，文件名为`1.h5`。这么一个简单的模型的准确率可以在90%以上。

### 8.3 目标识别与物体检测

图像分类只能解决一张图片是什么的问题，如果仅仅做图像分类是远远不够的，比如我们经常希望从下面的图片中知道到底有哪些物体，如图8-7所示。

![000](OEBPS/Images/207-1.jpg)

图8-7 目标检测示意图

这个时候就需要知道想要识别的物体在哪个位置，这就是所谓的目标检测。检测之后再通过分类器判定这个物体到底是哪个物体。我们已经知道了图像分类，下面将会具体介绍目标识别与其相关算法。

#### 8.3.1 目标识别简介

目标识别检测是目前深度学习在模式识别中应用的重要任务。这里我们需要介绍一些相关的概念以及相关的算法与实现。

目前可以将现有的基于深度学习的目标检测与识别算法大致分为以下3大类。

1）基于区域建议的目标检测与识别算法，如`R-CNN`、`Fast R-CNN`、`Faster R-CNN`等。

2）基于回归的目标检测与识别算法，如`YOLO`、`SSD`等。

3）基于搜索的目标检测与识别算法，如基于视觉注意的`AttentionNet`，基于强化学习的算法等。

目标识别主要有以下几个应用场景。

1）安全领域：指纹识别、人脸识别、地形勘察、飞行物识别等。

2）交通领域：车牌号识别、无人驾驶、交通标志识别等。

3）医疗领域：心电图、B超、健康管理、营养学等。

4）生活领域：智能家居、购物、智能测肤等。

我们可以看到目标识别的应用领域非常广阔，这也是越来越多的工程师开始学习深度学习模式识别的一个重要原因。

我们了解了目标识别大致完成的任务后，接下来就开始介绍一些流行的目标检测算法。

#### 8.3.2 R-CNN

`R-CNN`是最早的基于`Anchor`的目标检测算法，该算法在论文《Rich feature hierarchies for accurate oject detection and semantic segmentation》[4]中提出。

图8-8大致描述了`R-CNN`的检测过程，我们可以清晰地了解到深度学习模型是如何解决目标识别问题的，这张图来自于`R-CNN`的论文，我们此处直接引用。

![000](OEBPS/Images/208-1.jpg)

图8-8 `R-CNN`基本流程图

如图8-8所示，`R-CNN`的基本工作流程基本如下所示。

1）接收一个图像，使用`Selective Search`选择大约2000个从上到下的类无关的候选区域（proposal）。

2）将提取出来的候选区域转换为统一大小的图片（拉升/压缩等方法），使用`CNN`模型提取每一个候选区域的固定长度的特征。

3）使用特定类别的线性`SVM`分类器对每一个候选区域进行分类。

4）`Bounding Box`回归。

可以看出来，其实这就是一个基于滑动窗口的思路，目的是找出图中属于我们想要寻找的目标的小方框区域，在得到所有的方框区域后通过回归得到最后的物体位置。这也就是为什么检测结果都是矩形框的原因。

这样我们就知道`R-CNN`要如何处理训练和预测问题了。训练的过程基本如下所示。

1）预训练：预训练`CNN`（边界框标签不可用于该数据）。

2）特征领域的微调：使用基于`CNN`的`SGD`的训练，对模型进行微调。在这里选择学习率为预训练的。

将所有候选区域与真实框重叠（`IoU`）大于等于0.5的作为该框类的正例，其余的作为负例，再进行`SVM`分类。这个表明了训练过程是需要`Ground Truth`（标定框）的，是有监督的过程的。

预测过程和训练过程基本相同，仅有的差别就是预测不需要初始给定的标定框，另外就是预测之后得到的是一堆小方框，需要利用`Bounding Box`回归得到最后的检测框。简单来说，预测的过程就是根据在训练过程中找到的`CNN`回归值与所要预测的`Ground Truth`之间的关系，反向推导`Ground Truth`的位置。

因此我们可以看出`R-CNN`的一些优点和缺点。

`R-CNN`的优点主要是相对于传统的检测识别算法而言的，具体以下几点。

1）使用了`Select Search`进行proposal的选择，将proposal的数量从百万级别减少到了2000个左右，极大地减少了proposal的数量。

2）深度学习提取特征来代替人为设计，较大地提高了精度和效率。

3）使用了`Bounding Box`回归，进一步提高了检测的精度。

`R-CNN`具体的缺点有以下几点。

1）训练分为多个步骤，包括`Select Search`进行proposal的选择、`CNN`的模型训练（模型的预训练和微调）、`SVM`的分类、`Bounding Box`回归等，整个过程需要的时间过长。

2）由于当时的历史等各个因素的影响，使用了`SVM`进行多类别分类，要训练多个分类器，训练时间较长。

3）测试时间长，原因是每张图片要处理大量的目标候选框。

虽然`R-CNN`仍然存在很多问题，但是它打破了传统的目标识别方式，是第一个用于目标识别的深度学习模型，基于深度神经网络的目标识别技术也由此发展起来了。`R-CNN`的实现我们在9.4节的检测识别实战中再进行详细讨论。下面我们再介绍几种常用的检测算法。

#### 8.3.3 SPP-Net

在`RetinaNet`之前，`Faster R-CNN`是用得最多的目标检测方法，为了更加深刻理解`Faster R-CNN`我们先来介绍一下`SPP-Net`这个模型。

`SPP-Net`是何凯明在论文《Spatial Pyramid Pooling in Deep Convolutional Networks for Visual Recognition》[5]中提出的。

`SPP-Net`的创新之处主要体现在两点：一点是结合金字塔的思想，实现了`CNN`的多尺寸输入，解决了因为`CNN`对输入的格式要求进行预处理（如crop、warp等）操作造成的数据信息的丢失问题，另一点是只对原图进行一次卷积操作。

第1个创新点是`SPP-Net`的金字塔池化，如图8-9所示。

![000](OEBPS/Images/210-1.jpg)

图8-9 金字塔池化示意图

如图8-9所示，输入图片经过多个卷积层操作，再将输出的`FeatureMap`输入到`SPP-Net`池化层，最后将池化后的特征输入全连接层。下面针对图8-9来说说`SPP-Net`池化层的思想。

我们使用3层的金字塔池化层pooling，分别设置图片切分成多少块，论文中设置的分别是（1，4，16），然后按照层次对这个`FeatureMap` A进行分别处理（用代码实现就是for(1，2，3 layer)），也就是在第1层对这个`FeatureMap` A整个`FeatureMap`进行池化（池化又分为：最大池化，平均池化，随机池化），论文中使用的是最大池化，得到1个特征。

第2层先将这个`FeatureMap` A切分为4个（20，30）小的`FeatureMap`，然后使用对应大小的池化核对其进行池化后，得到4个特征，

第3层先将这个`FeatureMap` A切分为16个（10，15）的小的`FeatureMap`，然后使用对应大小的池化核对其进行池化后，得到16个特征.

最后将这1 + 4 + 16 = 21个特征输入到全连接层，进行权重计算。当然了，这个层数是可以随意设定的，这个图片划分也是可以随意的，只要效果好并且最后能组合成我们需要的特征个数即可。

第2个创新点是`SPP-Net`的一次卷积。

由于`R-CNN`先获取proposal，再进行resize，最后输入`CNN`卷积，这样做效率很低。`SPP-Net`针对这一缺点，提出了只进行一次原图的卷积操作，得到`FeatureMap`，然后找到每一个proposal在`FeatureMap`上对应的patch，将这个patch作为每个proposal的卷积特征输入到`SPP-Net`中，然后进行后续运算，速度可以提升百倍。

#### 8.3.4 Fast R-CNN

`Fast R-CNN`则在`SPP-Net`上更进一步，它更好地实现了对`R-CNN`的加速工作，`R-CNN`的基础上`Fast R-CNN`主要有以下几个方面的改进。

1）借鉴了`SPP-Net`的思路，提出了简化版的`ROI`池化层（没有使用金字塔），同时加入了候选框映射的功能，使得网络能够进行反向传播，解决了`SPP`的整体网络训练的问题。

2）多任务`Loss`层：一方面使用了`Softmax`代替`SVM`进行多分类，另一方面我们使用`SmoothL1Loss`取代了`Bounding Box`回归。

`Fast R-CNN`的基本工作流程如下所示。

1）接收一个图像，使用`Selective Search`选择大约2000个从上到下的类无关候选区域（proposal）。

2）对整张图片进行卷积操作提取特征，得到`FeatureMap`。

3）找到每个候选框在`FeatureMap`中的映射patch，将patch作为每个候选框的特征输入到`ROI`池化层及后面的层。

4）将提取出的候选框的特征输入到`Softmax`分类器中进行分类，替换了`R-CNN`的`SVM`分类。
