

### 9.3.2 GAN的原理

下面我们通过一个例子详细介绍一下GAN的原理。

先定义一个生成器G，同时还需要定义一个随机噪声 Z，噪声 Z 是维度设为100维且均值为0、方差为1的高斯噪声。生成模型生成数据的原理是通过 Z 去拟合数据分布，同时有一个可以自主学习的参数 $\theta_g$，参数 $\theta_g$ 用来调节 Z 拟合真实的数据分布。

其次还需要定义一个判别器D，D也是一个MLP，它也有可以自主学习的参数 $\theta_d$。其作用是在数据输入之后输出一个标量，这个标量是用来判别输入的数据 X 是来自G生成的数据还是来自真实的数据，如果是来自真实的数据则赋值1；如果是生成的数据则赋值0。当判别器被训练得特别好的时候，D(G(Z))是0这意味着1-D(G(Z))是1，也就是说，log(1-D(G(Z)))就会输出一个大于0的值。

GAN模型的目标就是经过对抗训练后能够生成以假乱真的数据，这意味着生成器G生成的数据要逐渐向真实数据靠拢，向量 Z 尽可能地拟合出真实数据分布。图9-3所示是生成器G生成的数据逐渐向真实数据靠拢的过程。

![](img/df1b219b2c6176235f1ea4f385724826_194_0.png)

图9-3 生成器G生成的数据逐渐向真实数据靠拢的过程

图9-3中的橙色线表示判别模型的概率分布，黑色的虚线表示原始数据的概率分布，绿色实线表示生成模型生成的数据的概率分布。

图9-3（a）给出的是训练刚开始的情况，其中橙色的波浪线表示判别模型概率分布波动很大，绿线和黑色虚线的差异表示生成的数据概率分布与真实数据概率分布有着很大的差异。图9-3（b）给出的是通过训练，判别器能够判别出真实数据和生成的数据，此时判别器的概率分布曲线变得光滑，如图中的橙线所示，橙线上点的位置越高表示判别器认为生成器生成的数据是真实数据的概率越高。继续训练一段时间之后，生成器生成的数据概率分布与真实数据概率分布越来越相近，如图9-3（c）所示，绿线开始往左向黑色虚线靠近。经过相当充足的训练后，出现的最佳结果如图9-3（d）所示，可以看到，生成器生成的数据概率分布与真实数据概率分布几乎一致，此时判别器判定生成的数据为假的概率为50%。

我们用 $P_{data}(x)$ 来代表真实图片集的分布，$x$ 代表真实的数据，$P_G(x)$ 代表的是生成器生成的数据分布，一般会加上一个 $\theta$ 来控制，一般生成器使用的随机噪声是高斯分布，此时 $\theta$ 就是每个高斯分布的平均值和方差，因此生成器的最终概率分布是 $P_G(x;\theta)$ 。

生成器G的主要目的是生成的数据概率分布与真实数据分布一样，即 $P_{data} = P_G$，如图9-3(d)所示，这意味着 $P_G(x;\theta)$ 随着生成的数据概率分布和真实数据概率分布的接近，期望变得越来越大。此时让生成器生成的数据被判别为真实数据的问题就变成了求生成器的似然 $L = \prod_{i=1}^m P_G(x^i;\theta)$ 的最大似然值。我们可以用一个 $\theta^*$ 来最大化似然。具体过程如下：

```
\theta^* = \arg \max_{\theta} \prod_{i=1}^m P_G (x^i;\theta)
= \arg \max_{\theta} \log \prod_{i=1}^m P_G (x^i;\theta)
= \arg \max_{\theta} \sum_{i=1}^m \log P_G (x^i;\theta)
= \arg \max_{\theta} E_{x \sim P_{data}} \left[ \log P_G (x;\theta) \right]
= \arg \max_{\theta} \int_x P_{data} (x) \log P_G (x;\theta) dx - \int_x P_{data} (x) \log P_{data} (x) dx
= \arg \max_{\theta} \int_x P_{data} (x)(\log P_G (x;\theta) - \log P_{data} (x)) dx
= \arg \min_{\theta} \int_x P_{data} (x) \log \frac{P_{data} (x)}{P_G (x;\theta)} dx
= \arg \min_{\theta} \text{KL}(P_{data} (x) || P_G (x;\theta)))
```

可以看出，在最大化似然时，添加了log，将结果变成求 x 在生成的数据概率分布中的log似然期望。这么做不仅不会影响结果还能够简化计算过程。

通过求概率积分，可以大致求出真实分布中的所有 x 的期望，因此在转换成求log似然期望之后便转换成积分运算。又因为 \(\int_x P_{data} (x) \log P_{data} (x)dx\) 这一项和 \(\theta\) 不相干，所以在减去这一项之后，结果是等价的。之后进行正常的提取公因式再合并，就得到

