本书配套的代码中会有实例文件（见“11.5.3 代码参考”文件夹）。

下载完成后，运行如下命令将刚下载的数据集转成 TFRecord 格式（以图 11-10 中的路径“/tmp/data/flowers”为例）：

```
D:\python\research\models\slim>python download_and_convert_data.py --dataset_name=flowers --dataset_dir=/tmp/data/flowers
```

# 11.5.4 实例 84: 利用 slim 读取 TFRecord 中的数据

TFRecord 文件创建好后，就可以读取文件中的数据了，本例将演示如何读取 TFRecord 中的数据，步骤如下。

# 实例描述

利用 slim 代码库里的函数读取 TFRecord 格式的数据并显示出来。

# 1. 定义slim数据集，创建provider

在图 11-9 中可以看到，有两个数据集 train 与 validation。在读取时，需要指定一个数据集然后创建 provider 对象，接着就可以从 provider 里读取数据了，代码如下：

# 代码11-1 tfrecodertest

```
01 import tensorflow as tf
02 from datasets import flowers
03 import pylab
04
05 slim = tf.contrib.slim
06
07 DATA_DIR="D:/own/python/flower_photosos" #指定 flower 数据集的路径
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
```