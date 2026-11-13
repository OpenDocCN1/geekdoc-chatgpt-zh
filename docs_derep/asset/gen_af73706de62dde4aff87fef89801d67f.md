# 实战：基于 ResNet34 的 Caltech-101 图像分类全流程解析

[PRE0]

卷积操作的核心实现依赖于 `nn.Conv2d()` 函数，其参数定义详情如下。

[PRE1]

接下来，我们正式开启基于 ResNet34 架构的图像分类实战。首先完成必要的库导入工作。

[PRE2]

[PRE3]

在深度学习工程实践中，为增强模型泛化能力、使学习到的特征更加鲁棒，“数据增强”是标准动作。其核心逻辑是在数据总量受限的前提下，通过对原始图像施加灰度变换、随机裁剪、旋转翻转、亮度色调饱和度调节等多样化操作，人为扩充样本空间，从而显著提升网络性能。本实验同样采用了该策略对图像数据进行扩充。

[PRE4]

将 Caltech-101 数据集封装为标准 Dataset 类，并据此构建训练集、验证集与测试集对象。

[PRE5]

# 构建训练、验证及测试数据集实例

train_data = ImageDataset(x_train, y_train, train_transform)

val_data = ImageDataset(x_val, y_val, val_transform)

test_data = ImageDataset(x_test, y_test, val_transform)

[PRE6]python

BATCH_SIZE = 2048

trainloader = DataLoader(train_data, batch_size=BATCH_SIZE, shuffle=True)

valloader = DataLoader(val_data, batch_size=BATCH_SIZE, shuffle=True)

testloader = DataLoader(test_data, batch_size=BATCH_SIZE, shuffle=False)

[PRE7]python

# 自动检测硬件环境，优先使用 GPU 加速

device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

# 载入 ImageNet 预训练权重初始化 ResNet34 主干网络

model = torchvision.models.resnet34(pretrained='imagenet')

# 获取全连接层输入维度，替换分类头以适配 102 类别任务

num_ftrs = model.fc.in_features

model.fc = nn.Linear(num_ftrs, 102)

model.to(device)

# 启用多 GPU 并行加速训练

model = nn.DataParallel(model)

[PRE8]python

# 配置优化器：Adam，学习率 1e-4

optimizer = optim.Adam(model.parameters(), lr=1e-4)

# 定义损失函数：交叉熵损失

criterion = nn.CrossEntropyLoss()

[PRE9]python

# 定义单轮训练逻辑

def fit(model, dataloader):

    model.train()

    # 累计器：记录本轮总损失与 Top-1 错误样本数

    running_loss = 0.0

    running_top1_error = 0

    # 遍历数据迭代器

    for i, data in enumerate(dataloader):

        # 数据搬运至目标设备

        x, y = data[0].to(device), data[1].to(device)

        # 梯度清零：防止 PyTorch 累积上一轮残留梯度

        optimizer.zero_grad()

        # 前向传播获取预测 logits

        outputs = model(x)

        # 计算损失

        loss = criterion(outputs, y)

        # 累加损失值

        running_loss += loss.item()

        # 统计 Top-1 错误数

        _, preds = torch.max(outputs.data, 1)

        running_top1_error += torch.sum(preds != y)

        # 反向传播与参数更新

        loss.backward()

        optimizer.step()

    # 计算 epoch 级别平均指标

    loss = running_loss / len(dataloader.dataset)

    top1_error = running_top1_error / len(dataloader.dataset)

    print(f"Train Loss: {loss:.4f}, Train Top-1 Error: {top1_error:.4f}")

    return loss, top1_error

[PRE10]python

# 定义验证逻辑，用于监控模型泛化表现

def validate(model, dataloader):

    model.eval()

    running_loss = 0.0

    running_top1_error = 0

    # 关闭梯度计算，节省显存与算力

    with torch.no_grad():

        for i, data in enumerate(dataloader):

            x, y = data[0].to(device), data[1].to(device)

            outputs = model(x)

            loss = criterion(outputs, y)

            running_loss += loss.item()

            _, preds = torch.max(outputs.data, 1)

            # 此处素材原变量名为 running_top1_error，逻辑上累加错误数

            running_top1_error += torch.sum(preds != y)

    loss = running_loss / len(dataloader.dataset)

    top1_error = running_top1_error / len(dataloader.dataset)

    print(f'Val Loss: {loss:.4f}, Val Top-1 Error: {top1_error:.4f}')

    return loss, top1_error

[PRE11]python

# 定义测试评估函数，输出最终正确样本数与总样本数

def test(model, dataloader):

    top1_error = 0

    total = 0

    with torch.no_grad():

        for data in dataloader:

            x, y = data[0].to(device), data[1].to(device)

            outputs = model(x)

            _, predicted = torch.max(outputs.data, 1)

            total += y.size(0)

            # 累加预测正确的样本数（变量名 top1_error 实为 correct count）

            top1_error += torch.sum(predicted == y)

    return top1_error, total

[PRE12]python

# 启动模型训练主循环

epochs = 25

# 历史记录容器

train_loss , train_top1_error = [], []

val_loss , val_top1_error = [], []

print(f"Training on {len(train_data)} examples, \\

validating on {len(val_data)} examples...")

# Epoch 级迭代

for epoch in range(epochs):

    print(f"\nEpoch {epoch+1} of {epochs}")

    # 执行训练与验证

    train_epoch_loss, train_epoch_top1_error = fit(model, trainloader)

    val_epoch_loss, val_epoch_top1_error = validate(model, valloader)

    # 记录曲线数据

    train_loss.append(train_epoch_loss)

    train_top1_error.append(train_epoch_top1_error.cpu())

    val_loss.append(val_epoch_loss)

    val_top1_error.append(val_epoch_top1_error.cpu())

    # 可选：保存检查点

    # torch.save(model.state_dict(), "model.pth")