
## 2.1.3 Jupyter Notebook 的基本使用

在打开的界面上，依次单击 New → Python3 来创建一个 Python3 的 .ipynb 文件。然后单击右上角的 new 按钮，选择 Python 3，如图 2-5 所示。

![](img/470b883072d48f1f2917a784b39d4327_26_0.png)

选择 Python 3 以后将打开一个新的界面，这里就是编写代码的地方了。首先，单击左上角的文件名，进行文件名重命名，如图 2-6 所示。

![](img/470b883072d48f1f2917a784b39d4327_26_1.png)

重命名后进入代码编辑页面，在里面写一行 Python 代码，比如输入 “a=1” 后按 Enter 键，Jupyter 换行但没有开启新的命令行单元格，继续输入 “b=5” 后单击 “运行”，或者按 Shift+Enter 快捷键，运行单元格中的命令，因为此时是赋值命令所以没有输出结果。输入变量名后按 Shift+Enter 快捷键即可看到交互的 Python 命令行中单元格的运行结果，这里输入 “a+b” 后运行，看到实时结果为 6，如图 2-7 所示。这就体现出交互式 Python 命令行编程的优势，即不用编写打印输出命令 print 就可以实时观察运行情况。

![](img/470b883072d48f1f2917a784b39d4327_26_2.png)

在图 2-8 中，In[6] 中的数字 “6” 表示执行的次数，这个次数也可以理解为在这个工作区里执行代码的顺序。因为每个代码块里面的内容是可以相互调用的，可能在后面定义的一个方法也是可以在前面来使用的。这里一定要注意，如果两个代码块里面的内容要进行调用，比如 B 块要调用 A 块里面的代码，A 块里面的代码在写完以后，必须先执行，然后才能在 B 块中调用，否则会报错。

Jupyter 不仅可以写代码，还可以用 Markdown 语法写注释说明文档。首先选择代码，然后切换到 Markdown 以后，就可以在里面写一些文档注释。写完注释后，同样使用快捷键 Shift + Enter 运行代码，就可以看到如图 2-9 所示的效果。

![](img/470b883072d48f1f2917a784b39d4327_27_0.png)

图 2-8

![](img/470b883072d48f1f2917a784b39d4327_27_1.png)

图 2-9

Jupyter 插入图片的方法是把要插入文档的图片（如 airplane.jpg）放到程序文件的相同目录下，通过 Markdown 格式插入图片，如输入 “![jupyter](airplane.jpg)”，运行单元格，即可显示该图片。

如果需要将本地的 Python 文件（.py 文件）载入到 Jupyter 的一个单元格中，比如当前路径下有一个 test_demo.py 文件，需要将其载入到 Jupyter 的一个单元格中。

test_demo.py 的 Python 源文件内容如下：

```
import os
print(os.getcwd()) # 获取当前工作目录路径
```

在需要导入该段代码的单元格中输入 “%load test_demo.py”，按快捷键 Shift+Enter 运行该单元格，结果如图 2-10 所示。可以看到运行后，%load test_demo.py 被自动加入了注释符号 #，test_demo.py 中的所有代码都被载入到了当前的单元格中。

![](img/470b883072d48f1f2917a784b39d4327_27_3.png)

图 2-10

利用 Jupyter 的单元格也是可以运行 Python 文件的，即在单元格中运行如下代码：

```
%run file.py
```

这里的 file.py 是要运行的 Python 程序的文件名，运行结果会显示在该单元格中，如图 2-11 所示。

![](img/470b883072d48f1f2917a784b39d4327_27_4.png)

图 2-11

Jupyter 的 .ipynb 文件也可以转换为 .py 文件，直接在 Jupyter Notebook 中的 File 菜单栏中依次单击 Download as → Python(.py) 即可，如图 2-12 所示。

## Python 深度学习从零开始学

![](img/470b883072d48f1f2917a784b39d4327_28_0.png)

图 2-12

## 2.2 深度学习常用框架介绍

在开始深度学习项目之前，选择一个合适的框架是非常重要的，因为选择一个合适的框架能起到事半功倍的作用。研究者们使用各种不同的框架来达到他们的研究目的，这也侧面证明了深度学习领域的百花齐放。全世界流行的深度学习框架有 PaddlePaddle、TensorFlow、Caffe、Theano、Keras、Torch 和 PyTorch。

