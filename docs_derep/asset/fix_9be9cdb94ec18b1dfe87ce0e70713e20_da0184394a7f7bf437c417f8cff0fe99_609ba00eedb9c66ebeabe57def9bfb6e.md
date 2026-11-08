# PyTorch 多卡并行 4 种写法对比

模型和数据一起变大后，单卡训练很容易成为瓶颈。PyTorch 给出了从手动同步到即插即用的几条加速路线，既能在一台机器上利用多张显卡，也能扩展到多台机器协同。下面用同一份极简网络统一演示：1024 维输入先过 512 维隐藏层，再过 256 维隐藏层，最后映射到 10 类。批量大小固定为 64，目标函数选用交叉熵，Adam 学习率设为 1e-3。网络结构与超参全部一致，只更换并行写法。

### 手工同步梯度：Gloo 自行平均

最底层的思路是自己搭建进程组，用 Gloo 作为通信后端。每个进程各自完成一次前向和反向，随后手动把所有参数的梯度做一次 all_reduce 求和，再除以参与进程数得到平均值。以双进程为例，设备选择会根据是否可用 CUDA 决定是 cuda:{序号} 还是 cpu。一次迭代结束后，两路的损失会打印成相同数值。

[PRE0]
Rank 0, 分布式训练的损失: 2.2341
Rank 1, 分布式训练的损失: 2.2341

### DataParallel：单机多卡的速成入口

想把网络快速分散到多张卡上跑，DataParallel 是最省心的选择。先建好网络，再用 DataParallel 包一层，输入会在主卡上切分到各卡计算，梯度最终聚回主卡。设备策略遵循有 CUDA 就用 CUDA，没有就退回 CPU。下面是可直接运行的片段：

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

### 单机 DDP 加 NCCL：多卡真并行

DataParallel 受限于主卡调度，DistributedDataParallel 让每张卡都有独立副本并行工作，通信走 NCCL。先把 MASTER_ADDR 设成 localhost、MASTER_PORT 设成 12355 再初始化进程组。每个进程绑定自己的 GPU，网络经 DDP 包装后即可训练，结束时销毁进程组。进程数取当前可见显卡数量。

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

### 跨机 DDP：指定可达主节点

算力跨到多台机器时，只要把主控地址和端口换成网络可达的节点信息，并在每个进程内部用进程号取模映射到本地显卡即可。初始化函数接受外部传入的主控信息，训练函数里设备写成 cuda:{进程号 % 本机卡数}，DDP 设备列表保持一致。总进程数可以跨节点累加，例如 4 个进程分布在两台机器各两卡。

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