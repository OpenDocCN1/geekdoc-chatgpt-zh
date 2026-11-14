
高级 API 接口。它类似前面所介绍的 TensorFlow.contrib.layers 模块，将很多常见的 TensorFlow 函数进行了二次封装，使代码变得更加简洁，特别适用于构建复杂结构的深度神经网络，它可以用来定义、训练和评估复杂的模型。

同时，在 TensorFlow 的 models 模块里又提供了大量用 slim 写好的网络模型结构代码，以及用该代码训练出的模型检查点文件，可以作为我们的预训练模型来使用。这些模型主要是与图片分类相关，包括 ResNet、VGG、Inception-ResNet-v2 等。下面就来详细了解下 slim。

## 11.5.1 获取 models 中的 slim 模块代码

为了能够使用 models 中的代码，需要先验证下我们的 TensorFlow 版本是否集成了 slim 模块，接着再从 GitHub 上将 models 代码下载下来，具体操作如下。

- 1. 验证slim库

在使用 slim 前，要测试本地的 tf.contrib.slim 模块是否有效。在命令行中输入如下命令：

```
python -c "import tensorflow.contrib.slim as slim;
eval = slim.evaluation.evaluate_once"
```

如果没有生成任何错误，则表明 TF-Slim 是可以工作的。

- 2. 下载models模块

接下来需要安装 TF-slim image models library。来到以下网址 https://github.com/tensorflow/models/，可以通过 Git 将代码复制下来，也可以手动下载下来（具体操作见 8.5.2 的详细介绍）。然后解压到本地 workspace 路径下（就是你自己建立的用来放个人 TF 代码的路径），通过下面的代码来验证它是否工作。

```
cd $workspace/models/research/slim
python -c "from nets import cifarnet; mynet = cifarnet.cifarnet"
```

将上面的$workspace 替换成你的工作路径（如笔者的是 d:\python）。运行时如果没有发生任何错误，则表明一切正常。

## 11.5.2 models 中的 slim 目录结构

在 models 下的 slim 中一共有 5 个文件夹。

- Datasets: 处理数据集相关的代码。
- Deployment: 部署。通过创建 clone 方式实现跨机器的分布训练，可以在多 CPU 和多 GPU 上实现运算的同步或异步。
- Nets: 该文件夹里放着各种网络模型。
- Preprocessing: 适用于各个网络的图片处理函数。
- Scripts: 运行网络模型的一些案例脚本，这些脚本只能在支持 shell 的系统下使用。

在这里重点介绍 Datasets、nets 和 Preprocessing 这 3 个文件夹。

## 1. Dataset——数据集处理模块

Dataset 里放着常用的图片训练数据集相关的代码。主要支持的数据集主要有 cifar10、flowers、mnist、imagenet。
代码文件的名字与数据集相对应，可以使用这些代码下载或获取数据集中的数据。以 imagenet 为例，可以使用如下函数从网上获取 imagenet 标签。

```
imagenet.create_readable_names_for_imagenet_labels()
```

上面代码返回的是 imagenet 中 1000 个类的分类标签名字（与样本序列对应）。

## 2. nets模块

nets 文件夹下包含前面介绍的各种网络模型，如图 11-8 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_421_0.png)

### 图 11-8 nets 文件结构

每个网络模型文件都是以自己的名字命名的，而且里面的代码结构框架也大致相同，以 inception_resnet_v2 为例，如表 11-1 中列出了比较常用的函数接口。

### 表 11-1 slim中nets的代码框架接口

| 操作 | 说明 |
| :--- | :--- |
| inception_resnet_v2.default_image_size | 默认图片大小 |
| inception_resnet_v2.inception_resnet_v2 | 同名的网络结构函数，这个函数有两个输出，一个是预测结果logits，另一个是辅助信息AuxLogits。辅助信息为了显示或分析使用，主要包括summaries或losses |
| inception_resnet_v2_arg_scope | 命名空间的名字。在外层修改或使用模型时，可以使用与模型相同的命名空间 |
| inception_resnet_v2_base | 为inception_resnet_v2的基本结构实现，输出inception_resnet_v2网络中最原始的数据，默认是传到inception_resnet_v2.inception_resnet_v2函数中，一般不会改动内部。当要使用自定义的输出层时，会将传入自己的函数来替换inception_resnet_v2.inception_resnet_v2 |

注意：表 11-1 中的框架全部是使用 slim 库代码来实现的，由于与 tensorflow.contrib.layers 模块的使用方式很相似，这里不再展开介绍，但是建议读者配合前面讲的各个模型的结构再看看其具体在代码中的真实实现，对自己构建高效的模型会有很大帮助。

## 3. Preprocessing模块

该模块代码里包含几个图片预处理文件，命名也是按照模型的名字来命名的。slim 会把某一类模型常用的预处理函数放到一个文件里，并命名为该类模型相关的名字，而且每个代码文件函数结构也大致相似。例如，调用 inception_preprocessing 函数中的代码如下：

```
inception_preprocessing.preprocess_image
```