### 1. TensorFlow

Google 开源的 TensorFlow 是一款使用 C++ 语言开发的开源数学计算软件，使用数据流图（Data Flow Graph）的形式进行计算。图中的节点代表数学运算，而图中的线条表示多维数据数组（Tensor）之间的交互。TensorFlow 灵活的架构可以部署在一个或多个 CPU、GPU 的计算机及服务器中，或者使用单一的 API 应用在移动设备中。TensorFlow 最初是由研究人员和 Google Brain 团队针对机器学习和深度神经网络进行研究而开发的，开源之后几乎可以在各个领域适用。

TensorFlow 是全世界使用人数最多、社区最为庞大的一个框架，因为是 Google 公司出品，所以维护与更新比较频繁，并且有着 Python 和 C++ 的接口，教程也非常完善，同时很多论文复现的第一个版本都是基于 TensorFlow 写的，所以是深度学习框架里默认的老大。

### 2. PyTorch

PyTorch 的前身便是 Torch。Torch 是纽约大学的一个机器学习开源框架，几年前在学术界非常流行，包括 Lecun 等都在使用。但是由于其使用的是 一种绝大部分人都没有听说过的 Lua 语言，导致很多人都被吓退。后来随着 Python 的生态越来越完善，Facebook 人工智能研究院推出了 PyTorch 并开源。PyTorch 不是简单地封装 Torch 并提供 Python 接口，而是使用 Python 重新编写了很多内容，使其不仅更加灵活、支持动态图，而且提供了 Python 接口。它由 Torch7 团队开发，是一个以 Python 优先的深度学习框架，不仅能够实现强大的 GPU 加速，同时还支持动态神经网络，这是很多主流深度学习框架（比如 TensorFlow 等）都不支持的功能特性。

### 3. Keras

Keras 是一个对小白用户非常友好且简单的深度学习框架，严格来说它并不是一个开源框架，而是一个高度模块化的神经网络库。它是一个用 Python 编写的高级神经网络 API（高层意味着会引用封装好的底层），能够以 TensorFlow、CNTK 或者 Theano 作为后端运行。Keras 的特点是能够快速实现模型的搭建，能够把我们的想法迅速转换为结果。TensorFlow 的 API 比较底层，有时候做一件很简单的事情要写很多辅助代码。而 Keras 的接口设计非常简洁，做同样的事情，Keras 的代码大概是 TensorFlow 的三分之一到五分之一。

### 4. 框架总结

深度学习框架的出现降低了深度学习入门的门槛，我们不需要从复杂的神经网络开始编写代码，可以根据需要选择已有的模型，通过训练得到模型参数，也可以在已有模型的基础上增加自己的层（Layer），或者是在顶端选择自己需要的分类器和优化算法（比如常用的梯度下降法）。当然也正因如此，没有什么框架是完美的，就像一套积木里可能没有你需要的那一种积木，所以不同的框架适用的领域不完全一致。总的来说深度学习框架提供了一系列的深度学习的组件（对于通用的算法，里面会有实现），当需要使用新的算法的时候，就需要用户自己去定义，然后调用深度学习框架的函数接口使用用户自定义的新算法。

![](img/470b883072d48f1f2917a784b39d4327_29_0.png)

## 2.3 Windows 环境下安装 TensorFlow（CPU 版本）和 Keras

Keras 只是一个前端 API，在使用它之前需要安装好其后端，根据主流情况推荐安装 TensorFlow 作为 Keras 的后端引擎。故先安装 TensorFlow，后安装 Keras。在安装 TensorFlow 和 Keras 时，需要注意两者版本的对应关系，否则可能会报错。比如，安装的 Keras 版本为 2.4，这个版本要求 TensorFlow 是 2.2 以上，版本之间不对应是无法使用的。

下面讲解如何安装 TensorFlow（CPU 版本）和 Keras。

### 1. 安装 TensorFlow（CPU 版本）

具体操作步骤如下：

步骤 01 首先确保 pip 的版本较新，先在命令提示符窗口输入命令 “python -m pip install -U pip” 升级 pip，如图 2-13 所示。

![](img/470b883072d48f1f2917a784b39d4327_30_0.png)

