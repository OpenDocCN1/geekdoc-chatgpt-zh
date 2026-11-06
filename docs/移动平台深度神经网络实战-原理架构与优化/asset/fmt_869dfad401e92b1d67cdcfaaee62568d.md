
结合这些设计思路我们来看看`SqueezeNet`是如何做到模型压缩的。首先`SqueezeNet`采用了3个主要策略以确保网络中的参数数量更少。
1）使用1×1的卷积核来替代3×3的卷积核，以减少卷积层的参数数量，这样可以使卷积层参数数量减少9倍。
2）如果3×3的卷积核无法替代，那么就减少3×3卷积核的通道数量，比如将128×3×3的卷积核减少到32×3×3，那么卷积层参数数量就可以减少4倍。
3）在卷积层后接入降采样，以得到更大的激活图，也就是说将卷积层的步长设置成大于1，步长越大，卷积运算跳过的数据就越多。在早期进行网络设计时，一般前面的层步长比较大，后面的层步长比较小，导致网络输出的激活图也比较小，影响了分类精度。
图6-1是`SqueezeNet`的核心微观结构`Fire Module`，`Fire Module`的输入是一组1×1的卷积核，传送到混合了1×1的卷积核和3×3的卷积核的混合扩展层。这里可以调节`SqueezeNet`的3个超参数，分别是`Fire Module`的层数、`Fire Module`中1×1卷积核的数量和3×3卷积核的数量。如图6-1所示
![000](OEBPS/Images/143-1.jpg)
图6-1　`Fire Module`示意图
`SqueezeNet`的网络结构如图6-2所示。
![000](OEBPS/Images/143-2.jpg)
图6-2　`SqueezeNet`网络结构图
由图6-2我们可以看到除了输出、输出前后接入了卷积层，中间就是指定数量的`Fire Module`层。除了这些主要方法，`SqueezeNet`还使用了一些其他小技巧。
1）由于1×1和3×3的输出激活值的高度和宽度不等，因此输入3×3滤波器的数据会添加上1像素的0边界。
2）激活层使用`ReLU`替代`Sigmoid`。`ReLU`全称是Rectified Linear Unit，最常见的形式是`f(x) = max(0, x)`。`ReLU`训练的收敛速度会比传统的`Sigmoid`/`tanh`快很多。
3）网络中不使用全连接层。全连接层是参数最多的层，需要两层神经元完全连接，如果`n`个神经元的层和`m`个神经元的层全连接，将会有`m×n`个参数。去掉全连接层会减少大量的参数。
4）训练开始的学习率是0.04，随着训练线性减少学习率。
这些小技巧都是经过反复实验与经验的总结，值得我们多多借鉴。

