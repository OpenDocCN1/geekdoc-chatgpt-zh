
在训练过程中，D 会接收真数据和 G 产生的假数据，它的任务是判断图片属于真数据还是假数据。对于最后输出的结果，可以同时对两方的参数进行调优。如果 D 判断正确，那就需要调整 G 的参数，从而使得生成的假数据更为逼真，如果 D 判断错误，则需要调节 D 的参数，避免下次类似判断出错。训练会一直持续到两者进入到一个均衡和谐的状态。

训练后的产物是一个质量较高的自动生成器和一个判断能力较强的分类器。前者可以用于机器创作（比如自动画出“猫”“狗”），而后者则可以用来进行机器分类（比如自动判断“猫”“狗”）。

那么一个很自然的问题来了，如何训练这样一个生成对抗网络模型？其步骤如下：

- 步骤 01 在噪声数据分布中随机采样，输入生成模型，得到一组假数据，记为 G(z)。
- 步骤 02 在真实数据分布中随机采样，作为真实数据，记做 x；将前两步中某一步产生的数据作为判别网络的输入（因此判别模型的输入为两类数据：真或假），判别网络的输出值为该输入属于真实数据的概率，real 为 1，fake 为 0。
- 步骤 03 然后根据得到的概率值计算损失函数。
- 步骤 04 根据判别模型和生成模型的损失函数，可以利用反向传播算法更新模型的参数。（先更新判别模型的参数，然后通过再采样得到的噪声数据来更新生成器的参数。）

生成模型与判别模型是完全独立的两个模型，它们之间没有什么联系，训练采用的大原则是单独交替迭代训练。

GAN 的强大之处在于能自动学习原始真实样本集的数据分布，不管这个分布有多么的复杂，只要训练得足够好就可以得到结果。传统的机器学习方法一般会先定义一个模型，再让数据去学习。而 GAN 的生成模型最后可以通过噪声生成一个完整的真实数据（比如人脸），说明生成模型掌握了从随机噪声到人脸数据的分布规律。GAN 一开始并不知道这个规律是什么样，也就是说 GAN 是通过一次次训练后学习到的真实样本集的数据分布。

GAN 的核心原理如何用数学语言来描述呢？这里直接摘录 Ian Goodfellow 的论文 Generative Adversarial Nets 中的公式，如图 13-3 所示。

```
min_G max_D V(D,G) = E_{x~Pdata(x)}[log D(x)] + E_{z~pz(z)}[log(1 - D(G(z)))]
```

图 13-3

简单分析一下这个公式：

- 整个公式由两项构成。x 表示真实图片，z 表示输入 G 网络的噪声，而 G(z) 表示 G 网络生成的图片。
- D(x) 表示 D 网络判断真实图片是否真实的概率（因为 x 就是真实的，所以对于 D 来说，这个值越接近 1 越好）。而 D(G(z)) 是 D 网络判断 G 生成的图片是否真实的概率。
- G 的目的：上面提到过，D(G(z)) 是 D 网络判断 G 生成的图片是否真实的概率，G 应该希望自己生成的图片“越接近真实越好”。也就是说，G 希望 D(G(z)) 尽可能的大，这时 V(D, G) 会变小。因此我们看到公式最前面的记号是 min_G。

- D 的目的： D 的能力越强，D(x) 应该越大，D(G(z)) 应该越小，这时 V(D,G) 会变大。因此公式对于 D 来说就是求最大值（max_D）。

所以，我们回过头来看这个最大最小目标函数，里面包含了判别模型的优化，包含了生成模型以假乱真的优化，完美地阐释了这样一个优美的 GAN 算法。

## 13.3 循环生成对抗网络

循环生成对抗网络（CycleGAN）是传统 GAN 的特殊变体，它也可以创建新的数据样本，但是通过转换输入样本来实现，而不是从头开始创建。换句话说，它学会了从两个数据源转换数据。这些数据可由提供此算法数据集的科学家或开发人员进行选择。在两个数据源是狗的图片和猫的图片的情况下，该算法能够有效地将猫的图像转换为狗的图像，反之亦然。

