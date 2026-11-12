

![](img/4aa0d33e89ab0b6060ece712474d6e70_76_0.png)

图 3-9

![](img/4aa0d33e89ab0b6060ece712474d6e70_76_1.png)

图 3-10

![](img/4aa0d33e89ab0b6060ece712474d6e70_76_2.png)

图 3-11

接下来就可以安装 TensorFlow 了。在虚拟环境“TensorFlow”上单击图标，在下拉菜单中选择 Open Terminal 选项，如图 3-12 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_77_0.png)

图 3-12

此时会打开一个终端窗口，目录前面显示（TensorFlow）为虚拟环境名，在终端输入 pip install tensorflow，按 Enter 键后安装开始，如图 3-13 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_77_1.png)

图 3-13

如果最终显示图 3-14 所示的信息，表示安装成功。

![](img/4aa0d33e89ab0b6060ece712474d6e70_77_2.png)

图 3-14

在安装完 TensorFlow 后，我们还需要验证 TensorFlow 是否安装成功，在刚才打开的 Terminal 中输入 python，并输入以下代码。

```python
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

如果看到 “Hello, TensorFlow!”, 则证明安装成功，如图 3-15 所示。如果是 no module，请检查是否安装错误。

![](img/4aa0d33e89ab0b6060ece712474d6e70_78_0.png)

图 3-15

### (3) 安装 GPU 版本 TensorFlow

如果希望使用带有 GPU 支持的 TensorFlow，可以通过以下步骤进行安装。

### ① 安装 CUDA 软件

CUDA 是 NVIDIA 推出的运算平台，cuDNN 是专门针对 Deep Learning 框架设计的一套 GPU 计算加速方案。如果拥有一款支持 CUDA 的 NVIDIA GPU，则可安装带有 GPU 支持的 TensorFlow。支持 CUDA 的显卡清单可从相关网址获取。

接下来就可以安装 CUDA 了。首先需要下载 CUDA，可到 NVIDIA 官方网站进行相关下载。

选择机器的操作系统及版本，本书实例安装 CUDA 9.0 版本，系统平台是 64 位 Windows 10 操作系统，选择后打开图 3-16 所示的界面进行 CUDA 及 CUDA 补丁的下载。

![](img/4aa0d33e89ab0b6060ece712474d6e70_78_1.png)

图 3-16

首先安装 Base Installer，如图 3-17 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_79_0.png)

图 3-17

选择精简安装，以默认选项进行安装即可，如果出现图 3-18 所示界面，则安装成功。

![](img/4aa0d33e89ab0b6060ece712474d6e70_79_1.png)

图 3-18

接下来下载并安装 CUDA 补丁，下载任一 CUDA 补丁包进行安装即可。安装完成后，需要下载 cuDNN。可通过 NVIDIA 官方网站链接进行下载。由于之前安装的是 CUDA 9.0，所以需要下载 cuDNN for CUDA 9.0，如图 3-19 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_79_2.png)

图 3-19

选择相应操作系统版本的 cuDNN 进行下载，如图 3-20 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_80_0.png)

图 3-20

下载后获得一个 cuDNN 的压缩包，解压此压缩包，并将包内文件及文件夹复制到之前 CUDA 安装文件内，具体位置如图 3-21 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_80_1.png)

图 3-21

注意：此操作需要提供管理员权限。

至此，CUDA 安装完毕，如果要查看安装的 CUDA 的版本信息，则需要在命令窗口（cmd）中输入如下命令。

```
nvcc -V
```

### ② 安装 GPU 版本 TensorFlow

启动 Anaconda，在 Conda 虚拟环境下安装 GPU 版本 TensorFlow，如图 3-22 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_81_0.png)

图 3-22

选择左侧菜单栏的 “Environments” 选项，打开后用 Create 新建一个虚拟环境，如图 3-23 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_81_1.png)

图 3-23

在弹出的 “Create new environment” 对话框中的 Name 文本框中输入 TensorFlowGPU，并选择 Packages 为 Python 3.5，单击 “Create” (创建) 按钮，如图 3-24 所示。创建完成后在中间的环境窗口中出现名为 TensorFlowGPU 的虚拟环境。

接下来就可以安装 TensorFlow 了。在虚拟环境 TensorFlowGPU 上单击 ▶ 图标，在下拉菜单中选择 “Open Terminal” 选项，打开一个终端窗口，此时目前前面显示 ( tensorflow_gpu ) 为虚拟环境名，在终端输入 pip install tensorflow-gpu，按 Enter 键后安装开始，如图 3-25 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_82_0.png)

图 3-24

![](img/4aa0d33e89ab0b6060ece712474d6e70_82_1.png)

图 3-25

全部下载解压完成后，输入 python 进行确认。在 Python 编译环境下分别输入 import tensorflow as tf，sess=tf.Session()并按 Enter 键，会显示出本机的 GPU 信息内容，如图 3-26 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_82_2.png)

图 3-26

至此，Windows 系统环境下 GPU 版本 TensorFlow 安装完毕。

## 4. 在 Linux 系统及 Mac OS X 下安装 TensorFlow

### (1) 创建 Virtualenv 环境

为保持依赖项的干净整洁，下面介绍如何利用 Virtualenv 创建虚拟的 Python 环境。首先需要确保 Virtualenv 与 pip（Python 的包管理器）均被安装。运行下列命令（根据不同的操作系统，选择相应的命令）。

### ① 64 位 Linux 系统

```
$ sudo apt-get install python-pip python-dev python-virtualenv
```

### ② Mac OS X

```
$ sudo easy_install pip
```

```
$ sudo pip install --upgrade virtualenv
```

至此，准备工作已完成，接下来创建一个包含该虚拟环境的目录，以及将来可能会创建的任意虚拟环境。

```
$ sudo mkdir ~/env
```

接下来，利用 Virtualenv 命令创建虚拟环境。在本例中，它位于 ~/env/tensorflow 目录（“~”指当前目录）下。

```
$ virtualenv --system-site-packages ~/env/tensorflow
```

一旦创建完毕，便可利用 source 命令激活该虚拟环境。

```
$ source ~/env/tensorflow/bin/activate
```

注意，命令提示窗口中现在多了一个 “tensorflow” 提示符。

```
(tensorflow)$
```

我们希望当使用 pip 安装任何软件时都确保该虚拟环境处于活动状态，从而使 Virtualenv 能够对各依赖库进行追踪。

虚拟环境使用完毕后，需用下列 deactivate 命令将其关闭。

```
(tensorflow)$ deactivate
```

由于将频繁使用虚拟环境，因此创建一个激活虚拟环境的快捷方式而非每次键入完整的 source 命令便很有价值。接下来的命令将向 ~/.bashrc 文件添加一个 bash 别名，使得启动虚拟环境时只需键入 tensorflow。

```
$ sudo printf '\alias tensorflow="source ~/env/tensorflow/bin/activate"' >> ~/.bashrc
```

要测试该快捷方式是否生效，可重启 bash 终端，并键入 tensorflow。

```
$ tensorflow
```

与之前一样，提示符会发生变化。

```
(tensorflow)$
```

### (2) TensorFlow 的简易安装

如果只是希望尽快上手实践一些入门的例子，而不关心是否有 GPU 支持，则可从 TensorFlow 官方预置的二进制安装程序中选择。请确保 Virtualenv 环境处于活动状态，并运行下列与你的操作系统和 Python 版本对应的命令。

### ① 64 位 Linux 系统

```
#Linux, Python 2.7
(tensorflow)$ pip install --upgrade https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.9.0-cp27-none-linux_x86_64.whl
```

```
#Linux 64-bit, Python 3.4
(tensorflow)$ pip3 install --upgrade https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.9.0-cp34-cp34m-linux_x86_64.whl
```

```
#Linux 64-bit, Python 3.5
```

```
(tensorflow)$ pip3 install --upgrade https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.9.0-cp35-cp35m-linux_x86_64.whl
```

### ② Mac OS X

```
#Mac OS X, Python 2.7:
(tensorflow)$ pip install --upgrade https://storage.googleapis.com/tensorflow/mac/tensorflow-0.9.0-py2-none-any.whl
```

```
#Mac OS X, Python 3.4+
(tensorflow)$ pip3 install --upgrade https://storage.googleapis.com/tensorflow/mac/tensorflow-0.9.0-py3-none-any.whl
```

从技术角度上看，可以使用带有 GPU 支持的预置的 TensorFlow 二进制安装程序，但它需要特定版本的 NVIDIA 软件，且与未来版本不兼容。

在 Linux 上安装 GPU 版 TensorFlow，或在 Mac OS X 上从源码构建 TensorFlow，都可以依据 TensorFlow 官方网站的安装指南进行相应设置，由于篇幅问题，这里不再赘述。

至此，TensorFlow 安装完毕！鉴于版本兼容等问题，建议初学者采用 Windows 环境下的安装方式，对于不支持 GPU 的机器，建议安装 CPU 版本。

## 3.3 数据流图简介

本节介绍一些数据流图的基础知识，内容包括节点、边和节点依赖关系的定义。此外，为了解释一些关键原理，本章还提供了若干实例。

### 3.3.1 数据流图基础

借助 TensorFlow API 用代码描述的数据流图是每个 TensorFlow 程序的核心。毫不意外，数据流图这种特殊类型的有向图正是用于定义计算结构的。在 TensorFlow 中，数据流图本质上是一组链接在一起的函数，每个函数都会将其输出传递给 0 个、1 个或更多个位于这个级联链上的其他函数。按照这种方式，用户可利用一些很小的、为人们所充分理解的数学函数构造数据的复杂变换。下面来看一个比较简单的例子，如图 3-27 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_84_0.png)

图 3-27 展示了可完成基本加法运算的数据流图。在该图中，加法运算是用圆圈表示的，它可接收两个输入（以指向该函数的箭头表示），并将 1+2 之和 3 输出（对应从该函数引出的箭头）。该函数的运算结果可传递给其他函数，也可直接返回给客户。

该数据流图可用如下简单公式表示。

```
f(1,2)=1+2=3
```

上面的例子解释了在构建数据流图时，两个基本构件——节点和边是如何使用的。下面回顾节点和边的基本性质。

- 节点（node）：在数据流图的语境中，节点通常以圆圈、椭圆和方框表示，代表了对数据所做的运算或某种操作。在上例中，add 对应于一个孤立节点。

- 边（edge）：对应于向 Operation 传入和从 Operation 传出的实际数值，通常以箭头表示。在 add 这个例子中，输入 1 和 2 均为指向运算节点的边，而输出 3 则为从运算节点引出的边。可从概念上将边视为不同 Operation 之间的连接，因为它们将信息从一个节点传输到另一个节点。

下面来看一个更有趣的例子，如图 3-28 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_85_0.png)

图 3-28

相比之下，图 3-28 的实例中所示的数据流图略复杂。由于数据是从左侧流向右侧的（如箭头方向所示），因此可以从最左端开始对这个数据流图进行分析。

（1）最开始时，可看到两个值 5 和 3 流入该数据流图。它们可能来自另一个数据流图，也可能读取自某个文件，或是由客户直接输入。

（2）这些初始值被分别传入两个明确的 input 节点（在图中分别以 a、b 标识）。这些 input 节点的作用仅仅是传递它们的输入值——节点 a 接收到输入值 5 后，将同样的数值输出给节点 c 和节点 d，节点 b 对其输入值 3 也完成同样的动作。

（3）节点 c 代表乘法运算。它分别从节点 a 和 b 接收输入值 5 和 3，并将运算结果 15 输出到节点 e。与此同时，节点 d 对相同的两个输入执行加法运算，并将计算结果 8 传递给节点 e。

（4）最后，该数据流图的终点——节点 e（也是另一个 add 节点）接收输入值 15 和 8，将两者相加，然后输出该数据流图的最终结果 23。

下面用一组公式表示该图的数据流图。

```
a=input1; b=input2
c=a*b; d=a+b
e=c+d
```

当 a=5, b=5 时，若要求解 e，只需依次代入上述公式。

```
a=5;b=3
e=a*b+(a+b)
e=5*3+(5+3)
```

经过上述步骤，便可以完成计算。这里有一些概念值得重点说明。

- 上述使用 input 节点的模式十分有用，因为这使得我们能够将单个输入值传递给大量后继节点。如果不这样做，客户（或传入这些初值的其他数据源）便不得不将输入显示传递给数据流图中的多个节点。按照这种模式，客户只需保证一次性传入恰当的输入值，而对这些输入重复使用的细节便被隐藏了起来。稍后，我们将对数据流图的抽象做更深入的探讨。

- 哪一个节点将首先执行运算？是乘法节点 c 还是加法节点 d？答案是：无从知晓。仅凭上述数据流图，无法推知 c 和 d 中的哪一个节点将率先执行。有的读者可能会按照从左到右、自上而下的

顺序阅读该数据流图，从而做出节点 c 先运行的假设。但需要指出，在该数据流图中，将节点 d 绘制在 c 的上方也未尝不可。也可能有些读者认为这些节点会并发执行，但考虑到各种实现细节或硬件的限制，实际情况往往并非如此，实际上，最好的方式是将它们的执行视为相互独立。由于节点 c 并不依赖于来自节点 d 的任何信息，所以节点 c 在完成自身的运算时无须关心节点 d 的状态如何。反之亦然，节点 d 也不需要任何来自节点 c 的信息。在本章稍后，还将对节点的依赖关系进行更深入的介绍。

接下来，对数据流图稍作修改，如图 3-29 所示。

![](img/4aa0d33e89ab0b6060ece712474d6e70_86_0.png)

图 3-29

数据流图的主要变化有以下两点。

- (1) 来自节点 b 的 input 值 3 现在也传递给了节点 e。
- (2) 节点 e 中的函数 add 被替换为 sum，表明它可完成两个以上的数的加法运算。

读者可能已经注意到，图 3-30 在看起来被其他节点 “隔离” 的两个节点之间添加了一条边。一般而言，任何节点都可将其输出传递给数据流图中的任意后继节点，而无论这两者之间发生了多少计算，数据流图甚至可以拥有图 3-30 所示的结构，它仍然是完全合法的。

![](img/4aa0d33e89ab0b6060ece712474d6e70_86_1.png)

图 3-30

通过这两个数据流图，已经能够初步感受到对数据流图的输入进行抽象所带来的好处。我们能够对数据流图中内部运算的精确细节进行操控，但客户只需了解将何种信息传递给那两个输入节点。我们甚至可以进一步抽象，将上述数据流图表示为图 3-31 所示的黑箱。

![](img/4aa0d33e89ab0b6060ece712474d6e70_86_2.png)

图 3-31

这样，便可将整个节点序列视为拥有一组输入和输出的离散构件。这种抽象方式使得对级联在一起的若干个运算组进行可视化更加容易，而无须关心每个部件的具体细节。