\(\arg \max_{\theta} \int_x P_{data} (x) \left( \frac{\log P_G (x;\theta)}{\log P_{data} (x)} \right) dx\)，求最大值，此时将分子、分母对换就能得到
\(\arg \min_{\theta} \int_x P_{data} (x) \log \frac{P_{data} (x)}{P_G (x;\theta)}\)，也就是 \(\arg \min_{\theta} \text{KL}(P_{data} (x) || P_G (x;\theta))\) 。最大化似然问题就变成了求KL散度，这次侧面也验证了当 \(P_{data} = P_G\) 时，生成数据的概率分布似然最大。

判别器主要是判别接收数据的真假，判别器的概率分布最大值就是判别器所达成的最佳目标；而生成器希望自己生成的数据被判定成真实数据，也就是 \(V(D, G)\) 的值最小。生成器和判别器互相对抗，就能够得到GAN最核心的公式，即式(9-6)：

```
\(\min_G \max_D V(D, G) = E_{x \sim P_{data} (x)}[\log D(x)] + E_{z \sim P_z}[\log(1 - D(G(z)))]\)
```

## 9.4 GAN的训练过程

GAN的优化过程是先训练判别器，再用训练好的判别器去训练生成器，当生成器被训练到能够迷惑判别器时，判别器根据返回的loss再优化自己。整个过程不断迭代进行，直到得到最好的生成器和最好的判别器。

之所以选择先训练判别器，是因为如果在没有评判标准的前提下进行生成器的训练，最终生成的也几乎都是噪声并不是目标数据。因此，我们需要先训练判别器，才能保证模型正常运行。

下面我们详细介绍GAN的训练过程。

### 9.4.1 训练判别器D

在第一阶段，我们先训练判别器，因此需要先固定生成器再训练判别器。我们使用一个随机噪声让生成网络不断地生成假的数据，再利用判别器去判断这些数据是否为真实的数据。起初的向量 Z 拟合的数据分布和真实数据分布差得很远，因此判别器很容易就能将生成器生成的数据分辨出来。

### 9.4.2 训练生成器G

第二阶段训练生成器（如清单9-2所示）。此时判别器D已经训练好了，需要固定判别器进行生成器的训练。经过第一阶段的训练，判别器已经能够很完美地将生成器生成的数据分辨出来，因此再训练判别器D已经失去意义，此时需要固定判别器D，进行生成器的训练，直到判别器不能够分辨出生成器生成的数据是假数据，一次训练过程结束。

### 清单9-2 训练生成器

```
# 训练生成器
z = torch.normal(0, 0.02, (num_img, 100), dtype=torch.float32).cuda()
# z = Variable(torch.randn(num_img, z_dimension)).cuda() # 得到随机噪声
# 随机噪声输入生成器中，得到一幅假的图片
fake_img = G(z)
# 经过判别器得到的结果
output = D(fake_img)
# 得到的假的图片与真实的图片的label的损失
g_loss = criterion(output, real_label)
# 定义优化器
# 梯度归0
g_optimizer.zero_grad()
# 进行反向传播
g_loss.backward()
# step() 一般用在反向传播后面，用于更新生成网络的参数
g_optimizer.step()
```

图9-4所示的是GAN训练过程的一次迭代过程。

![](img/df1b219b2c6176235f1ea4f385724826_198_0.png)

图9-4 GAN训练过程的一次迭代过程

## 9.5 GAN的应用与代码示例

在本书中，我们选用手写数据集来展示GAN的应用。

首先进行库的调用（如清单9-3所示），本案例使用的库是torch、torchvision和os。

### 清单9-3 库的调用

```
import torch.nn as nn
from torchvision import transforms
from torchvision import datasets
from torchvision.utils import save_image
import torch.autograd
from torch.autograd import Variable
import os
```

创建保存数据的文件夹和导入数据，如清单9-4所示。

### 清单9-4 创建保存数据的文件夹和导入数据

```
# 创建文件夹
if not os.path.exists('./img'):
    os.mkdir('./img')
# 进行图片大小的确认和预处理
def to_img(x):
    out = 0.5*(x+1)
    # clamp函数可以将随机变化的数值限制在一个给定的区间[min, max]内
    out = out.clamp(0, 1)
    # view()函数的作用是将一个多行的Tensor拼接成一行
    out = out.view(-1, 1, 28, 28)
    return out
# 一批128个
batch_size = 128
# 总共100批
num_epoch = 100
# 噪声维度
z_dimension = 100
# 图像预处理
img_transform = transforms.Compose([
    transforms.ToTensor(),
    #归一化处理
    transforms.Normalize([0.5], [0.5])
])
# 加载手写数据集，此处我们是将数据集存放在本地
# 数据集载入
# mnist数据集下载
mnist = datasets.MNIST(
    root='./data/mnist/',
    train=True,
    transform=img_transform,
    download=True)
# 数据载入(批次读取)
dataloader = torch.utils.data.DataLoader(
    dataset=mnist,
    batch_size=128,
    shuffle=True)
```

定义判别器discriminator（如清单 9-5 所示），判别器使用多层网络。首先我们将图片28×28展开成784，然后通过多层感知器，中间经过LeakyReLU()激活函数，最后通过Sigmoid()激活函数得到一个0和1之间的概率进行二分类。

### 清单9-5 定义判别器discriminator

