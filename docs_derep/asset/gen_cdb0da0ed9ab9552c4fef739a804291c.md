MNIST 手写识别实战：从数据到可分离卷积

想把一个标准的 MNIST 训练跑通，最关键的是把数据形状、网络骨架、训练循环和参数效率这几件事说清楚。下面用一份完整的 PyTorch 示例把流程拆开，重点看卷积堆叠如何落地，以及 groups 参数背后对应的深度可分离卷积为什么更省参数。

先把原始的 NumPy 数据升一维，让它符合 PyTorch 卷积层的输入约定。扩充后可以直观看出整体规模。

x_train = np.expand_dims(x_train,axis=1) #在指定维度上进行扩充

print(x_train.shape)

[PRE0]

(60000, 1, 28, 28)

接下来是网络定义。整体结构很紧凑：三层卷积做特征提取，最后用线性层做十分类。维度变换用 einops 的 Rearrange 实现，也可以直接用 Flatten。

[PRE1]python

import torch

import torch.nn as nn

import numpy as np

import einops.layers.torch as elt

class MnistNetwork(nn.Module):

    def __init__(self):

        super(MnistNetwork, self).__init__()

        #前置的特征提取模块

        self.convs_stack = nn.Sequential(

            nn.Conv2d(1,12,kernel_size=7),  #第一个卷积层

            nn.ReLU(),

            nn.Conv2d(12,24,kernel_size=5),  #第二个卷积层

            nn.ReLU(),

            nn.Conv2d(24,6,kernel_size=3)  #第三个卷积层

        )

        #最终分类器层

        self.logits_layer = nn.Linear(in_features=1536,out_features=10)

    def forward(self,inputs):

        image = inputs

        x = self.convs_stack(image)

        #elt.Rearrange的作用是对输入数据维度进行调整，读者可以使用torch.nn.Flatten函数完成此工作

        x = elt.Rearrange("b c h w -> b (c h w)")(x)

        logits = self.logits_layer(x)

        return logits

model = MnistNetwork()

torch.save(model,"model.pth")

保存模型只是第一步，真正跑起来需要加载数据、送到设备、编译加速并循环优化。

[PRE2]python

import torch

import torch.nn as nn

import numpy as np

import einops.layers.torch as elt

#载入数据

x_train = np.load("../dataset/mnist/x_train.npy")

y_train_label = np.load("../dataset/mnist/y_train_label.npy")

x_train = np.expand_dims(x_train,axis=1)

print(x_train.shape)

class MnistNetwork(nn.Module):

    def __init__(self):

        super(MnistNetwork, self).__init__()

        self.convs_stack = nn.Sequential(

            nn.Conv2d(1,12,kernel_size=7),

            nn.ReLU(),

            nn.Conv2d(12,24,kernel_size=5),

            nn.ReLU(),

            nn.Conv2d(24,6,kernel_size=3)

        )

        self.logits_layer = nn.Linear(in_features=1536,out_features=10)

    def forward(self,inputs):

        image = inputs

        x = self.convs_stack(image)

        x = elt.Rearrange("b c h w -> b (c h w)")(x)

        logits = self.logits_layer(x)

        return logits

device = "cuda" if torch.cuda.is_available() else "cpu"

#注意需要将model发送到GPU计算

model = MnistNetwork().to(device)

model = torch.compile(model)

loss_fn = nn.CrossEntropyLoss()

optimizer = torch.optim.SGD(model.parameters(), lr=1e-4)

batch_size = 128

for epoch in range(42):

    train_num = len(x_train)//128

    train_loss = 0.

    for i in range(train_num):

        start = i * batch_size

        end = (i + 1) * batch_size

        x_batch = torch.tensor(x_train[start:end]).to(device)

        y_batch = torch.tensor(y_train_label[start:end]).to(device)

        pred = model(x_batch)

        loss = loss_fn(pred, y_batch)

        optimizer.zero_grad()

        loss.backward()

        optimizer.step()

        train_loss += loss.item()  # 记录每个批次的损失值

    # 计算并打印损失值

    train_loss /= train_num

    accuracy = (pred.argmax(1) == y_batch).type(torch.float32).sum().item() / batch_size

    print("epoch: ",epoch,"train_loss:", round(train_loss,2),"accuracy:",round(accuracy,2))

