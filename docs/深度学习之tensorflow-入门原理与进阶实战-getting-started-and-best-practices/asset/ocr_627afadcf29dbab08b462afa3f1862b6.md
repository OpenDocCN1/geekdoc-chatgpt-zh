级高级 API 接口。它类似前面所介绍的 TensorFlow.contrib.layers 模块，将很多常见的 TensorFlow 函数进行了二次封装，使代码变得更加简洁，特别适用于构建复杂结构的深度神经网络，它可以用来定义、训练和评估复杂的模型。

同时，在 TensorFlow 的 models 模块里又提供了大量用 slim 写好的网络模型结构代码，以及用该代码训练出的模型检查点文件，可以作为我们的预训练模型来使用。这些模型主要是与图片分类相关，包括 ResNet、VGG、Inception-ResNet-v2 等。下面就来详细了解下 slim。

# 11.5.1 获取 models 中的 slim 模块代码

为了能够使用 models 中的代码，需要先验证下我们的 TensorFlow 版本是否集成了 slim 模块，接着再从 GitHub 上将 models 代码下载下来，具体操作如下。

+   1. 验证slim库

在使用 slim 前，要测试本地的 tf.contrib.slim 模块是否有效。在命令行中输入如下命令：

```
python -c "import tensorflow.contrib.slim as slim;
eval = slim.evaluation.evaluate_once"
```

如果没有生成任何错误，则表明 TF-Slim 是可以工作的。

+   2. 下载models模块

接下来需要安装 TF-slim image models library。来到以下网址 https://github.com/tensorflow/models/，可以通过 Git 将代码复制下来，也可以手动下载下来（具体操作见 8.5.2 的详细介绍）。然后解压到本地 workspace 路径下（就是你自己建立的用来放个人 TF 代码的路径），通过下面的代码来验证它是否工作。

```
cd $workspace/models/research/slim
python -c "from nets import cifarnet; mynet = cifarnet.cifarnet"
```

将上面的$workspace 替换成你的工作路径（如笔者的是 d:\python）。运行时如果没有发生任何错误，则表明一切正常。

# 11.5.2 models 中的 slim 目录结构

在 models 下的 slim 中一共有 5 个文件夹。

+   • Datasets: 处理数据集相关的代码。

+   • Deployment: 部署。通过创建 clone 方式实现跨机器的分布训练，可以在多 CPU 和多 GPU 上实现运算的同步或异步。

+   • Nets: 该文件夹里放着各种网络模型。

+   • Preprocessing: 适用于各个网络的图片处理函数。