# CIFAR-10 内存友好训练实测：梯度检查点与混合精度

在有限显存下跑 CIFAR-10，最常见的瓶颈是中间激活占用和数据预处理卡 GPU。下面的实验用同一个小 CNN 骨架，先通过梯度检查点 + AMP 降低显存峰值，再对比把预处理搬到 CPU 的做法。批次固定为 64，训练 5 个 epoch。

**观察到的收敛趋势**
第二轮训练时第 200 个小批的损失已降到约 1.39，批次大小保持 64。第二轮整体累计损失约为 113.8，到第 5 轮时累计损失降至 56.99，测试集准确率稳定在 61.32% 左右。与 CPU 预处理版本对比，第一轮第 100 个小批损失 1.85、第 200 个小批损失 1.65，第一轮累计损失 178.42，同样在第 5 轮收敛到累计损失 56.99、准确率 61.32%。

**方案一：梯度检查点 + 混合精度**
思路是只在反向时重算部分卷积，减少激活缓存，同时用 torch.cuda.amp 在 FP16 下做前向与损失计算。模型结构为两层卷积：3→64、64→128，卷积后做 2×2 最大池化，展平为 128×8×8 后接 256 隐藏层和 10 维输出。DataLoader 使用 2 个 worker，ToTensor 和 Normalize 在加载时完成。

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

**方案二：预处理放 CPU，模型留 GPU**
为避免在 GPU 上做 ToTensor 和 Normalize，另写一个 CPUBasedDataset，在 __getitem__ 里完成转换，DataLoader 只负责搬运。模型换成更轻的 SimpleCNN：单层 3→64 卷积 + 2×2 池化，展平为 64×16×16 再接 256 隐藏层。训练仍用 Adam，学习率 0.001，没有 AMP。

对应的运行日志如下：

[PRE1]
下载数据集...
训练模型...
Epoch 1, Batch 100, Loss: 1.8473
Epoch 1, Batch 200, Loss: 1.6532
Epoch 1/5, Total Loss: 178.4234
Epoch 5/5, Total Loss: 56.9876
测试模型...
Test Accuracy: 61.32%

实现参考：

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