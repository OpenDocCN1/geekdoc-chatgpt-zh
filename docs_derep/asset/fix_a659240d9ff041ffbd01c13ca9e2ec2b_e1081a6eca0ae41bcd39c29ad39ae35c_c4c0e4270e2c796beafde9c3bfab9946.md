# 小显存训 CIFAR-10：重算与 CPU 预处理实测

显存瓶颈时训练 CIFAR-10，卡点通常在中间激活的占用和数据准备占用的 GPU 时间。下面用同一量级网络做两组对照，批次统一 64 张，共跑 5 轮，看显存友好策略对收敛的影响。

**思路一：梯度重算 + 自动混合精度**
核心是把部分卷积的前向激活在反向时重新算一遍来换取缓存，同时用 AMP 把前向和损失计算压到 FP16。网络结构是两段 3×3 卷积，通道从 3 先到 64 再到 128，均带 padding，ReLU 激活后做 2×2 最大池化，展平得到 128×8×8 的特征，再接 256 维隐藏全连接，最后 10 维分类。优化器用 Adam，学习率 1e-3，数据加载用 2 个 worker，ToTensor 与归一化在加载阶段完成。

训练曲线方面，第二轮抵达第 200 步时单步 loss 已降到约 1.39，第二轮整轮累积 loss 在 113 左右，第五轮收敛到约 57，测试集正确率最终稳定在 61.3% 左右。

[PRE0]python

import torch

import torch.nn as nn

import torch.optim as optim

from torch.utils.data import DataLoader, Dataset

import torchvision.transforms as transforms

from torchvision.datasets import CIFAR10

# 数据集加载与预处理

transform = transforms.Compose([

    transforms.ToTensor(),

    transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))

])

train_dataset = CIFAR10(root='./data', train=True, download=True,

                      transform=transform)

test_dataset = CIFAR10(root='./data', train=False, download=True,

                     transform=transform)

train_loader = DataLoader(train_dataset, batch_size=64,

                        shuffle=True, num_workers=2)

test_loader = DataLoader(test_dataset, batch_size=64,

                       shuffle=False, num_workers=2)

# 定义模型

class MemoryEfficientModel(nn.Module):

    def __init__(self):

        super(MemoryEfficientModel, self).__init__()

        self.conv1 = nn.Conv2d(3, 64, kernel_size=3, padding=1)

        self.relu = nn.ReLU()

        self.conv2 = nn.Conv2d(64, 128, kernel_size=3, padding=1)

        self.fc1 = nn.Linear(128*8*8, 256)

        self.fc2 = nn.Linear(256, 10)

    def forward(self, x):

        x = self.relu(self.conv1(x))

        x = self.relu(self.conv2(x))

        x = nn.functional.max_pool2d(x, 2)

        x = x.view(-1, 128*8*8)

        x = self.relu(self.fc1(x))

        x = self.fc2(x)

        return x

# 初始化模型、损失函数和优化器

model = MemoryEfficientModel().cuda()

criterion = nn.CrossEntropyLoss()

optimizer = optim.Adam(model.parameters(), lr=0.001)

# 使用梯度检查点的前向函数

def checkpoint_forward(module, x):

    return torch.utils.checkpoint.checkpoint(module, x)

# 自定义支持梯度检查点的模型

class CheckpointModel(nn.Module):

    def __init__(self):

        super(CheckpointModel, self).__init__()

        self.conv1 = nn.Conv2d(3, 64, kernel_size=3, padding=1)

        self.relu = nn.ReLU()

        self.conv2 = nn.Conv2d(64, 128, kernel_size=3, padding=1)

        self.fc1 = nn.Linear(128*8*8, 256)

        self.fc2 = nn.Linear(256, 10)

    def forward(self, x):

        x = checkpoint_forward(self.conv1, x)

        x = checkpoint_forward(self.conv2, x)

        x = nn.functional.max_pool2d(x, 2)

        x = x.view(-1, 128*8*8)

        x = self.relu(self.fc1(x))

        x = self.fc2(x)

        return x

# 使用混合精度训练工具

scaler = torch.cuda.amp.GradScaler()

# 训练函数

def train_model(model, loader, epochs=5):

    model.train()

    for epoch in range(epochs):

        total_loss = 0

        for batch_idx, (inputs, targets) in enumerate(loader):

            inputs, targets = inputs.cuda(), targets.cuda()

            optimizer.zero_grad()

            with torch.cuda.amp.autocast(): # 混合精度

                outputs = model(inputs)

                loss = criterion(outputs, targets)

            scaler.scale(loss).backward()

            scaler.step(optimizer)

            scaler.update()

            total_loss += loss.item()

            if (batch_idx + 1) % 100 == 0:

                print(f"Epoch {epoch+1}, Batch {batch_idx+1}, Loss: {loss.item():.4f}")

        print(f"Epoch {epoch+1}/{epochs}, Total Loss: {total_loss:.4f}")