传统的 GAN 是单向的，网络中由生成器 G 和判别器 D 两部分组成。假设两个数据域分别为 X、Y。G 负责把 X 域中的数据拿过来拼命地模仿成真实数据，并把它们藏在真实数据中让 D 猜不出来，而 D 就拼命地要把伪造数据和真实数据分开。经过二者的博弈以后，G 的伪造技术越来越厉害，D 的判别技术也越来越厉害。直到 D 再也分不出数据是真实的还是 G 生成的，这个时候对抗的过程达到一个动态的平衡。

单向 GAN 需要两个 loss：生成器的重建 loss 和判别器的判别 loss。

- 重建 loss：希望生成的图片与原图尽可能相似。
- 判别 loss：生成的假图片和原始真图片都会输入到判别器中。

而 CycleGAN 本质上是两个镜像对称的 GAN，构成了一个环形网络。两个 GAN 共享两个生成器，并各自带一个判别器，即共有两个判别器和两个生成器。一个单向 GAN 有两个 loss，两个 GAN 即有四个 loss。

CycleGAN 的网络架构有两个比较重要的特点：第一个特点就是双判别器，如图 13-4 所示，两个分布 X 与 Y，生成器 G、F 分别是 X 到 Y 和 Y 到 X 的映射，两个判别器 Dx、Dy 可以对转换后的图片进行判别；第二个特点就是 cycle-consistency loss，用数据集中其他的图来检验生成器，防止 G 和 F 过拟合，比如想把一幅小狗照片转化成梵高风格，如果没有 cycle-consistency loss，生成器可能会生成一幅梵高真实画作来骗过 Dx，而无视输入的小狗照片。

![](img/470b883072d48f1f2917a784b39d4327_216_0.png)

图 13-4

CycleGAN 有许多有趣的应用。下面介绍五类具体的应用，以展示 CycleGAN 这一技术 的应用能力。

### 1. 风格转换

风格转换指学习一个领域的艺术风格并将该艺术风格应用到其他领域，一般是将绘画的 艺术风格迁移到照片上。图 13-5 展示了使用 CycleGAN 学习莫奈、梵高、塞尚、浮世绘的绘 画风格，并将其迁移到风景照上的结果。

![](img/470b883072d48f1f2917a784b39d4327_217_0.png)

图 13-5

### 2. 物体变形

物体变形指将物体从一个类别转换到另一个类别，例如将狗转换为猫。CycleGAN 实现 了斑马和马的照片之间的相互转换。虽然马和斑马的大小和身体结构很相似，但它们在皮肤 颜色上有所差异，因此这种转换是有意义的。

如图 13-6 所示，CycleGAN 将图片中的苹果和橘子进行了相互的转换，苹果和橘子的 大小和结构相似但颜色不一样，因此这一转换同样合理。

![](img/470b883072d48f1f2917a784b39d4327_217_1.png)

图 13-6

### 3. 季节转换

季节转换指将在某一季节拍摄的照片转换为另一个季节的照片，例如将夏季的照片转换为冬季。如图 13-7 所示，CycleGAN 实现了冬天和夏天拍摄的风景照之间的相互转换。

![](img/470b883072d48f1f2917a784b39d4327_218_0.png)

图 13-7

### 4. 使用绘画生成照片

使用绘画生成照片指使用给定的绘画合成像照片一样逼真的图片，一般使用著名画家的画作或著名的风景画进行生成。如图 13-8 所示，CycleGAN 将莫奈的一些名画合成为类似照片的图片。

![](img/470b883072d48f1f2917a784b39d4327_218_1.png)

图 13-8

### 5. 图像增强

图像增强指通过某种方式对原图片质量进行提升。如图 13-9 所示，通过增加景深对近距离拍摄的花卉照片进行了增强。

![](img/470b883072d48f1f2917a784b39d4327_219_0.png)

图 13-9

## 13.4 利用 CycleGAN 进行图像风格迁移