### 6.2.4　实战：用PyTorch实现`SqueezeNet`
现在我们来看一下如何使用`PyTorch`实现`SqueezeNet`。
首先我们来看网络定义。在网络定义`model.py`中，如代码清单6-1所示。
代码清单6-1　`model.py`
```
 1 import torch
 2 import torch.nn as nn
 3 from torch.autograd import Variable
 4 import torch.functional as F
 5 import numpy as np
 6 import torch.optim as optim
 7 import math
 8 
 9 class fire(nn.Module):
10     def __init__(self, inplanes, squeeze_planes, expand_planes):
11         super(fire, self).__init__()
12         self.conv1 = nn.Conv2d(inplanes, squeeze_planes, kernel_size=1, stride=1)
13         self.bn1 = nn.BatchNorm2d(squeeze_planes)
14         self.relu1 = nn.ReLU(inplace=True)
15         self.conv2 = nn.Conv2d(squeeze_planes, expand_planes, kernel_size=1, stride=1)
16         self.bn2 = nn.BatchNorm2d(expand_planes)
17         self.conv3 = nn.Conv2d(squeeze_planes, expand_planes, kernel_size=3, stride=1, padding=1)
18         self.bn3 = nn.BatchNorm2d(expand_planes)
19         self.relu2 = nn.ReLU(inplace=True)
20 
21         # using MSR initilization
22         for m in self.modules():
23             if isinstance(m, nn.Conv2d):
24                 n = m.kernel_size[0] * m.kernel_size[1] * m.in_channels
25                 m.weight.data.normal_(0, math.sqrt(2./n))
26 
27     def forward(self, x):
28         x = self.conv1(x)
29         x = self.bn1(x)
30         x = self.relu1(x)
31         out1 = self.conv2(x)
32         out1 = self.bn2(out1)
33         out2 = self.conv3(x)
34         out2 = self.bn3(out2)
35         out = torch.cat([out1, out2], 1)
36         out = self.relu2(out)
37         return out
38 
39 
40 class SqueezeNet(nn.Module):
41     def __init__(self):
42         super(SqueezeNet, self).__init__()
43         self.conv1 = nn.Conv2d(3, 96, kernel_size=3, stride=1, padding=1) # 32
44         self.bn1 = nn.BatchNorm2d(96)
45         self.relu = nn.ReLU(inplace=True)
46         self.maxpool1 = nn.MaxPool2d(kernel_size=2, stride=2) # 16
47         self.fire2 = fire(96, 16, 64)
48         self.fire3 = fire(128, 16, 64)
49         self.fire4 = fire(128, 32, 128)
50         self.maxpool2 = nn.MaxPool2d(kernel_size=2, stride=2) # 8
51         self.fire5 = fire(256, 32, 128)
52         self.fire6 = fire(256, 48, 192)
53         self.fire7 = fire(384, 48, 192)
54         self.fire8 = fire(384, 64, 256)
55         self.maxpool3 = nn.MaxPool2d(kernel_size=2, stride=2) # 4
56         self.fire9 = fire(512, 64, 256)
57         self.conv2 = nn.Conv2d(512, 10, kernel_size=1, stride=1)
58         self.avg_pool = nn.AvgPool2d(kernel_size=4, stride=4)
59         self.softmax = nn.LogSoftmax(dim=1)
60         for m in self.modules():
61             if isinstance(m, nn.Conv2d):
62                 n = m.kernel_size[0] * m.kernel_size[1] * m.in_channels
63                 m.weight.data.normal_(0, math.sqrt(2. / n))
64             elif isinstance(m, nn.BatchNorm2d):
65                 m.weight.data.fill_(1)
66                 m.bias.data.zero_()
67 
68 
69     def forward(self, x):
70         x = self.conv1(x)
71         x = self.bn1(x)
72         x = self.relu(x)
73         x = self.maxpool1(x)
74         x = self.fire2(x)
75         x = self.fire3(x)
76         x = self.fire4(x)
77         x = self.maxpool2(x)
78         x = self.fire5(x)
79         x = self.fire6(x)
80         x = self.fire7(x)
81         x = self.fire8(x)
82         x = self.maxpool3(x)
83         x = self.fire9(x)
84         x = self.conv2(x)
85         x = self.avg_pool(x)
86         x = self.softmax(x)
87         return x
88 
89 def fire_layer(inp, s, e):
90     f = fire(inp, s, e)
91     return f
92 
93 def squeezenet(pretrained=False):
94     net = SqueezeNet()
95 
96     return net
```
第1～7行，是导入模块。这里主要导入了`PyTorch`，以及`PyTorch`的神经网络模块`nn`、自动梯度计算模块`autograd`、`numpy`和参数训练优化模块`optim`。
第9行，开始是`fire`模块定义。这里`fire`是一个类，继承自`PyTorch`的`nn.Module`。`nn.Module`是`PyTorch`中所有层的基类（类似于我们自己编写的`layer`），可以自动注册到`PyTorch`的网络中。
第10行，定义了该类的初始化方法`__init__`，这里初始化了一个`Fire Module`的网络结构。
第12～14行，定义了第1组1×1的卷积模块，以及该组模块后面的`BatchNorm`和`ReLu`。
第15～16行，定义了第2组中1×1的卷积模块，以及该组模块后的`BatchNorm`。
第17～18行，定义了第2组中3×3的卷积模块，以及该组模块后的`BatchNorm`，最后再定义了一个公共的`ReLU`模块。
第21～25行，进行`MSR`初始化，主要就是根据刚刚定义的3组卷积模块对所有输入参数进行归一化。整体结构如图6-1所示（详见6.2.3节图6-1）。
第27行，定义了`forward`方法，该方法就是模块的前向计算，也就是模块的核心内容。模块内容很简单，就是将输入同时输入到第1组1×1的卷积模块中，然后通过第1个`BatchNorm`，再通过第1个`ReLU`，这相当于是第1组模块的输出。
第31～32行，是将第1组的输出输入第2组3×3的卷积模块，然后通过第2个`Batch-Norm`。然后将第1组的输出同时通过第2组1×1的卷积模块，再通过第2个`BatchNorm`，接着将两部分的输出通过`cat`连接到一起，最后再接入第2个`ReLU`输出。这就是整个`Fire Module`的前向过程。
定义好了`SqueezeNet`的核心组件`Fire Module`后我们就来组织整个`SqueezeNet`。
第40行，定义了`SqueezeNet`类，该类也继承了`nn.Module`。我们可以看到`PyTorch`里`Module`是一个非常灵活的类，无论是层还是网络，只要你想使用`PyTorch`的操作符，就可以继承`PyTorch`的`Module`，然后将其他`Module`子类的实例保存在你的对象的`self`中，这样`PyTorch`就会自动注册所有你用到的其他`Module`，所以在`PyTorch`中，网络、层与操作符都是一样的，这具有非常强的一致性。
第41行，定义了网络的初始化函数。这里我们依次定义了一个3×3的卷积层，一个`BatchNorm`层，一个`ReLU`层，一个2×2的`MaxPooling`层，后面连接了3个`Fire Module`，接着又是一个2×2的`MaxPooling`，再连接4个`Fire Module`，再接着1个2×2的`MaxPooling`，再接一个`Fire Module`和1×1的卷积层，最后再接入一个`AvgPooling`层。网络输出是一个`Softmax`层，以完成多分类。
第60行，开始将所有卷积层的参数归一化，并将所有`BatchNorm`层中的权重参数设置为1，偏置参数设置为0。
第69～87行，是网络的前向处理，这里我们将初始化函数中定义好的所有层依次连接就结束了。
第89～91行，定义了一个`fire_layer`函数，用于生成一个`Fire Module`层对象。
第93～96行，定义了一个`squeezenet`函数，用于生成一个`SqueezeNet`对象。
定义好模型后，我们开始编写`main.py`，以完成训练、评估和测试工作，如代码清单6-2所示。
代码清单6-2　`main.py`
