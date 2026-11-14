# 8.5.2 下载 CIFAR 数据

与 MNIST 类似，TensorFlow 中同样有一个下载和导入 CIFAR 数据集的代码文件，不同的是，自从 TensorFlow1.0 之后，将里面的 Models 模块分离了出来。下载和导入 CIFAR 数据集的代码在 models 里面，所以要先去 TensorFlow 的 GitHub 网站将其下载下来。

如果你使用 Git，可以直接用下面的命令下载：

```
git clone https://github.com/tensorflow/models.git
```

如果没有使用 Git，可直接复制上面的网址，在右下角单击 clone or download 按钮，在下方在单击 Download ZIP 按钮下载代码的压缩包，如图 8-13 所示。

![](bbox=[0.31, 0.35, 0.72, 0.54])

图 8-13 model 代码下载

代码下载后，将其解压，将里面 models/tutorials/image/路径下的 CIFAR10 复制到本地的 Python 工作区即可。

现在可以在 CIFAR10 文件夹下新建 Python 文件，用来下载和导入 CIFAR10 图片了。与 MNIST 不同的是，CIFAR 数据集代码不是很方便，下载和导入时都需要单独调用，所以本节的例子代码第一个步会有一个独立的代码文件。

将如下代码文件放到 cifar10 文件夹下（确保 import cifar10 能找到对应文件），引入 CIFAR10，使用函数 maybe_download_and_extract 即可完成数据的下载和解压。

代码8-4 CIFAR下载

```
import cifar10
cifar10.maybe_download_and_extract()
```

上面的代码会自动将 CIFAR10 的 bin 文件 ZIP 包下载到\tmp\cifar10_data 路径下（如果是 Windows 就是本地磁盘下的这个路径，如 D:\tmp\cifar10_data），然后自动解压到 \tmp\cifar10_data\cifar-10-batches-bin 路径下。

以上这个环节也可以手动下载，然后解压到指定路径里。