图 2-13

步骤 02 通过 pip 安装 TensorFlow CPU 版本（这里安装的 TensorFlow 版本为 1.3.0），输入命令 “pip install tensorflow==1.3.0”。安装完成后执行命令 “pip show tensorflow” 查看 CPU 版本的 TensorFlow，如图 2-14 所示。

![](img/470b883072d48f1f2917a784b39d4327_30_1.png)

图 2-14

步骤 03 无论是 CPU 版本还是 GPU 版本的 TensorFlow，在安装完成后，都可以使用图 2-15 中的代码测试 TensorFlow 是否正常安装。在 Jupyter Notebook 代码编辑界面输入示例代码，如果没有提示错误，并输出 “Hello,TensorFlow!” ，则说明 TensorFlow 已经安装成功，如图 2-15 所示。

```
In [19]:
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
h=sess.run(hello)
print(h.decode())

Hello, TensorFlow!
```

图 2-15

### 2. 安装 keras

具体操作步骤如下：

步骤 01 在命令提示符窗口下输入命令 “pip install keras==2.1.2”，安装 Keras（指定安装的 Keras 版本为 2.1.2）。安装完成后执行命令 “pip show keras” 查看 Keras，如图 2-16 所示。

![](img/470b883072d48f1f2917a784b39d4327_30_2.png)

图 2-16

也可以在 Jupyter Notebook 代码编辑界面输入如图 2-17 所示的代码，查看 Keras 的版本（注意，代码中是双下划线）。

```
import tensorflow as tf
import keras
print(tf.__version__)
print(keras.__version__)

1.3.0
2.1.2
```

图 2-17

## 2.4 Windows 环境下安装 TensorFlow（GPU 版本）和 Keras

本节主要介绍如何在 Windows 环境下安装 TensorFlow（GPU 版本）和 Keras。

### 2.4.1 确认显卡是否支持 CUDA

在深度学习中，我们常常要对图像数据进行处理和计算，而处理器 CPU 因为需要处理的事情太多，并不能满足我们对图像处理和计算的速度的要求，显卡 GPU 就是用来帮助 CPU 解决这个问题 的，因为 GPU 特别擅长处理图像数据。

为什么 GPU 特别擅长处理图像数据呢？这是因为图像上的每一个像素点都有被处理的需要，而且每个像素点的处理过程和方式都十分相似，GPU 就是用很多简单的计算单元去完成大量的计算任务，类似于纯粹的人海战术。GPU 不仅可以在图像处理领域大显身手，它还被用在科学计算、密码破解、数值分析、海量数据处理（排序、Map-Reduce 等）、金融分析等需要大规模并行计算的领域。

而 CUDA（Compute Unified Device Architecture）是显卡厂商 NVIDIA（英伟达）推出的只能用于自家 GPU 的并行计算框架，只有安装这个框架才能够进行复杂的并行计算。该架构使 GPU 能够解决复杂的计算问题。它包含了 CUDA 指令集架构（ISA）以及 GPU 内部的并行计算引擎。安装 CUDA 之后，可以加快 GPU 的运算和处理速度，主流的深度学习框架也都是基于 CUDA 进行 GPU 并行加速的。

要想安装 CUDA 用于 GPU 并行加速，首先需要确定计算机显卡是否支持 CUDA 的安装，也就是查看计算机里面有没有 NVIDIA 的独立显卡。在 NVIDIA 官网列表（https://developer.nvidia.com/cuda-gpus）中可以查看显卡型号。

在桌面上右击，如果能找到 NVIDIA 控制面板，如图 2-18 所示，则说明该计算机有 GPU。

在 NVIDIA 控制面板上，通过查看系统信息获取支持的 CUDA 版本，类似结果如图 2-19 所示。

![](img/470b883072d48f1f2917a784b39d4327_31_0.png)

图 2-18

### 2.4.2 安装 CUDA

CUDA 是显卡厂商 NVIDIA 推送的基于新的并行编程模型和指令集架构的通用并行计算框架，能利用 NVIDIA GPU 的并行计算引擎进行复杂的并行计算。安装 CUDA 的操作步骤如下：

步骤 01 如果已经确认系统已有支持 CUDA 的显卡，这时可以到 NVIDIA 官方网站 https://developer.nvidia.com/cuda-toolkit-archive 下载 CUDA，如图 2-20 所示。

