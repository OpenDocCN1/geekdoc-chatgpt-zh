
```
 1 #include <iostream>
 2 #include <opencv2/core/core.hpp>
 3 #include <opencv2/highgui/highgui.hpp>
 4 #include <opencv2/imgproc/imgproc.hpp>
 5 #include <string>
 6 #include <cstdint>
 7 
 8 enum class ImageSimilarity {
 9     Similar,
10     Different,
11     SomeWhatSimilar
12 };
13 
14 std::vector<int32_t> GetImageHash(const std::string& imagePath) {
15     cv::Mat image = cv::imread(imagePath, CV_LOAD_IMAGE_COLOR);
16 
17     cv::Mat processedImage;
18     cv::resize(image, processedImage, cv::Size(8, 8), 0, 0, cv::INTER_CUBIC);
19     cv::cvtColor(processedImage, processedImage, CV_BGR2GRAY);
20 
21     int avg = 0;
22     int features[64];
23 
24     for (int i = 0; i < 8; i++)
25     {
26         uchar* imageData = processedImage.ptr<uchar>(i);
27         for (int j = 0; j < 8; j++)
28         {
29             int featureIndex = i * 8 + j;
30 
31             features[featureIndex] = imageData[j] / 4 * 4;
32 
33             avg += fatures[featureIndex];
34         }
35     }
36 
37     avg /= 64;
38 
39     for (int i = 0; i < 64; i++)
40     {
41         features[i] = (features[i] >= avg) ? 1 : 0;
42     }
43 
44     return std::vector<int32_t>(features, fatures + 64);
45 }
46 
47 ImageSimilarity IsDifferentImage(const std::vector<int32_t>& hash1, const std::vector<int32_t>& hash2) {
48     int difference = 0;
49 
50     for (int i = 0; i < 64; i++)
51         if (hash1[i] != hash2[i])
52             ++ difference;
53 
54     if (difference <= 5) {
55         return ImageSimilarity::Similar;
56     }
57     else if (difference > 10) {
58         return ImageSimilarity::Different;
59     }
60 
61     return ImageSimilarity::SomeWhatSimilar;
62 }
```

第14行，定义了`GetImageHash`函数，该函数负责读取文件，并根据文件内容计算图像的pHash值。
第15行，调用OpenCV的`imread()`读取文件中的图像数据。
第17～19行，调用`resize()`和`cvtColor()`先将图像缩放再转成灰度图。
第24～35行，使用二重循环将图像分割为一块块的独立区块，每次计算这个区块的平均值。
第37～44行，将特征存储在长度为64的数组里。最后构造成长度为64的字符串返回。

### 10.2.5　完成Topology

最后我们需要按照系统架构将之前的Spout和Bolt全部组织起来。首先编写`Crawler-Topology.h`，该文件定义了`GetTopology`函数，这个函数会被`Hurricane`调用以生成`Topology`。另外，注意该函数是一个C符号的函数，因此代码中使用了`BEGIN_EXTERN_C`和`END_EXTERN_C`来处理这个函数声明，这两个宏定义在`Hurricane`的`base/externc.h`中。该文件如代码清单10-7所示。

代码清单10-7　CrawlerTopology.h
```
 1 #pragma once
 2 
 3 namespace hurricane {
 4     namespace topology {
 5         class Topology;
 6     }
 7 }
 8 
 9 #include "hurricane/base/externc.h"
10 
11 BEGIN_EXTERN_C
12 hurricane::topology::Topology* GetTopology();
13 END_EXTERN_C
```

接下来我们需要编写`CrawlerTopology.cpp`以实现这个`GetTopology`函数，该文件如代码清单10-8所示。

代码清单10-8　CrawlerTopology.cpp
```
 1 #include "CrawlerTopology.h"
 2 #include "SpiderTaskSpout.h"
 3 #include "UrlParseBolt.h"
 4 #include "HashFilterBolt.h"
 5 
 6 #include "hurricane/topology/Topology.h"
 7 
 8 hurricane::topology::Topology* GetTopology() {
 9     hurricane::topology::Topology* topology = new hurricane::topology::Topology("crawler-topology");
10 
11     topology->SetSpout("get-task-spout", new SpiderTaskSpout)
12         .ParallismHint(1);
13 
14     topology->SetBolt("url-parse-bolt", new UrlParseBolt)
15         .Random("get-task-spout")
16         .ParallismHint(3);
17 
18     topology->SetBolt("hash-filter-bolt", new HashFilterBolt)
19         .Random("url-parse-bolt")
20         .ParallismHint(2);
21 
22     return topology;
23 }
```