把一幅图像的特征转移到另一幅图像，这是个非常激动人心的想法。把照片瞬间变成梵高、毕加索的画作风格，想想就很酷。利用 CycleGAN 进行风格迁移，可以实现 A 类图片和 B 类图片之间相互的风格迁移。比如 A 类图片为马的图片，B 类图片为斑马的图片，可以实现将马转化成斑马，也可以将斑马转化成马。整体架构使用的是 CycleGAN，即同时训练将 A 转化为 B 风格的 GAN 和将 B 转化为 A 风格的 GAN。训练耗费的时间比较长，但是一旦有了训练好的模型，生成图片的速度就比较快。

本节介绍的案例目的是利用 CycleGAN 对一批梵高油画图像和现实风景图像进行训练，利用现实风景图像生成具有梵高风格的图像。

本案例程序使用 vangogh2photo 数据集，该数据集的下载地址为 https://people.eecs.berkeley.edu/~taesung_park/CycleGAN/datasets/，在该网址的最下面可以找到 vangogh2photo 数据集，其大小为 292MB，下载后解压即可使用。其目录结构如图 13-10 所示。

```
目录结构：
/—— snapshot/      ...存放快照
|
|—— models/        ...存放训练出来的模型
|
|—— data/
    |
    |—— vangogh2photo/  ...某个数据集
        |
        |—— trainA/     ...类别为A的图片
        |
        |—— trainB/     ...类别为B的图片
```

图 13-10

### 13.4.1 导入必要的库

导入 PIL 图像处理库，其目的为了保存中间的快照。导入 glob 库，用于获取目录下所有文件名，以及获取数据。导入一些神经网络中的层，如 Conv2D（二维卷积层），BatchNormalization（批标准化层）、Add（加法层）、Conv2D Transpose（反卷积层）、Activation（激活层）。导入一些必要的库，代码如下：

```
from PIL import Image
import numpy as np
import keras.backend as K
from keras.models import Sequential, Model
from keras.layers import Conv2D, BatchNormalization, Input, Dropout, Add
from keras.layers import Conv2DTranspose, Reshape, Activation, Cropping2D, Flatten
from keras.layers import Concatenate
from keras.optimizers import RMSprop, SGD, Adam
from keras.layers.advanced_activations import LeakyReLU
from keras.activations import relu,tanh
from keras.initializers import RandomNormal
```

### 13.4.2 数据处理

数据读取的代码如下：

```
def load_image(fn, image_size):
    """
    加载一幅图片
    fn: 图像文件路径
    image_size: 图像大小
    """
    im = Image.open(fn).convert('RGB')
    # 切割图像（截取图像中间的最大正方形，然后将大小调整至输入大小）
    if (im.size[0] >= im.size[1]):
        im = im.crop(((im.size[0] - im.size[1])//2, 0, (im.size[0] + im.size[1])//2, im.size[1]))
    else:
        im = im.crop((0, (im.size[1] - im.size[0])//2, im.size[0], (im.size[0] + im.size[1])//2))
    im = im.resize((image_size, image_size), Image.BILINEAR)
    # 将 0 ~ 255 的 RGB 值转换为 [-1,1] 上的值
    arr = np.array(im)/255*2-1
    return arr
import glob
import random
class DataSet(object):
```

```
"""
用于管理数据的类
"""
def __init__(self, data_path, image_size = 256):
    self.data_path = data_path
    self.epoch = 0
    self.__init_list()
    self.image_size = image_size
def __init_list(self):
    self.data_list = glob.glob(self.data_path)
    random.shuffle(self.data_list)
    self.ptr = 0
def get_batch(self, batchsize):
    """
    取出 batchsize 幅图片
    """
    if (self.ptr + batchsize >= len(self.data_list)):
        batch = [load_image(x, self.image_size) for x in self.data_list[self.ptr:]]
        rest = self.ptr + batchsize - len(self.data_list)
        self.__init_list()
        batch.extend([load_image(x, self.image_size) for x in self.data_list[:rest]])
        self.ptr = rest
        self.epoch += 1
    else:
        batch = [load_image(x, self.image_size) for x in self.data_list[self.ptr:self.ptr + batchsize]]
        self.ptr += batchsize
    return self.epoch, batch
def get_pics(self, num):
    """
    取出 num 幅图片，用于快照
    不会影响队列
    """
    return np.array([load_image(x, self.image_size) for x in random.sample(self.data_list, num)])
def arr2image(X):
    """
    将 RGB 值从 [-1,1] 重新转回 [0,255]
    """
    int_X = ((X+1)/2*255).clip(0,255).astype('uint8')
    return Image.fromarray(int_X)
def generate(img, fn):
    """
    将一幅图片 img 送入生成网络 fn 中
    """
    r = fn([np.array([img])])[0]
    return arr2image(np.array(r[0]))
```