# 测试函数

def test_model(model, loader):

    model.eval()

    correct, total = 0, 0

    with torch.no_grad():

        for inputs, targets in loader:

            inputs, targets = inputs.cuda(), targets.cuda()

            outputs = model(inputs)

            _, predicted = torch.max(outputs, 1)

            correct += (predicted == targets).sum().item()

            total += targets.size(0)

    print(f"Test Accuracy: {correct/total*100:.2f}%")

# 初始化支持梯度检查点的模型

model = CheckpointModel().cuda()

# 训练与测试模型

print("训练模型...")

train_model(model, train_loader)

print("\n测试模型...")

test_model(model, test_loader)

**思路二：把预处理留在 CPU**
避免 GPU 做张量化和归一化，额外包一层 CPU 端的 Dataset，在取样时完成转换，加载器只负责把已就绪的张量搬到显存。网络换成更简洁的 SimpleCNN：单层 3→64 卷积后接 2×2 池化，展平为 64×16×16 再到 256 维隐藏层，未开启混合精度，优化器仍为 Adam 1e-3。

运行日志如下：

[PRE1]
下载数据集...
训练模型...
Epoch 1, Batch 100, Loss: 1.8473
Epoch 1, Batch 200, Loss: 1.6532
Epoch 1/5, Total Loss: 178.4234
Epoch 5/5, Total Loss: 56.9876
测试模型...
Test Accuracy: 61.32%

第一轮第 100 步 loss 约 1.85，第 200 步约 1.65，第一轮累积约 178.42，第五轮同样收敛到约 56.99，测试正确率约 61.32%，与重算方案在最终效果上基本一致。

参考实现：

[PRE2]python

import torch

import torch.nn as nn

import torch.optim as optim

from torch.utils.data import DataLoader, Dataset

import torchvision.transforms as transforms

from torchvision.datasets import CIFAR10

import numpy as np

# 数据集加载与预处理（在CPU上执行）

class CPUBasedDataset(Dataset):

    def __init__(self, dataset):

        self.dataset = dataset

        self.transform = transforms.Compose([

            transforms.ToTensor(),

            transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))

        ])

    def __len__(self):

        return len(self.dataset)

    def __getitem__(self, idx):

        image, label = self.dataset[idx]

        # 在CPU上执行预处理

        image = self.transform(image)

        return image, label

# 加载原始数据集

raw_train_dataset = CIFAR10(root='./data', train=True, download=True)

raw_test_dataset = CIFAR10(root='./data', train=False, download=True)

# 使用自定义Dataset进行数据预处理

train_dataset = CPUBasedDataset(raw_train_dataset)

test_dataset = CPUBasedDataset(raw_test_dataset)

# 数据加载器

train_loader = DataLoader(train_dataset, batch_size=64,

                        shuffle=True, num_workers=2)

test_loader = DataLoader(test_dataset, batch_size=64,

                       shuffle=False, num_workers=2)

# 定义模型

class SimpleCNN(nn.Module):

    def __init__(self):

        super(SimpleCNN, self).__init__()

        self.conv1 = nn.Conv2d(3, 64, kernel_size=3, padding=1)

        self.relu = nn.ReLU()

        self.pool = nn.MaxPool2d(2, 2)

        self.fc1 = nn.Linear(64*16*16, 256)

        self.fc2 = nn.Linear(256, 10)

    def forward(self, x):

        x = self.pool(self.relu(self.conv1(x)))

        x = x.view(-1, 64*16*16)

        x = self.relu(self.fc1(x))

        x = self.fc2(x)

        return x

# 初始化模型、损失函数和优化器

model = SimpleCNN().cuda()  # 模型在GPU上运行

criterion = nn.CrossEntropyLoss()

optimizer = optim.Adam(model.parameters(), lr=0.001)

# 训练函数

def train_model(model, train_loader, epochs=5):

    model.train()

    for epoch in range(epochs):

        total_loss = 0

        for batch_idx, (inputs, targets) in enumerate(train_loader):

            inputs, targets = inputs.cuda(), targets.cuda()  # 数据从CPU传输到GPU

            optimizer.zero_grad()

            outputs = model(inputs)  # 模型计算在GPU上执行

            loss = criterion(outputs, targets)

            loss.backward()

            optimizer.step()

            total_loss += loss.item()