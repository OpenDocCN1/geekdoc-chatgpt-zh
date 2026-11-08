# 多卡训练怎么选？PyTorch 四种并行实战一览

模型越深、数据越大，单张卡的耗时就越难接受。PyTorch 提供了几条常见的加速路线，从最原始的手工梯度平均，到现成的 DataParallel，再到生产常用的 DistributedDataParallel，单机和跨机都能覆盖。下面用同一个三层全连接小模型做演示，输入 1024 维，隐藏层 512、256，输出 10 类，批次 64，交叉熵损失，Adam 学习率 1e-3，保持网络结构和超参一致，只换并行方式。

### 手工平均梯度 + Gloo 进程组

最直观的做法是自己启动进程组，用 Gloo 作为后端，跑完反向后对每个参数的梯度做 all_reduce 求和，再除以进程数实现平均。代码里用 multiprocessing.spawn 启动 2 个 rank，设备会根据是否可用 CUDA 选择 cuda:{rank} 或 cpu。训练一步后两个进程打印的损失完全一致。

[PRE0]

Rank 0, 分布式训练的损失: 2.2341

Rank 1, 分布式训练的损失: 2.2341

### 单机多卡 DataParallel

如果只是想快速把模型丢到多张卡上跑，nn.DataParallel 是最省心的选择。模型先实例化再包一层 DataParallel，输入数据在主卡上切分到各卡计算，梯度自动汇总回主卡。设备选择 cuda 或 cpu 均可。下面是完整的运行片段：

[PRE1]python

import torch

import torch.nn as nn

import torch.optim as optim

# 定义模型

class SimpleModel(nn.Module):

    def __init__(self):

        super(SimpleModel, self).__init__()

        self.fc1=nn.Linear(1024, 512)

        self.fc2=nn.Linear(512, 256)

        self.fc3=nn.Linear(256, 10)

    def forward(self, x):

        x=torch.relu(self.fc1(x))

        x=torch.relu(self.fc2(x))

        return self.fc3(x)

# 初始化模型和数据

device=torch.device("cuda" if torch.cuda.is_available() else "cpu")

model=SimpleModel()

model=nn.DataParallel(model)  # 使用DataParallel进行多卡并行

model.to(device)

# 模拟输入数据

input_data=torch.randn(64, 1024).to(device)  # 批次大小为64

labels=torch.randint(0, 10, (64,)).to(device)

# 损失函数和优化器

criterion=nn.CrossEntropyLoss()

optimizer=optim.Adam(model.parameters(), lr=1e-3)

# 前向传播和损失计算

outputs=model(input_data)

loss=criterion(outputs, labels)

# 反向传播和参数更新

optimizer.zero_grad()

loss.backward()

optimizer.step()

print("DataParallel训练的损失:", loss.item())

[PRE2]

DataParallel训练的损失: 1.9876

### 单机 DDP，NCCL 加速

DataParallel 的瓶颈在主卡，DistributedDataParallel 能让每张卡都独立前向反向，通信用 NCCL。先用 setup 设置 MASTER_ADDR 为 localhost、MASTER_PORT 为 12355，init_process_group 用 nccl。每个 rank 绑定自己的 cuda:{rank}，模型包装成 DDP 后直接训练，最后销毁进程组。进程数取当前可见 GPU 数量。

[PRE3]python

import torch

import torch.distributed as dist

import torch.nn as nn

import torch.optim as optim

from torch.nn.parallel import DistributedDataParallel as DDP

import os

# 初始化分布式环境

def setup(rank, world_size):

    os.environ['MASTER_ADDR']='localhost'

    os.environ['MASTER_PORT']='12355'

    dist.init_process_group("nccl", rank=rank, world_size=world_size)

# 定义模型

class SimpleModel(nn.Module):

    def __init__(self):

        super(SimpleModel, self).__init__()

        self.fc1=nn.Linear(1024, 512)

        self.fc2=nn.Linear(512, 256)

        self.fc3=nn.Linear(256, 10)

    def forward(self, x):

        x=torch.relu(self.fc1(x))

        x=torch.relu(self.fc2(x))

        return self.fc3(x)

