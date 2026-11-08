# 多卡训模型到 Nginx 上线的一条龙实操

先把训练闭环说清。每次迭代先把上轮残留的梯度清空，喂入一批数据让网络正向计算出输出，再用损失函数算出误差并回传求梯度，最后交给优化器做一次参数更新。轮次结束打印平均损失，是最常用的监控方式。

在早期的调试里可以看到损失在缓慢下降：
[PRE0]

Epoch 1, Loss: 251.4636

Epoch 2, Loss: 250.7968

Epoch 3, Loss: 250.1427

进入完整流程，先准备环境和数据。脚本会先探测可用 GPU， 对 CIFAR-10 做 ToTensor 和归一化， 用 DataLoader 以 128 为批量加载训练集和测试集， 并定义一个三层卷积加两层全连接的轻量 CNN。 当检测到多张显卡时用 DataParallel 包装模型， 把模型搬到 CUDA， 损失用 CrossEntropy， 优化器选 Adam 学习率 0.001。 训练函数里完成前向、反向、参数更新， 测试函数关闭梯度统计准确率。

[PRE1]

import torch

import torch.nn as nn

import torch.optim as optim

from torchvision import datasets, transforms

from torch.utils.data import DataLoader

# 检查可用的 GPU 设备数量

print(f"可用 GPU 数量: {torch.cuda.device_count()}")

# 数据预处理

transform=transforms.Compose([

    transforms.ToTensor(),

    transforms.Normalize((0.5,), (0.5,))

])

# 加载 CIFAR-10 数据集

train_dataset=datasets.CIFAR10(root='./data', train=True,

    transform=transform, download=True)

test_dataset=datasets.CIFAR10(root='./data', train=False,

    transform=transform, download=True)

train_loader=DataLoader(dataset=train_dataset, batch_size=128,

    shuffle=True, num_workers=4)

test_loader=DataLoader(dataset=test_dataset, batch_size=128,

    shuffle=False, num_workers=4)

print(f"训练集样本数: {len(train_dataset)}, 测试集样本数: {len(test_dataset)}")

# 定义模型

class SimpleCNN(nn.Module):

    def __init__(self):

        super(SimpleCNN, self).__init__()

        self.conv1=nn.Conv2d(3, 16, kernel_size=3, padding=1)

        self.conv2=nn.Conv2d(16, 32, kernel_size=3, padding=1)

        self.conv3=nn.Conv2d(32, 64, kernel_size=3, padding=1)

        self.pool=nn.MaxPool2d(2, 2)

        self.fc1=nn.Linear(64 * 4 * 4, 512)

        self.fc2=nn.Linear(512, 10)

        self.relu=nn.ReLU()

    def forward(self, x):

        x=self.pool(self.relu(self.conv1(x)))

        x=self.pool(self.relu(self.conv2(x)))

        x=self.pool(self.relu(self.conv3(x)))

        x=x.view(-1, 64 * 4 * 4)

        x=self.relu(self.fc1(x))

        x=self.fc2(x)

        return x

model=SimpleCNN()

# 使用 DataParallel 实现多 GPU 训练

if torch.cuda.device_count() > 1:

    print("使用 DataParallel 进行多 GPU 训练")

    model=nn.DataParallel(model)

# 将模型移动到 GPU

device=torch.device("cuda" if torch.cuda.is_available() else "cpu")

model.to(device)

# 定义损失函数和优化器

criterion=nn.CrossEntropyLoss()

optimizer=optim.Adam(model.parameters(), lr=0.001)

# 训练函数

def train_model(model, train_loader, criterion, optimizer, device, epochs=5):

    model.train()

    for epoch in range(epochs):

        running_loss=0.0

        for images, labels in train_loader:

            images, labels=images.to(device), labels.to(device)

            # 前向传播

            outputs=model(images)

            loss=criterion(outputs, labels)

            # 反向传播

            optimizer.zero_grad()

            loss.backward()

            optimizer.step()

            running_loss += loss.item()

        print(f"第{epoch+1}轮训练完成,损失: {running_loss / len(train_loader):.4f}")

# 测试函数

