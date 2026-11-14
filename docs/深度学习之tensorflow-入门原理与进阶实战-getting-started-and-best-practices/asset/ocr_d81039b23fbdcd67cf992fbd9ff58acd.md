# 2. 引入头文件，指定模型

类似实例 85，在 slim 文件夹夹下创建代码文件。导入 nets 中的 VGG 模块，同时导入像素均值处理函数 mean_image_subtraction，导入 Datasets 中 imagenet 的 label 标签，令 slim = tf.contrib.slim。

# 代码11-3 vgg19图片检测使用

```
01 import numpy as np
02 import os
03 import tensorflow as tf
04
05 from PIL import Image
06 from datasets import imagenet
07 from nets import vgg
08 # 加载像素均值相关函数
09 # 从preprocessing.vgg_preprocessing import (_mean_image_subtraction,
10 _R_MEAN, _G_MEAN, _B_MEAN)
11 from matplotlib import pyplot as plt
12 import matplotlib.pyplot as plt
13 mpl.rcParams['font.sans-serif']=['SimHei'] #用来正常显示中文标签
14 mpl.rcParams['font.family'] = 'STSong'
15 mpl.rcParams['font.size'] = 12
16
17 tf.reset_default_graph()
18
19 slim = tf.contrib.slim
20
21 # 网络模型的输入图像有默认的尺寸
22 # 先调整输入图片的尺寸
23
24 names = imagenet.create_readable_names_for_imagenet_labels()
25 checkpoints_dir = 'vgg_19_2016_08_28'
26 sample_images = ['hy.jpg', 'ps.jpg']
```

# 3. 定义节点，载入模型

定义输入占位符，在这里不需要使用 VGG 的默认尺寸，所以使用[None, None,3]的 shape 对输入节点进行均值处理，并使用 reshape 函数更新，将形状调整成 1,None, None,3]。
获取模型参数的命名空间 arg_scope，定义相同命名空间下的输出节点。Logits 是刚从网络结构里运算出来的输出。手动将 logits 的最大索引放入 pred 里，即代表分类。

# 代码11-3 vgg19图片检测使用（续）

```
27 input_imgs = tf.placeholder("float", [None,None,3])
28 # 每个像素减去像素的均值
29 processed_image = _mean_image_subtraction(input_imgs,
30 [ _R_MEAN, _G_MEAN, _B_MEAN])
31
32 input_image = tf.expand_dims(processed_image, 0)
33 with slim.arg_scope(vgg.vgg_arg_scope()):
```