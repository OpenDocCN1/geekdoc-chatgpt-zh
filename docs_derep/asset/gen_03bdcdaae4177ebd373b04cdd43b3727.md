CNN+LSTM 天气预测实操

想用深度学习做序列气象预测，常见的思路是先用卷积把局部时序模式压成特征，再交给循环网络记住长期依赖。下面这个最小可跑的 demo 用了两层一维卷积做特征抽取，之后接一个 LSTM，最后用全连接把最后一个时间步映射到单值输出，目标是给 30 步历史的 10 维气象特征预测下一步温度。

第一版搭建了名字为 CNN_RNN_Model 的结构，卷积通道从输入维度映射到 64 再到 128，核宽 3 且填充保持长度，之后接最大池化下采样两倍。卷积后的张量先转成卷积可接受的通道在前格式，提取完再转回批次-时间-特征格式喂给 LSTM，隐藏维设为 128。预测只取序列末尾的隐状态，配合均方误差和 Adam 0.001 学习率训练。

数据用纯随机模拟的 WeatherDataset，样本 1 万条，每条 30 步 10 维输入，标签同长度单通道。训练时 batch 64，跑 10 轮，设备自动选 CUDA，结束后把权重存成 cnn_rnn_weather_model.pth。训练过程只对比模型输出与标签最后一个时间步。

第一版的训练曲线如下：
[PRE0]

Epoch [1/10], Loss: 1.3784

Epoch [2/10], Loss: 1.2821

Epoch [3/10], Loss: 1.1534

Epoch [4/10], Loss: 1.0312

Epoch [5/10], Loss: 0.9247

Epoch [6/10], Loss: 0.8276

Epoch [7/10], Loss: 0.7409

Epoch [8/10], Loss: 0.6674

Epoch [9/10], Loss: 0.6003

Epoch [10/10], Loss: 0.5431

损失单调下降，说明卷积+循环的组合在模拟数据上能稳定收敛。

为了复现和对比，这里给出标准化的 CNN_LSTM 版本完整实现，超参与上面一致：输入维 10，时间步 30，LSTM 隐藏 128，输出 1，batch 64，10 个 epoch，同样使用 MSE 损失和 Adam 0.001，模型文件命名为 cnn_lstm_weather_model.pth。
[PRE1]python

import torch

import torch.nn as nn

import torch.optim as optim

from torch.utils.data import Dataset, DataLoader

import numpy as np

# 1. 数据集定义（模拟的气象数据集）

class WeatherDataset(Dataset):

    def __init__(self, size=10000, input_dim=10, time_steps=30):

        self.size=size

        self.input_dim=input_dim

        self.time_steps=time_steps

        self.data=np.random.randn(self.size, self.time_steps,

                                  self.input_dim).astype(np.float32)

        self.labels=np.random.randn(self.size,

                                    self.time_steps, 1).astype(np.float32)  # 预测温度变化

    def __len__(self):

        return self.size

    def __getitem__(self, idx):

        return torch.tensor(self.data[idx]), torch.tensor(self.labels[idx])

# 2. 定义基于 CNN 和 LSTM 的气象模拟模型

class CNN_LSTM_Model(nn.Module):

    def __init__(self, input_dim, time_steps, hidden_dim, output_dim):

        super(CNN_LSTM_Model, self).__init__()

        # CNN 部分

        self.conv1=nn.Conv1d(input_dim, 64, kernel_size=3, padding=1)

        self.conv2=nn.Conv1d(64, 128, kernel_size=3, padding=1)

        self.pool=nn.MaxPool1d(kernel_size=2, stride=2)

        # LSTM 部分

        self.lstm=nn.LSTM(128, hidden_dim, batch_first=True)

        # 输出层

        self.fc=nn.Linear(hidden_dim, output_dim)

    def forward(self, x):

        # CNN 特征提取

        # 调整输入形状，以符合 CNN 的输入要求 [batch_size, input_dim, time_steps]

        x=x.permute(0, 2, 1)

        x=self.pool(torch.relu(self.conv1(x)))

        x=self.pool(torch.relu(self.conv2(x)))

        # LSTM 部分

        # 调整为符合 LSTM 要求的输入形状 [batch_size, time_steps, features]

        x=x.permute(0, 2, 1)

        lstm_out, _=self.lstm(x)

        # 输出层

        x=self.fc(lstm_out[:, -1, :])    # 只使用最后一个时间步的输出

        return x

# 3. 训练函数

def train(model, dataloader, optimizer, criterion, device):

    model.train()

    running_loss=0.0

    for inputs, labels in dataloader:

        inputs, labels=inputs.to(device), labels.to(device)

        optimizer.zero_grad()

        outputs=model(inputs)            # 前向传播

        # 计算损失

        loss=criterion(outputs, labels[:, -1, :])  # 使用最后一个时间步的标签

        loss.backward()

        optimizer.step()                # 优化

        running_loss += loss.item()

    return running_loss/len(dataloader)

# 4. 初始化模型与训练

def main():

    # 超参数设置

    input_dim=10                # 输入特征的维度

    time_steps=30               # 时间步长

    hidden_dim=128              # LSTM 隐藏层维度

    output_dim=1                # 输出维度，预测未来的气象值（如温度）

    batch_size=64               # 批次大小

    epochs=10                   # 训练轮数

    # 数据集和数据加载器

    dataset=WeatherDataset(size=10000, input_dim=input_dim,

                        time_steps=time_steps)

    dataloader=DataLoader(dataset, batch_size=batch_size, shuffle=True)

    # 定义模型、损失函数和优化器

    device=torch.device('cuda' if torch.cuda.is_available() else 'cpu')

    model=CNN_LSTM_Model(input_dim, time_steps, hidden_dim,

                     output_dim).to(device)

    criterion=nn.MSELoss()              # 使用均方误差损失函数

    optimizer=optim.Adam(model.parameters(), lr=0.001)

    # 训练模型

    for epoch in range(epochs):

        loss=train(model, dataloader, optimizer, criterion, device)

        print(f"Epoch [{epoch+1}/{epochs}], Loss: {loss:.4f}")

    # 保存模型

    torch.save(model.state_dict(), "cnn_lstm_weather_model.pth")

if __name__ == "__main__":

    main()

在相同随机模拟数据上跑完 10 轮的结果：
[PRE2]

Epoch [1/10], Loss: 1.2734

Epoch [2/10], Loss: 1.2104

Epoch [3/10], Loss: 1.1432

Epoch [4/10], Loss: 1.0909

Epoch [5/10], Loss: 1.0383

Epoch [6/10], Loss: 0.9867

Epoch [7/10], Loss: 0.9338

Epoch [8/10], Loss: 0.8865

Epoch [9/10], Loss: 0.8442

Epoch [10/10], Loss: 0.8030

两组实验都验证了思路的可行性：卷积负责提取局部时序纹理，LSTM 负责记忆长程依赖，配合最后一个时刻回归可以得到稳定下降的训练损失。把模拟数据换成真实气象序列后，只需替换数据加载部分，整体骨架就能直接用于实战预测。