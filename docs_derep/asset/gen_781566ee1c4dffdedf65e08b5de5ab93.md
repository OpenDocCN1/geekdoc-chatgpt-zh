# PyTorch自定义Dataset从零实现指南

想自己掌控数据加载节奏，先搞懂Dataset的骨架再动手。PyTorch里只要继承 torch.utils.data.Dataset，并实现初始化、取值、长度三个方法，数据管道就能跑起来。下面用MNIST的npy文件一步步演示，从最简模板到带转换的完整用法。

先看最原始的接口长什么样，__getitem__负责按索引取样，__len__负责报总数，后续再填内容。

[PRE0]python

class Dataset():

    def __init__(self, transform=None): #注意transform参数会在6.1.2节进行介绍

        super(Dataset, self).__init__()

    def __getitem__(self, index):

        pass

    def __len__(self):

        pass
调用方式也很直观，方括号取单个样本，len函数统计总量。

[PRE1]python

data = CustomDataset[index]    #打印出index序号对应的数据

length = len(CustomDataset)    #打印出数据集总行数
把思路落地到MNIST。先在构造方法里把特征和标签的npy读进来存成实例属性。

[PRE2]python

def __init__(self, transform=None):    #注意transform参数会在6.1.2节进行介绍

    super(MNIST_Dataset, self).__init__()

    # 载入数据

    self.x_train = np.load("../dataset/mnist/x_train.npy")

    self.y_train_label = np.load("../dataset/mnist/y_train_label.npy")
取样逻辑就是按索引切片，返回图像和标签的二元组。

[PRE3]python

def __getitem__(self, item):

    image = (self.x_train[item])

    label = (self.y_train_label[item])

    return image,label
长度直接跟标签数组保持一致。

[PRE4]python

def __len__(self):

    return len(self.y_train_label)
整合起来就是一个可用的最小MNIST数据集类。

[PRE5]python

class MNIST_Dataset(torch.utils.data.Dataset):

    def __init__(self):

        super(MNIST_Dataset, self).__init__()

        # 载入数据

        self.x_train = np.load("../dataset/mnist/x_train.npy")

        self.y_train_label = np.load("../dataset/mnist/y_train_label.npy")

    def __getitem__(self, item):

        image = self.x_train[item]

        label = self.y_train_label[item]

        return image,label

    def __len__(self):

        return len(self.y_train_label)
实战中几乎都要做预处理，这里引入transform。自定义一个可调用类，把numpy转成torch张量。

[PRE6]python

class ToTensor:

    def __call__(self, inputs, targets):  #可调用对象

        return torch.tensor(inputs), torch.tensor(targets)
把transform作为参数传入构造方法，并在__getitem__里判断是否执行，保证原始数据也能直接用。

[PRE7]python

class MNIST_Dataset(torch.utils.data.Dataset):

    def __init__(self,transform = None):    #在定义时需要定义transform的参数

        super(MNIST_Dataset, self).__init__()

        # 载入数据

        self.x_train = np.load("../dataset/mnist/x_train.npy")

        self.y_train_label = np.load("../dataset/mnist/y_train_label.npy")

        self.transform = transform          #需要显式地提供transform类

    def __getitem__(self, index):

        image = (self.x_train[index])

        label = (self.y_train_label[index])

        #通过判定transform类的存在对其进行调用

        if self.transform:

            image,label = self.transform(image,label)

        return image,label

    def __len__(self):

        return len(self.y_train_label)
验证一下效果，不传transform时还是numpy类型，传入ToTensor后变成torch.Tensor。

[PRE8]python

import numpy as np

import torch

class ToTensor:

    def __call__(self, inputs, targets):    #可调用对象

        return torch.tensor(inputs), torch.tensor(targets)

class MNIST_Dataset(torch.utils.data.Dataset):

    def __init__(self,transform = None):    #在定义时需要定义transform的参数

        super(MNIST_Dataset, self).__init__()

        # 载入数据

        self.x_train = np.load("../dataset/mnist/x_train.npy")

        self.y_train_label = np.load("../dataset/mnist/y_train_label.npy")

        self.transform = transform          #需要显式地提供transform类

    def __getitem__(self, index):

        image = (self.x_train[index])

        label = (self.y_train_label[index])

        #通过判定transform类的存在对其进行调用

        if self.transform:

            image,label = self.transform(image,label)

        return image,label

    def __len__(self):

        return len(self.y_train_label)

mnist_dataset = MNIST_Dataset()

image,label = (mnist_dataset[1024])

print(type(image), type(label))

print("--------------------------------------------------")

mnist_dataset = MNIST_Dataset(transform=ToTensor())

image,label = (mnist_dataset[1024])

print(type(image), type(label))
很多模型需要固定形状，这里在ToTensor里先把图像拉平成28*28向量。

[PRE9]python

class ToTensor:

    def __call__(self, inputs, targets):  #可调用对象

        inputs = np.reshape(inputs,[28*28])

        return torch.tensor(inputs), torch.tensor(targets)
再打印一次就能看到shape变化。

[PRE10]python

mnist_dataset = MNIST_Dataset(transform=ToTensor())

image,label = (mnist_dataset[1024])

print(type(image), type(label))

print(image.shape)
下面几段代码仅作演示，提醒设备选择的问题。PyTorch默认会用CPU，若显卡可用可切到cuda，跑不通就改回cpu。

[PRE11]python

#注意下面这段代码无法正常使用，仅供演示

import numpy as np

import torch

#device = "cpu"    #PyTorch的特性，需要指定计算的硬件，如果没有GPU的存在，就使用CPU进行计算

device = "cuda"    #在这里默认使用GPU，如果出现运行问题，可以将其改成CPU模式
有时还需要对维度做更细的控制，比如加一个前置通道维。

[PRE12]python

class ToTensor:

    def __call__(self, inputs, targets):   #可调用对象

        inputs = np.reshape(inputs,[1,-1])

        targets = np.reshape(targets, [1, -1])

        return torch.tensor(inputs), torch.tensor(targets)
下面是带transform的完整数据集定义，同样仅为演示用途。

[PRE13]python

#注意下面这段代码无法正常使用，仅供演示

class MNIST_Dataset(torch.utils.data.Dataset):

    def __init__(self,transform = None):   #在定义时需要定义transform的参数

        super(MNIST_Dataset, self).__init__()

        # 载入数据

        self.x_train = np.load("../dataset/mnist/x_train.npy")

        self.y_train_label = np.load("../dataset/mnist/y_train_label.npy")

        self.transform = transform   #需要显式地提供transform类

    def __getitem__(self, index):

        image = (self.x_train[index])

        label = (self.y_train_label[index])

        #通过判定transform类的存在对其进行调用

        if self.transform:

            image,label = self.transform(image,label)

        return image,label

    def __len__(self):

        return len(self.y_train_label)
训练参数的设定片段，batch_size和epochs只是示例，实际运行需要完整pipeline。

[PRE14]python

#注意下面这段代码无法正常使用，仅供演示

mnist_dataset = MNIST_Dataset(transform=ToTensor())

import os

os.environ['CUDA_VISIBLE_DEVICES'] = '0' #指定GPU编码

import torch

import numpy as np

batch_size = 320    #设定每次训练的批次数

epochs = 1024    #设定训练次数
最后是模型开头，多层感知机的骨架从Flatten开始，后续层由业务决定。

[PRE15]python

#设定的多层感知机网络模型

class NeuralNetwork(torch.nn.Module):

    def __init__(self):

        super(NeuralNetwork, self).__init__()

        self.flatten = torch.nn.Flatten()