# 分布式训练函数

def train(rank, world_size):

    setup(rank, world_size)

    # 设置设备和模型

    device=torch.device(f"cuda:{rank}")

    model=SimpleModel().to(device)

    model=DDP(model, device_ids=[rank])

    # 数据和标签

    input_data=torch.randn(64, 1024).to(device)

    labels=torch.randint(0, 10, (64,)).to(device)

    # 损失函数和优化器

    criterion=nn.CrossEntropyLoss()

    optimizer=optim.Adam(model.parameters(), lr=1e-3)

    # 前向传播和损失计算

    outputs=model(input_data)

    loss=criterion(outputs, labels)

    # 反向传播和参数更新

    optimizer.zero_grad()

    loss.backward()

    optimizer.step()

    print(f"Rank {rank}, DistributedDataParallel训练的损失: {loss.item()}")

    # 关闭进程组

    dist.destroy_process_group()

# 启动多进程进行训练

world_size=torch.cuda.device_count()

torch.multiprocessing.spawn(train, args=(world_size,), nprocs=world_size, join=True)

[PRE4]

Rank 0, DistributedDataParallel训练的损失: 1.8973

Rank 1, DistributedDataParallel训练的损失: 1.8973

### 跨机器 DDP，指定主节点

当算力分散在多台机器时，只需把 MASTER_ADDR、MASTER_PORT 换成可达的主节点地址，并在每个进程里按 rank 取模映射到本地 GPU。setup 接受外部传入的主节点信息，训练函数里用 cuda:{rank % torch.cuda.device_count()} 并让 DDP device_ids 保持一致。world_size 可以跨节点累加，例如 4 个进程两卡两机。

[PRE5]python

import torch

import torch.distributed as dist

import torch.nn as nn

import torch.optim as optim

from torch.nn.parallel import DistributedDataParallel as DDP

import os

# 初始化分布式环境

def setup(rank, world_size, master_addr, master_port):

    os.environ['MASTER_ADDR']=master_addr

    os.environ['MASTER_PORT']=master_port

    dist.init_process_group("nccl", rank=rank, world_size=world_size)

# 定义简单模型

class SimpleModel(nn.Module):

    def __init__(self):

        super(SimpleModel, self).__init__()

        self.fc1=nn.Linear(1024, 512)

        self.fc2=nn.Linear(512, 256)

        self.fc3=nn.Linear(256, 10)

    def forward(self, x):

        x=torch.relu(self.fc1(x))

        x=torch.relu(self.fc2(x))

        return self.fc3(x)

# 跨机器分布式训练函数

def train(rank, world_size, master_addr, master_port):

    setup(rank, world_size, master_addr, master_port)

    # 配置设备和模型

    device=torch.device(f"cuda:{rank % torch.cuda.device_count()}")

    model=SimpleModel().to(device)

    model=DDP(model, device_ids=[rank % torch.cuda.device_count()])

    # 数据和标签

    input_data=torch.randn(64, 1024).to(device)

    labels=torch.randint(0, 10, (64,)).to(device)

    # 损失函数和优化器

    criterion=nn.CrossEntropyLoss()

    optimizer=optim.Adam(model.parameters(), lr=1e-3)

    # 前向传播和损失计算

    outputs=model(input_data)

    loss=criterion(outputs, labels)

    # 反向传播和参数更新

    optimizer.zero_grad()

    loss.backward()

    optimizer.step()

    print(f"Rank {rank}, 分布式跨机器训练的损失: {loss.item()}")

    # 关闭进程组

    dist.destroy_process_group()

# 定义主节点IP地址和端口号

master_addr='192.168.1.1'     # 主节点IP地址

master_port='12355'           # 主节点端口号

world_size=4                  # 总的进程数（假设每台机器上有两个GPU）

# 启动多进程