第6行，包含了`Topology.h`，该头文件定义了`Topology`类。
第8行，我们定义了`GetTopology`函数，该函数会构建并返回`Topology`对象，定义了整个计算网络。
第9行，调用`Topology`的构造函数创建一个`Topology`对象，`Topology`的名称是`crawler-topology`。
第11～12行，创建`SpiderTaskSpout`对象，调用`topology`的`SetSpout`构造函数，将该Spout加入到网络中，将其命名为`get-task-spout`，然后调用`ParallismHint`将其并行度设置为1。
第14～16行，创建`UrlParseBolt`对象，调用`topology`的`SetBolt`成员函数将Bolt加入到网络中，将其命名为`url-parse-bolt`，使用`Random`将其接到`get-task-spout`后，然后调用`ParallismHint`将其并行度设置为3。
第18～20行，创建`HashFilterBolt`对象，调用`topology`的`SetBolt`成员函数将Bolt加入到网络中，将其命名为`hash-filter-bolt`，使用`Random`将其接到`url-parse-bolt`后，然后调用`ParallismHint`将其并行度设置为2。
第22行，返回创建好的`Topology`对象指针，这样我们就完成了`Topology`的编写。整个`Topology`的结构图如图10-2所示。

![000](OEBPS/Images/330-1.jpg)

图10-2　CrawlerTopology结构图

## 10.3　训练与测试

我们爬取整理完图片后就可以开始训练了。由于在移动平台上使用的是`TensorFlow Lite`，因此先使用`TensorFlow`进行训练。

### 10.3.1　模型定义

首先我们编写一下我们的网络，在定义网络模型之前我们编写`train_util.py`，将`TensorFlow`的常用操作符做一个简单封装，让其使用起来像`PyTorch`那么简单，如代码清单10-9所示。

代码清单10-9　train_util.py
```
 1 import os
 2 import random
 3 import tensorflow as tf
 4 import numpy as np
 5 from scipy.io import loadmat
 6 from PIL import Image
 7 
 8 
 9 def weight(shape, name):
10         initial = tf.truncated_normal(shape, stddev=0.01)
11         w = tf.Variable(initial, name=name)
12         tf.add_to_collection('weights', w)
13         return w
14 
15 def bias(value, shape, name):
16         initial = tf.constant(value, shape=shape)
17         return tf.Variable(initial, name=name)
18 
19 def conv2d(x, W, stride, padding):
20         return tf.nn.conv2d(x, W, strides=[1, stride[0], stride[1], 1], padding = padding)
21 
22 def max_pool2d(x, kernel, stride, padding):
23         return tf.nn.max_pool(x, ksize=kernel, strides=stride, padding=padding)
24 
25 def lrn(x, depth_radius, bias, alpha, beta):
26         return tf.nn.local_response_normalization(x, depth_radius, bias, alpha, beta)
27 
28 def relu(x):
29         return tf.nn.relu(x)
30 
31 def batch_norm(x):
32         epsilon = 1e-3
33         batch_mean, batch_var = tf.nn.moments(x, [0])
34         return tf.nn.batch_normalization(x, batch_mean, batch_var, None, None, epsilon)
```

第9～13行，定义了`weight`函数，该函数用于将权重参数添加到`TensorFlow`网络中。
第15～17行，定义了`bias`函数，该函数用于将偏置参数添加到`TensorFlow`网络中。
第19～20行，定义了`conv2d`函数，该函数封装了`TensorFlow`的`conv2d`层。
第22～23行，定义了`max_pool2d`函数，该函数封装了`TensorFlow`的`max_pool`层。
第25～26行，定义了`lrn`函数，该函数封装了`TensorFlow`的`local_response_normalization`层。
第28～29行，定义了`relu`函数，该函数封装了`TensorFlow`的`relu`层。
第31～34行，定义了`batch_norm`函数，该函数封装了`TensorFlow`的`batch_normalization`层。

接下来利用`train_util.py`编写网络模型，网络模型在`model.py`中，如代码清单10-10所示。

代码清单10-10　model.py