![](img/470b883072d48f1f2917a784b39d4327_32_0.png)

图 2-19

![](img/470b883072d48f1f2917a784b39d4327_32_1.png)

图 2-20

注意 安装 CUDA Driver 时，需要与 NVIDIA GPU Driver 的版本驱动一致，CUDA 才能找到显卡。

步骤 02 根据实际情况选择合适的版本，这里下载 CUDA10 的本地安装包，如图 2-21 所示选择操作系统版本。

![](img/470b883072d48f1f2917a784b39d4327_33_0.png)

图 2-21

步骤 03 安装时选择自定义，注意箭头标记的复选框不要勾选，如图 2-22 所示，不需要安装 Visual Studio，CUDA 的环境变量会自动进行配置。

![](img/470b883072d48f1f2917a784b39d4327_33_1.png)

图 2-22

### 2.4.3 安装 cuDNN

cuDNN 是用于深度神经网络的 GPU 加速库，接下来需要下载与 CUDA 对应的 cuDNN，下载地址为 https://developer.nvidia.com/rdp/cudnn-archive，如图 2-23 所示。

![](img/470b883072d48f1f2917a784b39d4327_33_2.png)

图 2-23

步骤 01 下载 cuDNN 需要注册英伟达开发者计划的会员账号，加入会员，如图 2-24 所示。

![](img/470b883072d48f1f2917a784b39d4327_34_0.png)

图 2-24

步骤 02 成为会员后下载与 CUDA 对应版本的 cuDNN。cuDNN 就是个压缩包，解压会生成 cuda/include、cuda/lib、cuda/bin 三个目录，将里面的文件分别复制到 CUDA 安装目录（这里是 C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0 目录）下对应的目录即可。注意不是替换文件夹，而是将文件放入对应的文件夹中。

步骤 03 安装 Visual Studio 2015、2017 和 2019 支持库，这个支持库务必安装，否则后面容易出现各种问题，支持库所占内存大约十几 MB。下载地址为 https://docs.microsoft.com/zh-CN/cpp/windows/latest-supported-vc-redist?view=msvc-160，如图 2-25 所示。

步骤 04 安装完成后重启计算机即可。

![](img/470b883072d48f1f2917a784b39d4327_34_1.png)

图 2-25

### 2.4.4 安装 TensorFlow (GPU 版本) 和 Keras

步骤 01 通过 pip 命令安装。在命令提示符窗口输入命令 “pip install tensorflow-gpu==1.15.2”，安装 TensorFlow 的 GPU 版本 1.15.2。

步骤 02 检查是否安装成功。在 Jupyter Notebook 代码编辑界面输入如下代码：

```
from tensorflow.python.client import device_lib
import tensorflow as tf
print(device_lib.list_local_devices())
print(tf.test.is_built_with_cuda())
```

输出结果如图 2-26 所示。输出结果为 GPU 的相关信息则表示安装成功，如果不是 GPU 版本，则会输出 “False”。

步骤03 使用 pip 命令安装 Keras。在命令提示符窗口输入命令 “pip install keras==2.1.2”， 安装 Keras 的版本 2.1.2。

![](img/470b883072d48f1f2917a784b39d4327_35_0.png)

图 2-26

## 2.5 Windows 环境下安装 PyTorch

本节主要介绍如何在 Windows 环境下安装 PyTorch， PyTorch 也有 CPU 版和 GPU 版。

### 2.5.1 安装 PyTorch (CPU 版本)

从 2018 年 4 月起， PyTorch 官方开始发布 Windows 版本。 PyTorch 是基于 Python 开发的，要使用 PyTorch 首先需要安装 Python， 这里的 Python 版本是 3.6。 Windows 用户能直接通过 conda、 pip 和源码编译三种方式来安装 PyTorch。 本小节主要介绍如何通过 pip 来安装 PyTorch。

步骤01 在命令提示符窗口输入命令 “pip install torch==1.8.0 torchvision==0.9.0 torchaudio==0.8.0” 安装指定版本的 PyTorch， 如图 2-27 所示。

![](img/470b883072d48f1f2917a784b39d4327_35_1.png)

图 2-27