### 13.4.3 生成网络

首先定义一些常用的网络结构，以便于后续编写代码。代码如下：

```
```python
# 用于初始化
conv_init = RandomNormal(0, 0.02)
def conv2d(f, *a, **k):
    """
    卷积层
    """
    return Conv2D(f,
                  kernel_initializer = conv_init,
                  *a, **k)
def batchnorm():
    """
    标准化层
    """
    return BatchNormalization(momentum=0.9, epsilon=1.01e-5, axis=-1,)
```
```

我们的生成网络采用“残差网络”的结构，这种结构可以构建比较深的神经网络。残差网络的基本结构是一个“块（block）”，它的代码如下：

```
```python
def res_block(x, dim):
    """
    残差网络
    [x] --> [卷积] --> [标准化] --> [激活] --> [卷积] --> [标准化] --> [激活]
    --> [+] --> [激活]
    |
    |
    +-----------------------------------------------+
    """
    x1 = conv2d(dim, 3, padding="same", use_bias=True)(x)
    x1 = batchnorm()(x1, training=1)
    x1 = Activation('relu')(x1)
    x1 = conv2d(dim, 3, padding="same", use_bias=True)(x1)
    x1 = batchnorm()(x1, training=1)
    x1 = Activation("relu")(Add()([x,x1]))
    return x1
```
```

之所以采用这样的结构，是因为神经网络的深度不是越深越好，56层的神经网络产生的误差反而比18层的要大，这是和我们常识相违背的。直觉告诉我们，模型的深度加深，学习能力增强，因此更深的模型不应当比它更浅的模型产生更高的错误率。假设深层网络后面的几层网络都将输入原样输出，那么最坏的情况也应该和浅层网络误差一样。从这个想法出

发，我们把网络设定为着重于训练偏离输入的小变化，这就是残差网络。假设网络的输出是 H(x)=F(x)+x，那么残差网络着重学习的就 是 F(x)。如图 13-11 所示，表示残差网络的一个块。

![](img/470b883072d48f1f2917a784b39d4327_223_0.png)

图 13-11

使用残差网络可以大大加深网络的深度，提升训练效果。在生成网络中，也是使用残差网络。

生成网络是按照 3 个卷积层、9 个残差网络块和 3 个反卷积层的结构堆叠而成。而反卷积运算可以帮助我们从小尺寸的特征图中生成大尺寸的图像。

这里的反卷积，顾名思义是卷积操作的逆向操作。为了方便理解，假设卷积前为图片，卷积后为图片的特征。卷积，输入图片，输出图片的特征，这些操作的理论依据是统计不变性中的平移不变性（Translation Invariance），起到降维的作用。反卷积，输入图片的特征，输出图片，这些操作起到还原的作用。

有了残差网络和反卷积的知识，就比较容易理解生成网络的结构，以下是生成网络的代码：

```
def NET_G(ngf=64, block_n=6, downsampling_n=2, upsampling_n=2, image_size = 256):
    """
    生成网络
    采用 resnet 结构
    block_n 为残差网络叠加的数量
    论文中采用的参数为 若图片大小为128，采用6；若图片大小为256，采用9
    [第一层] 大小为7的卷积核 通道数量3->ngf
    [下采样] 大小为3的卷积核 步长为2 每层通道数量倍增
    [残差网络] 九个block叠加
    [上采样]
    [最后一层] 通道数量变回3
    """
    input_t = Input(shape=(image_size, image_size, 3))
    # 输入层
    x = input_t
    dim = ngf
    x = conv2d(dim, 7, padding="same")(x)
    x = batchnorm()(x, training = 1)
    x = Activation("relu")(x)
    # 第一层
```