def test_model(model, test_loader, device):

    model.eval()

    correct = 0

    total = 0

    with torch.no_grad():

        for images, labels in test_loader:

            images, labels = images.to(device), labels.to(device)

            outputs = model(images)

            _, predicted = torch.max(outputs, 1)

            total += labels.size(0)

            correct += (predicted == labels).sum().item()

    print(f"模型测试完成,准确率: {100 * correct / total:.2f}%")

# 开始训练和测试

print("开始训练模型...")

train_model(model, train_loader, criterion, optimizer, device)

print("开始测试模型...")

test_model(model, test_loader, device)

实际跑起来，环境显示两块 GPU，训练集 50000 条，测试集 10000 条，成功启用 DataParallel。5 个 epoch 的损失稳步下降，最终在测试集上达到 82.34% 的准确率。

[PRE2]

可用 GPU 数量: 2

训练集样本数: 50000, 测试集样本数: 10000

使用 DataParallel 进行多 GPU 训练

开始训练模型...

第 1 轮训练完成,损失: 1.4523

第 2 轮训练完成,损失: 1.1234

第 3 轮训练完成,损失: 0.8921

第 4 轮训练完成,损失: 0.7654

第 5 轮训练完成,损失: 0.6543

开始测试模型...

模型测试完成,准确率: 82.34%

训练完成之后转向服务化。先在服务器上装 Nginx 作为前端网关，更新源并安装。

[PRE3]

sudo apt update

sudo apt install nginx -y

安装后立即启动并设为开机自启，检查状态。

[PRE4]

sudo systemctl start nginx

sudo systemctl enable nginx

sudo systemctl status nginx

核心配置里工作进程自动分配，连接数 1024，开启高效发送和 keepalive。 80 端口监听，根目录托管静态页， /api 统一反向代理到本地 5000 端口的 Flask 应用，并透传 Host 和真实 IP， 同时开启错误日志和访问日志。

[PRE5]

worker_processes auto; # 自动配置工作进程数

events {

    worker_connections 1024; # 每个工作进程的最大连接数

}

http {

    include /etc/nginx/mime.types; # 包含 MIME 类型配置

    default_type application/octet-stream; # 默认内容类型

    sendfile on; # 启用高效文件传输

    keepalive_timeout 65; # 长连接超时时间

    server {

        listen 80; # 监听端口

        server_name localhost; # 服务器名称

        location / {

            root /var/www/html; # 静态资源根目录

            index index.html; # 默认首页文件

        }

        location /api {

            proxy_pass http://127.0.0.1:5000; # 转发至后端服务器

            proxy_set_header Host $host;

            proxy_set_header X-Real-IP $remote_addr;

        }

        error_log /var/log/nginx/error.log; # 错误日志

        access_log /var/log/nginx/access.log; # 访问日志

    }

}

生成一个简单的静态首页用于验证 Nginx。

[PRE6]

sudo mkdir -p /var/www/html

echo "<h1>欢迎访问 Nginx 静态文件服务</h1>" | sudo tee /var/www/html/index.html

后端用 Flask 提供 API，先安装依赖。

[PRE7]

pip install flask

最简应用提供 /api/hello 接口，返回 JSON 问候。

[PRE8]

from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/api/hello', methods=['GET'])

def hello():

    return jsonify(message="Hello from Flask!")

if __name__ == '__main__':

    app.run(host='127.0.0.1', port=5000)

启动服务。

[PRE9]

python app.py

为了清晰展示反代段，可以单独摘出 location 配置。

[PRE10]

location /api {

    proxy_pass http://127.0.0.1:5000;

    proxy_set_header Host $host;

    proxy_set_header X-Real-IP $remote_addr;

}

保存后重载 Nginx 使配置生效。

[PRE11]

sudo systemctl restart nginx

浏览器访问 /api/hello 能拿到预期返回。

[PRE12]

{

    "message": "Hello from Flask!"

}

最后按需自定义访问日志格式，记录客户端 IP、请求、状态等关键字段。

[PRE13]

log_format custom '$remote_addr - $remote_user [$time_local] "$request" '

                  '$status $body_bytes_sent "$http_referer" "$http_user_agent"';

access_log /var/log/nginx/custom_access.log custom;

[PRE14]