```
class discriminator(nn.Module):
    def __init__(self):
        super(discriminator, self).__init__()
        self.dis = nn.Sequential(
            # 输入特征数为784，输出为256
            nn.Linear(784, 256),
            # 进行非线性映射
            nn.LeakyReLU(0.2),
            # 进行线性映射
            nn.Linear(256, 256),
            nn.LeakyReLU(0.2),
            nn.Linear(256, 1),
            # 激活函数，二分类问题中，Sigmoid可以将实数映射到 [0,1]，作为概率值
            # 多分类用Softmax函数
            nn.Sigmoid()
        )
    def forward(self, x):
        x = self.dis(x)
        return x.reshape(-1)
```

定义生成器generator（如清单9-6所示），输入一个100维的0~1的高斯分布，通过第一层线性变换将其映射到256维，然后通过激活函数，接着进行一个线性变换，再经过一个激活函数，然后经过线性变换将其变成784维，最后经过Tanh()激活函数是希望生成假的图片数据分布。

### 清单9-6 定义生成器generator

```
class generator(nn.Module):
    def __init__(self):
        super(generator, self).__init__()
        self.gen = nn.Sequential(
            # 用线性变换将输入映射到256维
            nn.Linear(100, 256),
            # ReLU激活
            nn.ReLU(True),
            # 线性变换
            nn.Linear(256, 256),
            # ReLU激活
            nn.ReLU(True),
            # 线性变换
            nn.Linear(256, 784),
            # Tanh激活使得生成数据分布在[-1,1]
            nn.Tanh()
        )
    def forward(self, x):
        x = self.gen(x)
        return x
```

训练判别器（如清单9-7所示），我们将其训练分为两部分：其一，真的图像判别为真；其二，假的图像判别为假。首先需要定义损失的度量方式，使用二分类的交叉熵；其次定义优化函数，优化函数的学习率为0.0003。在此过程中，生成器参数不断更新。

### 清单9-7 训练判别器

```
for epoch in range(num_epoch):
    for i, (img, _) in enumerate(dataloader):
        # print(img.shape)
        num_img = img.size(0)
        real_img = img.view(num_img, -1).cuda()
        real_label = torch.ones(num_img).cuda()
        fake_label = torch.zeros(num_img).cuda()
        # 计算真实图片的损失
        # 将真实图片放入判别器中
        real_out = D(real_img)
        print(real_out.shape[0])
        # 得到真实图片的损失
        d_loss_real = criterion(real_out, real_label)
        # 得到真实图片的判别值，输出的值越接近1越好
        real_scores = real_out
        # 计算假的图片的损失
        z = torch.normal(0, 0.02, (num_img, 100), dtype=torch.float32).cuda()
        # 随机噪声放入生成网络中，生成一张假的图片
        fake_img = G(z)
        # 判别器判断假的图片
        fake_out = D(fake_img)
        # 得到假的图片的损失
        d_loss_fake = criterion(fake_out, fake_label)
        # 得到假图片的判别值，对于判别器来说，假图片的损失越接近0越好
        fake_scores = fake_out
        # 损失函数和优化
        # 损失包括判真损失和判假损失
        d_loss = d_loss_real + d_loss_fake
        # 在反向传播之前，先将梯度归0
        d_optimizer.zero_grad()
        # 将误差反向传播
        d_loss.backward()
        # 更新参数
        d_optimizer.step()
```

固定判别器，训练生成器（如清单9-8所示），目的是使生成的图片被判别器判断为真的。训练时将生成的图片传入判别器的结果和真实的对比，通过反向传播更新新的参数来更新生成网络中的参数，以此来训练网络，使得生成的图片被判别器判别为真的。

### 清单9-8 训练生成器

```
z = torch.normal(0, 0.02, (num_img, 100), dtype=torch.float32).cuda()
# z = Variable(torch.randn(num_img, z_dimension)).cuda() # 得到随机噪声

# 随机噪声输入生成器中，得到一幅假的图片
fake_img = G(z)
# 经过判别器得到的结果
output = D(fake_img)
# 得到的假的图片与真实的图片的label的损失
g_loss = criterion(output, real_label)
# 梯度归0
g_optimizer.zero_grad()
# 进行反向传播
g_loss.backward()
# step()一般用在反向传播后面，用于更新生成网络的参数
g_optimizer.step()
# 打印中间的损失
if (i + 1) % 100 == 0:
    # 打印的是真实图片的损失均值
    print('Epoch[{}/{}]，d_loss:{:.6f},g_loss:{:.6f}' 'D real: {:.6f},D fake: {:.6f}'.format(epoch, num_epoch, d_loss.item(), g_loss.item(), real_scores.data.mean(), fake_scores.data.mean()))
```

保存图片和训练好的模型，如清单9-9所示。

### 清单9-9 保存图片和训练好的模型

```
if epoch == 0:
    real_images = to_img(real_img.cpu().data)
    save_image(real_images, './img/real_images.png')
    fake_images = to_img(fake_img.cpu().data)
    save_image(fake_images, './img/fake_images-
```