该函数是将传入的图片转化成模型尺寸并归一化处理。

## 11.5.3 slim 中的数据集处理

slim 模块包自带了函数，可以用来下载数据集，也可以对数据集进行转换操作。它可以下载标准的数据集并转换为 TensorFlow 自带的 TFRecord 格式，还可以使用 TF-slim 的 data reading 和 queueing utilities 来读取 TFRecord 格式的数据集。slim 所支持的数据集如表 11-2 所示。

| 数据集 | 训练数据集大小 | 测试数据集大小 | 分类个数 | 备注 |
| :--- | :--- | :--- | :--- | :--- |
| Flowers | 2500 | 2500 | 5 | 尺寸可变 |
| Cifar10 | 60×1000 | 10×1000 | 10 | 32×32彩色图 |
| MNIST | 60×1000 | 10×1000 | 10 | 28×28灰度图 |
| ImageNet | 1.2×1000×1000 | 50×1000 | 1000 | 尺寸可变 |

## 1. 将数据转为TFRecord格式

TFRecord 是 TensorFlow 推荐的数据集格式，与 TensorFlow 框架结合紧密。在 TensorFlow 中提供了一系列接口可以访问 TFRecord 格式。该结构存在的意义主要是为了满足在处理海量样本集时，需要边执行训练边从硬盘上读取数据的需求。将原始文件转化成 TFRecord 的格式，然后在运行中通过多线程的方式来读取，这样可以减小主线程训练的负担，使整个训练过程变得更高效。

只需要在命令行里输入下列命令，即可将下载数据集并将其转成 TFRecord 格式：

```
D:\\python\\research\\models\\slim>python download_and_convert_data.py--dataset_name=flowers--dataset_dir=/tmp/data/flowers
```

这里需要指定两个关键点：一个是数据集（例子中的 flowers），另一个是下载路径（笔者的 Python 文件是在 D 盘，所以会下载到 D:\\tmp\\data\\flowers 下）。执行完后会看到下载的数据文件和生成的 TFRecord 文件，如图 11-9 所示。

这里包含 5 个训练数据文件、5 个验证数据文件及一个标签文件。标签文件定义了整数标签和分类名称。

如果想将其他数据集转成 TFRecord 格式，可以参考上面的代码实现，这里不再展开介绍。

同样，也可以按照这个方法下载 MNIST 和 cifar10 数据集。如果需要下载 imageNet 数据集，则需要在 image-net.org 中注册一个账号，然后再运行下载脚本，大概有 500GB，因此需要留出足够大的硬盘空间，并且下载时间会很长。

## 2. 处理slim数据集时的常见错误

由于有时网络有时会不稳定，因此使用上面讲的方法下载数据时往往会遇到如下错误，主要是由于没有下载完成的原因。

```
urllib.error.ContentTooShortError: <urlopen error retrieval incomplete:
got only
64456280 out of 228813984 bytes>
```

这表明由于网络原因数据包没有下载完整，有两种方法可以解决。

- 如果你的网速较快的话，可以多运行几次，总有一次可以成功。
- 可以将 http://download.tensorflow.org/example_images/ flower_photos.tgz 网址放到下载工具（如迅雷等）里自行下载。

解压后的路径如图 11-10 所示。

### 图 11-9 flowers 文件夹的 TFRecord 数据集

![](img/d841d305683d65f55b29f4e3f25e532c_423_0.png)

### 图 11-10 flowers 数据集

![](img/d841d305683d65f55b29f4e3f25e532c_423_1.png)

然后来到 download_and_convert_flowers.py 第 191 行，注释掉下列代码即可。

```
#dataset_utils.download_and_uncompress_tarball(_DATA_URL,dataset_dir)
```

本书配套的代码中会有实例文件（见“11.5.3 代码参考”文件夹）。

下载完成后，运行如下命令将刚下载的数据集转成 TFRecord 格式（以图 11-10 中的路径“/tmp/data/flowers”为例）：

```
D:\python\research\models\slim>python download_and_convert_data.py --dataset_name=flowers --dataset_dir=/tmp/data/flowers
```

## 11.5.4 实例 84: 利用 slim 读取 TFRecord 中的数据

TFRecord 文件创建好后，就可以读取文件中的数据了，本例将演示如何读取 TFRecord 中的数据，步骤如下。

### 实例描述

利用 slim 代码库里的函数读取 TFRecord 格式的数据并显示出来。

### 1. 定义slim数据集，创建provider

在图 11-9 中可以看到，有两个数据集 train 与 validation。在读取时，需要指定一个数据集然后创建 provider 对象，接着就可以从 provider 里读取数据了，代码如下：

### 代码11-1 tfrecordertest

```
01 import tensorflow as tf
02 from datasets import flowers
03 import pylab
04
05 slim = tf.contrib.slim
06
07 DATA_DIR="D:/own/python/flower_photos" #指定 flower 数据集的路径
08
09 #选择数据集 validation
10 dataset = flowers.get_split('validation', DATA_DIR)
11
12 #创建一个 provider
13 provider = slim.dataset_data_provider.DatasetDataProvider(dataset)
14 #通过 provider 的 get 获得一条样本数据
15 [image, label] = provider.get(['image', 'label'])
16 print(image.shape)
```

