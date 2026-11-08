双卡并行跑起来：用随机数据验证 PyTorch 多 GPU 流程

想快速确认多卡环境是否可用，最省事的做法就是搭一个最小可运行的例子：造一批完全随机的输入和标签，堆一个两层全连接网络，开 DataParallel 跑几轮，看看日志是否正常。下面的代码就是这套验证思路的完整实现。

先是数据和模型的基础搭建，随后是 DataLoader、设备选择以及 DataParallel 的开关，最后进入标准的训练循环并打印损失、准确率与少量样本的预测对照。

[PRE0]python

import torch

import torch.nn as nn

import torch.optim as optim

from torch.utils.data import DataLoader, Dataset

import numpy as np

import time

# 1. 数据集定义

class RandomDataset(Dataset):

    def __init__(self, size=1000, input_dim=10):

        self.size = size

        self.input_dim = input_dim

        self.data = np.random.randn(self.size,

                                    self.input_dim).astype(np.float32)

        self.labels = np.random.randint(0, 2, size=(self.size, 1)).astype(

            np.float32)

    def __len__(self):

        return self.size

    def __getitem__(self, idx):

        return torch.tensor(self.data[idx]), torch.tensor(self.labels[idx])

# 2. 定义一个简单的全连接神经网络模型

class SimpleModel(nn.Module):

    def __init__(self, input_dim, hidden_dim, output_dim):

        super(SimpleModel, self).__init__()

        self.fc1 = nn.Linear(input_dim, hidden_dim)

        self.relu = nn.ReLU()

        self.fc2 = nn.Linear(hidden_dim, output_dim)

    def forward(self, x):

        x = self.fc1(x)

        x = self.relu(x)

        x = self.fc2(x)

        return x

# 3. 数据加载与分配

batch_size = 64

dataset = RandomDataset(size=10000, input_dim=10)

train_loader = DataLoader(dataset, batch_size=batch_size, shuffle=True)

# 4. 设备配置与模型初始化

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

model = SimpleModel(input_dim=10, hidden_dim=50, output_dim=1).to(device)

# 5. 使用 nn.DataParallel 模块进行多 GPU 训练

if torch.cuda.device_count() > 1:

    print(f"使用 {torch.cuda.device_count()} 个 GPU 进行训练")

    model = nn.DataParallel(model)  # 将模型并行化

# 6. 定义优化器和损失函数

optimizer = optim.Adam(model.parameters(), lr=0.001)

criterion = nn.BCEWithLogitsLoss()

# 7. 训练过程

epochs = 5

start_time = time.time()

for epoch in range(epochs):

    model.train()

    running_loss = 0.0

    correct = 0

    total = 0

    for i, (inputs, labels) in enumerate(train_loader):

        inputs, labels = inputs.to(device), labels.to(device)

        optimizer.zero_grad()                # 清零梯度

        outputs = model(inputs)                # 前向传播

        loss = criterion(outputs, labels)      # 计算损失

        # 反向传播与优化

        loss.backward()

        optimizer.step()

        running_loss += loss.item()          # 统计损失

        # 计算准确率

        preds = torch.sigmoid(outputs) > 0.5

        correct += (preds == labels).sum().item()

        total += labels.size(0)

    epoch_loss = running_loss / len(train_loader)

    epoch_accuracy = correct / total * 100

训练结束后输出统计信息并抽取一批样本做可视化对照，看模型在随机数据上的表现。

[PRE1]python

print(f"Epoch [{epoch+1}/{epochs}], Loss: {epoch_loss:.4f}, Accuracy: {epoch_accuracy:.2f}%")

end_time = time.time()

print(f"训练完成，总耗时: {end_time-start_time:.2f}秒")

# 8. 输出部分预测结果

sample_inputs, sample_labels = next(iter(train_loader))

sample_inputs, sample_labels = sample_inputs.to(device), sample_labels.to(device)

with torch.no_grad():

    sample_outputs = model(sample_inputs)

    predicted = torch.sigmoid(sample_outputs) > 0.5

print("部分预测结果: ")

for i in range(10):

    print(f"输入: {sample_inputs[i].cpu().numpy()}, 预测: {predicted[i].cpu().numpy()}, 实际: {sample_labels[i].cpu().numpy()}")

在 2 张显卡的机器上实际运行，日志如下。可以看到 DataParallel 成功生效，损失稳定在 0.693 附近，准确率始终徘徊在 50% 上下，完全符合随机标签没有任何可学习信号的预期。

[PRE2]

使用 2 个 GPU 进行训练

Epoch [1/5], Loss: 0.6934, Accuracy: 50.14%

Epoch [2/5], Loss: 0.6933, Accuracy: 50.20%

Epoch [3/5], Loss: 0.6932, Accuracy: 50.20%

Epoch [4/5], Loss: 0.6931, Accuracy: 50.24%

Epoch [5/5], Loss: 0.6930, Accuracy: 50.23%

训练完成，总耗时：10.21 秒

部分预测结果：

输入：[ 0.32398205 -0.10485428  1.18855265  0.07087453 -0.69790612 -0.40489091

 0.75874152  0.04468145 -0.34187609  0.57041267]，预测：[False]，实际：[0.]

输入：[ 0.15803842 -0.76593952 -0.49743831  0.17116888 -0.58077869 -0.15803842

 0.26097333  0.48809864  1.39873464  0.21834783]，预测：[True]，实际：[0.]

输入：[-0.56852653  0.47676167  0.26249689  -0.4475019   1.56946765  0.36561229

-1.16038148 -0.11462617  0.89734556 -0.23038817]，预测：[True]，实际：[1.]

...

同样的验证思路也可以换成更深的网络。下面的片段把数据集规模固定在 10000 条，并开始定义一个多隐藏层的 LargeModel，后续可以沿用同样的 DataParallel 与训练循环进行对比。

[PRE3]python

import torch

import torch.nn as nn

import torch.optim as optim

from torch.utils.data import DataLoader, Dataset

import numpy as np

import time

# 1. 数据集定义

class RandomDataset(Dataset):

    def __init__(self, size=10000, input_dim=10):

        self.size = size

        self.input_dim = input_dim

        self.data = np.random.randn(self.size, self.input_dim).astype(np.float32)

        self.labels = np.random.randint(0, 2, size=(self.size, 1)).astype(np.float32)

    def __len__(self):

        return self.size

    def __getitem__(self, idx):

        return torch.tensor(self.data[idx]), torch.tensor(self.labels[idx])

# 2. 定义一个大规模神经网络模型（多个隐藏层）

class LargeModel(nn.Module):