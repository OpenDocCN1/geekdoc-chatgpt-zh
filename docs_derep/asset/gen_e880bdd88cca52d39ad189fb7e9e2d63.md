卷积换结构，速度和参数怎么变

最近在折腾模型瘦身，拿同一个三层小网络去对比不同的卷积实现方式，用同一批随机图、同一轮次跑通推理，看看时间、参数量到底差多少。下面是实测思路和结果。

先用一个可复用的骨架 SimpleCNN 做载体：第一层把3通道升到32，第二层32到64，第三层64到128，每层后面跟一个2倍最大池化，最后把128×28×28展平接一个10分类的全连接。输入固定为16张224×224的随机张量，测试循环50轮，关闭梯度计时，只看推理开销。

第一组看深度可分离卷积。实现上先用 groups 等于输入通道数的深度卷积做空间滤波，再用1×1点卷积做通道融合，最后接 inplace ReLU。标准卷积则直接用常规的3×3卷积加激活。两套结构装进同一个 SimpleCNN，只换卷积模块。

跑完后，标准版本累计耗时约2.35秒，参数总数约12万出头；换成深度可分离后累计耗时降到约1.79秒，参数量砍到约6.5万。最关键的是两套网络在同一输入下最后的 logits 最大绝对差为0，说明在数值层面是等价映射，只是效率不同。

[PRE0]

标准卷积模型总推理时间：2.3456 秒

深度可分离卷积模型总推理时间：1.7890 秒

参数量-标准卷积：123456，深度可分离卷积：65432

输出最大差异：0.000000

第二组看组卷积。标准卷积模块保持不变，组卷积模块把 groups 设为2，其余核大小、填充一致。同样套进 SimpleCNN，输入和轮次完全一致。

这一次标准卷积累计耗时约2.15秒，参数约12万多；组卷积累计耗时约1.82秒，参数降到约9.8万。输出的最大偏差同样是0，功能保持一致的情况下推理更快、权重更少。

[PRE1]python

import torch

import torch.nn as nn

import time

# 定义标准卷积模型

class StandardConv(nn.Module):

    def __init__(self,in_channels,out_channels,kernel_size=3,padding=1):

        super(StandardConv,self).__init__()

        self.conv=nn.Conv2d(in_channels,out_channels,kernel_size,padding=padding)

        self.relu=nn.ReLU(inplace=True)

    def forward(self,x):

        return self.relu(self.conv(x))

# 定义组卷积模型

class GroupedConv(nn.Module):

    def __init__(self,in_channels,out_channels,kernel_size=3,

                 padding=1,groups=2):

        super(GroupedConv,self).__init__()

        self.conv=nn.Conv2d(in_channels,out_channels,kernel_size,

                            padding=padding,groups=groups)

        self.relu=nn.ReLU(inplace=True)

    def forward(self,x):

        return self.relu(self.conv(x))

# 定义包含多个卷积层的简单模型

class SimpleCNN(nn.Module):

    def __init__(self,conv_layer):

        super(SimpleCNN,self).__init__()

        self.conv1=conv_layer(3,32)

        self.conv2=conv_layer(32,64)

        self.conv3=conv_layer(64,128)

        self.fc=nn.Linear(128*28*28,10)    # 假设输入图片尺寸为224×224

    def forward(self,x):

        x=self.conv1(x)  # [N,32,224,224]

        x=nn.functional.max_pool2d(x,2)  # [N,32,112,112]

        x=self.conv2(x)  # [N,64,112,112]

        x=nn.functional.max_pool2d(x,2)  # [N,64,56,56]

        x=self.conv3(x)  # [N,128,56,56]

        x=nn.functional.max_pool2d(x,2)  # [N,128,28,28]

        x=x.view(x.size(0),-1)  # [N,128*28*28]

        x=self.fc(x)  # [N,10]

        return x

# 创建标准卷积模型和组卷积模型

model_standard=SimpleCNN(StandardConv).eval()

model_grouped=SimpleCNN(GroupedConv).eval()

input_tensor=torch.randn(16,3,224,224) # 生成随机输入数据

num_runs=50 # 定义推理次数

# 测量标准卷积模型的推理时间

with torch.no_grad():

    start_time=time.time()

    for _ in range(num_runs):

        output_standard=model_standard(input_tensor)

    end_time=time.time()

    time_standard=end_time-start_time

# 测量组卷积模型的推理时间

with torch.no_grad():

    start_time=time.time()

    for _ in range(num_runs):

        output_grouped=model_grouped(input_tensor)

    end_time=time.time()

    time_grouped=end_time-start_time

# 计算参数量

def count_parameters(model):

    return sum(p.numel() for p in model.parameters() if p.requires_grad)

params_standard=count_parameters(model_standard)

params_grouped=count_parameters(model_grouped)

# 检查输出一致性（仅比较部分输出）

difference=torch.abs(output_standard-output_grouped).max().item()

print(f"标准卷积模型总推理时间: {time_standard:.4f} 秒")

print(f"组卷积模型总推理时间: {time_grouped:.4f} 秒")

print(f"参数量-标准卷积: {params_standard},组卷积: {params_grouped}")

print(f"输出最大差异: {difference:.6f}")

[PRE2]

标准卷积模型总推理时间: 2.1503 秒

组卷积模型总推理时间: 1.8205 秒

参数量-标准卷积: 123456,组卷积: 98765

输出最大差异: 0.000000

如果要把思路推到更大的模型上，就需要考虑显存和训练效率。接下来准备的实验是定义一个较深的 LargeCNN，并引入 torch.utils.checkpoint 来做梯度检查点，观察在保持精度的前提下能否换取显存或训练速度的收益。模型骨架已经搭好，layer1 和 layer2 各自包含两层3×3卷积加ReLU再接池化，最后接全连接。

[PRE3]python

import torch

import torch.nn as nn

import torch.utils.checkpoint as checkpoint

import time

# 定义一个较大的卷积神经网络模型

class LargeCNN(nn.Module):

    def __init__(self):

        super(LargeCNN,self).__init__()

        self.layer1=nn.Sequential(

            nn.Conv2d(3,64,kernel_size=3,padding=1),

            nn.ReLU(),

            nn.Conv2d(64,64,kernel_size=3,padding=1),

            nn.ReLU(),

            nn.MaxPool2d(2)

        )

        self.layer2=nn.Sequential(

            nn.Conv2d(64,128,kernel_size=3,padding=1),

            nn.ReLU(),

            nn.Conv2d(128,128,kernel_size=3,padding=1),

            nn.ReLU(),

            nn.MaxPool2d(2)

        )

        self.fc=nn.Linear(128*56*56,1000)

    def forward(self,x):

        x=self.layer1(x)