上述代码中，先引入头文件，然后创建 provider，通过 get 来获得 image 与 label 两个张量。这时并没有真的读到数据，只是一个构建图的过程。具体取数据则要通过 session 中启动队列线程后才可以。

provider 是使用 DatasetDataProvider 类的实例化实现的，在 DatasetDataProvider 类中还可以有更多的设置：

```
class DatasetDataProvider(data_provider.DataProvider):
def __init__(self,
	dataset,
	num_readers=1,
	reader_kwargs=None,
	shuffle=True,
	num_epochs=None,
	common_queue_capacity=256,
	common_queue_min=128,
	record_key='record_key',
	seed=None,
	scope=None):
```

必选参数是传入指定的数据集 dataset，其他还包括指定几个并行读取器来读取数据 num_readers、是否打乱顺序 shuffle、指定数据源读取的循环次数 num_epochs（None 表示无限循环）、队列大小 common_queue_capacity 等。没有特殊要求的情况下，直接默认即可。

> 注意：本例演示的是只读取一条样本，在训练中需要按批次读取指定数量的样本，这时会需要配合 tf.train.batch 一起使用，tf.train.batch 有个条件就是必须指定样本的固定大小，所以在传入时需要将变长的图片按固定大小调整。在 slim 的训练模型代码里有使用的例子，读者可以自己参考。另外，在第 12 章对抗神经网络里，超分辨率部分也有实例供读者学习、参考。

### 2. 启用session读取数据

在 session 中初始化变量之后，需要通过 tf.train.start_queue_runners 来启动队列线程。这时会有一个线程专门负责从磁盘里读图片数据，接着通过 run 来运行图节点 image 与 label 得到真实的数据。

### 代码11-1 tfrecordertest（续）

```
17 sess = tf.InteractiveSession()
18 tf.global_variables_initializer().run()
19 #启动队列
20 tf.train.start_queue_runners()
21 #获取数据
22 image_batch, label_batch = sess.run([image, label])
23 #显示
24 print (label_batch)
25 pylab.imshow(image_batch)
26 pylab.show()
```

### 图 11-11 TFRecord 例子

![](img/d841d305683d65f55b29f4e3f25e532c_425_0.png)

运行上述代码，输出的图片如图 11-11 所示。

(?, ?, 3)
1

多运行几次发现，每次的结果都不一样，再次证实了默认是随机读取的。

> 注意：在处理大数据样本时，将数据转成 TFRecord 后使用线程来读取，是一个较常规的方式。千万不能像 MNIST 数据集读取那样一次都读入内存，内存会被样本耗尽，系统就无法处理其他的数据了。另外，除了使用 TFRecord 方式以外，还可以从 filenames 中读取，通过异步读取文件，然后按批次的随机抽取指定样本数量，再输入到模型中来做模型参数的更新。

## 11.5.5 在 slim 中训练模型

slim 提供了很多便捷的方式，前面提到的全部模型在 slim 中都可以找到对应的代码实现。不仅如此，slim 还共享了模型的训练代码，使用者不再需要关注模型代码，只需通过命令行方式即可完成训练、微调、测试等任务，大大方便了模型的产出。

对于 linux 用户，在 slim 的 Scripts 文件夹下还提供了模型下载、训练、预训练、微调、测试等一条龙的完整 shell 脚本。如果你用的是 Windows，也可以在命令行下一条一条地复制命令并执行命令。

关于 shell 脚本代码，不再逐条解释，下面举例演示使用命令行来训练模型的相关操作。

### 1. 从头训练

训练模型的代码被放在 slim 下的 train_image_classifier.py 文件里，这里用 flower 数据集来训练 Inception_v3 网络结构的深度神经网络模型。在命令行中执行如下命令：

```
D:\python\research\models\slim>python train_image_classifier.py --train_dir=in3flower --dataset_name=flowers --dataset_split_name=train --dataset_dir=/tmp/data/flowers/flower_photos --model_name=inception_v3
```

参数说明如下。

- train_dir: 是要生成模型的路径。
- dataset_name: 数据集名字。
- dataset_split_name: 数据集中的哪一部分是 validation 还是 train。
- dataset_dir: 数据集路径。
- model_name: 模型名字。

这里只列出了主要的参数，其他的参数可以仿照 shell 脚本中的例子，如果读者想知道全部的参数，可参看 train_image_classifier.py 文件。也可以修改 train_image_classifier.py 文件，添加自己喜欢的参数。

> 注意： dataset_name、dataset_split_name、model_name 的名字不是随意命名的，必须与代码中的名字对应。如果使用自己的数据集，则需要在 slim 中的 dataset 文件夹下仿照其他的数据集加一个.py 文件，然后也可以用 train_image_classifier.py 来运行。当然读者也可以不使用 train_image_classifier.py，直接自己编写代码载入数据集。
