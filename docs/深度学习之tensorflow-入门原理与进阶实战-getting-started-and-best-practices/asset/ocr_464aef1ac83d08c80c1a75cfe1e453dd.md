2017-05-02 17:48:48.343454: E c:\tf_jenkins\home\workspace\release-win\device\gpu\ os\windows\tensorflow\stream_executor\cuda\cuda_dnn.cc:326] could not destroy cudnn handle: CUDNN_STATUS_BAD_PARAM
这种问题表明显卡没有启动，重启计算机即可

# 4. 评估模型

eval_image_classifier.py 文件是已经封装好用来评估模型的。下面还是以上面的 flower 集合微调 Inception_v3 的模型为例，评估模型的命令如下：

```
```
python eval_image_classifier.py--checkpoint_path=log/in3/model.ckpt
-3416059--eval_dir=log/in3/model.ckpt-3416059--dataset_name=flowers
--dataset_split_name=validation---dataset_dir=D:\own\python\flower_photosos--model_name=inception_v3
```
```

其中，指定路径的文件为 log/in3/model.ckpt-3416059，即在微调中训练出来的模型文件。

# 5. 打包模型

训练好的模型可以被打包到各个平台上使用，无论是 iOS、Android 还是 Linux 系列。具体是通过一个 bazel 开源工具来实现的。这部分内容不在本书的范围之内，有兴趣的读者可以自行研究。

更多的内容可以参考链接 https://github.com/tensorflow/models/tree/master/research/slim#Export。

# 11.6 使用 slim 中的深度网络模型进行图像的识别与检测

前面模型训练的知识点可以覆盖模型方面的大部分情况。如果读者刚好有图片分类的任务，或是想进行图片的识别，用 slim 中已有的网络结构来训练出自己的模型，比自己重新写一个模型的可行性更高一些。智者必须要学会借力而行。

有了模型之后就是使用模型了。下面通过几个实例来演示如何使用模型。

# 11.6.1 实例 85：调用 Inception_ResNet_v2 模型进行图像识别

本例是使用在 ImageNet 上训练好的 Inception_ResNet_v2 模型来识别图片内容，练习通过编写代码来调用 slim 中的 inception_resnet_v2 函数。具体步骤如下。

# 实例描述

使用基于 ImageNet 上训练的 Inception_ResNet_v2 模型对任意图片进行识别。