训练日志能反映学习曲线，前十个 epoch 的走势如下，损失缓慢下降，准确率逐步提升。

[PRE3]

epoch: 0 train_loss: 2.3 accuracy: 0.15

epoch: 1 train_loss: 2.3 accuracy: 0.16

epoch: 2 train_loss: 2.29 accuracy: 0.24

epoch: 3 train_loss: 2.29 accuracy: 0.27

epoch: 4 train_loss: 2.29 accuracy: 0.34

epoch: 5 train_loss: 2.28 accuracy: 0.35

epoch: 6 train_loss: 2.28 accuracy: 0.37

epoch: 7 train_loss: 2.27 accuracy: 0.38

epoch: 8 train_loss: 2.26 accuracy: 0.41

epoch: 9 train_loss: 2.24 accuracy: 0.45

epoch: 10 train_loss: 2.23 accuracy: 0.48

为了理解参数效率，先看 Conv2d 的完整签名，groups 就是控制通道分组的关键开关。

[PRE4]python

class Conv2d(_ConvNd):

    ...

    def __init__(

        self, in_channels: int, out_channels: int, kernel_size: _size_2_t, stride: _size_2_t = 1,

        padding: Union[str, _size_2_t] = 0, dilation: _size_2_t = 1, groups: int = 1, bias: bool = True,

        padding_mode: str = 'zeros',  # TODO: refine this type

        device=None,

        dtype=None

    ) -> None:

深度可分离卷积正是利用 groups 把跨通道和空间内计算拆开。groups 等于输入通道数时实现逐通道卷积，再接 1x1 卷积融合。

[PRE5]python

#group=3是依据通道数设置的分离卷积数

Conv2d(in_channels=3, out_channels=3, kernel_size=3, groups=3) #这是第一步完成跨通道计算

Conv2d(in_channels=4, out_channels=4, kernel_size=1) #完成平面内计算

最后用参数数量做对比，线性层、普通卷积、深度可分离卷积的差异一目了然。

[PRE6]python

import torch

from torch.nn import Conv2d, Linear

linear = Linear(in_features=3*28*28, out_features=3*28*28)

linear_params = sum(p.numel() for p in linear.parameters() if p.requires_grad)

conv = Conv2d(in_channels=3, out_channels=3, kernel_size=3)

params = sum(p.numel() for p in conv.parameters() if p.requires_grad)

depth_conv = Conv2d(in_channels=3, out_channels=3, kernel_size=3, groups=3)

point_conv = Conv2d(in_channels=3, out_channels=3, kernel_size=1)

# 需要注意的是，这里是先进行depth，然后进行逐点卷积，从而两者结合，就得到了深度、可分离、卷积

depthwise_separable_conv = torch.nn.Sequential(depth_conv, point_conv)

params_depthwise = sum(p.numel() for p in depthwise_separable_conv.parameters() if p.requires_grad)

print(f"多层感知机使用的参数为 {linear_params} parameters.")

print("-------------------")

print(f"普通卷积层使用的参数为 {params} parameters.")

print("-------------------")

print(f"深度可分离卷积使用的参数为 {params_depthwise} parameters.")

再贴一次 Conv2d 的构造签名，方便对照 groups 的默认值和使用场景。

[PRE7]python

class Conv2d(_ConvNd):

    ...

    def __init__(

        self, in_channels: int, out_channels: int, kernel_size: _size_2_t, stride: _size_2_t = 1,

        padding: Union[str, _size_2_t] = 0, dilation: _size_2_t = 1, groups: int = 1, bias: bool = True,

        padding_mode: str = 'zeros',  # TODO: refine this type

        device=None,

        dtype=None

    ) -> None: