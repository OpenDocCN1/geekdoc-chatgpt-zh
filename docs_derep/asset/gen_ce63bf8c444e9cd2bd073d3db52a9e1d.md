# 剪枝实操笔记：稀疏度飙升与精度先跌后回

下面是一次完全可复现的随机数据实验，用来直观展示非结构化权重剪枝、结构化过滤器剪枝以及剪枝后微调对模型稀疏率和预测准确率的影响。整个流程分为评估函数、数据与模型初始化、剪枝操作、结果打印，以及另一套 MLP 演示框架。

准确率评估采用分批无梯度计算，先累加正确样本数再除以总量：

    correct = 0

    total = 0

    with torch.no_grad():

        for i in range(0, data.size(0), batch_size):

            batch_data = data[i:i+batch_size]

            batch_labels = labels[i:i+batch_size]

            outputs = model(batch_data)

            _, preds = torch.max(outputs, 1)

            correct += (preds == batch_labels).sum().item()

            total += batch_data.size(0)

    return 100.0 * correct / total

首先构造随机训练集与测试集，模型选用 SimpleCNN，在此之上统计初始稀疏度并完成三轮预训练。

[PRE0]python

if __name__ == '__main__':

    # 生成训练集与测试集的随机数据

    train_data, train_labels = create_random_data(num_samples=512, num_classes=10)

    test_data, test_labels = create_random_data(num_samples=128, num_classes=10)

[PRE1]python

model = SimpleCNN(num_classes=10)

print(">>> 初次训练前的模型稀疏率统计:")

p_total, p_zero, p_ratio = count_sparsity(model)

print(f"总参数数量: {p_total},零参数数量: {p_zero},稀疏率: {p_ratio:.2f}%\n")

print(">>> 开始初步训练...")

train_model(model, train_data, train_labels, epochs=3, batch_size=64)

acc_before = test_model(model, test_data, test_labels)

print(f"初步训练完成,随机测试集上的准确率: {acc_before:.2f}%\n")

# 7.1 基于权重剪枝 (Unstructured Pruning)

# 对fc2层的权值进行L1非结构化剪枝

print(">>> 应用基于权重剪枝到fc2层 (剪除30%的权重)...")

prune.global_unstructured(

    [(model.fc2, 'weight')],

    pruning_method=prune.L1Unstructured,

    amount=0.3

)

# 剪枝后统计与测试

p_total_un, p_zero_un, p_ratio_un = count_sparsity(model)

print(f"剪枝后(fc2),总参数数量: {p_total_un},零参数数量: {p_zero_un},稀疏率: {p_ratio_un:.2f}%")

acc_unstructured = test_model(model, test_data, test_labels)

print(f"剪枝后(fc2)未微调时,准确率: {acc_unstructured:.2f}%\n")

# 7.2 结构化剪枝 (Structured Pruning)

# 对conv2层进行结构化剪枝,移除部分卷积核

# 通过按L1范数将部分过滤器权值设置为0

print(">>> 应用结构化剪枝到conv2层 (剪除25%的过滤器)...")

# 结构化剪枝需要使用ln_structured

# dim=0 表示对输出通道进行剪枝

prune.ln_structured(

    module=model.conv2,

    name="weight",

    amount=0.25,

    n=1,

    dim=0

)

# 剪枝后统计与测试

p_total_st, p_zero_st, p_ratio_st = count_sparsity(model)

print(f"剪枝后(conv2),总参数数量: {p_total_st},零参数数量: {p_zero_st},稀疏率: {p_ratio_st:.2f}%")

acc_structured = test_model(model, test_data, test_labels)

print(f"剪枝后(conv2)未微调时,准确率: {acc_structured:.2f}%\n")

# 7.3 对剪枝后的模型进行微调 (Fine-tuning)

print(">>> 开始对剪枝后的模型进行微调...")

# 在微调前先移除剪枝掩码,使得权重中被剪除的部分保持为0

prune.remove(model.fc2, 'weight')

prune.remove(model.conv2, 'weight')

# 微调3个epoch

train_model(model, train_data, train_labels, epochs=3, batch_size=64)

# 微调后再次测试

p_total_final, p_zero_final, p_ratio_final = count_sparsity(model)

实际运行输出如下，可看出稀疏率逐步提升，剪枝后未调优精度小幅下降，微调后回升：

[PRE2]

>>> 初次训练前的模型稀疏率统计：

总参数量：72442，零参数量：0，稀疏率：0.00%

>>> 开始初步训练...

初步训练完成，随机测试集上的准确率：10.94%

>>> 应用基于权重剪枝到fc2层（剪除30%的权重）...

剪枝后(fc2)，总参数量：72442，零参数量：19392，稀疏率：26.77%

剪枝后(fc2)未微调时，准确率：9.38%

>>> 应用结构化剪枝到conv2层（剪除25%的过滤器）...

剪枝后(conv2)，总参数量：72442，零参数量：21456，稀疏率：29.63%

剪枝后(conv2)未微调时，准确率：9.58%

>>> 开始对剪枝后的模型进行微调...

>>> 微调完成后：

总参数量：72442，零参数量：21456，稀疏率：29.63%

最终准确率：11.09%

接下来提供一套通用的 MLP 演示骨架，包含随机种子固定、三层全连接网络、合成数据生成、训练、评估、稀疏率统计与 L1 非结构化剪枝接口。

[PRE3]python

import torch

import torch.nn as nn

import torch.optim as optim

import torch.nn.utils.prune as prune

import random

# 1. 设置随机种子，确保结果可复现

def set_seed(seed_value=1234):

    """

    设置Python和PyTorch的随机种子，以保证每次运行实验的可复现性

    参数：

        seed_value: 整型随机种子

    """

    random.seed(seed_value)

    torch.manual_seed(seed_value)

    if torch.cuda.is_available():

        torch.cuda.manual_seed_all(seed_value)

# 2. 构建一个多层感知器，用于演示剪枝及精度恢复流程

class MLPNetwork(nn.Module):

    """

    三层全连接网络：

    输入 -> 隐藏层1 -> 隐藏层2 -> 输出

    用于多分类任务

    """

    def __init__(self,input_dim=20,hidden_dim1=64,hidden_dim2=32,num_classes=5):

        super(MLPNetwork,self).__init__()

        self.fc1=nn.Linear(input_dim,hidden_dim1)

        self.relu1=nn.ReLU()

        self.fc2=nn.Linear(hidden_dim1,hidden_dim2)

        self.relu2=nn.ReLU()

        self.fc3=nn.Linear(hidden_dim2,num_classes)

    def forward(self,x):

        """

        前向传播

        x形状：[batch_size,input_dim]

        """

        x=self.relu1(self.fc1(x))

        x=self.relu2(self.fc2(x))

        x=self.fc3(x)

        return x

# 3. 模拟训练数据：随机生成多分类问题的数据

def generate_synthetic_data(num_samples=1000,input_dim=20,num_classes=5):

    """

    生成随机输入特征和随机标签

    参数：

        num_samples: 样本数量

        input_dim: 特征维度

        num_classes: 类别数

    返回：

        data: (num_samples,input_dim)的浮点张量

        labels: (num_samples,)的整型张量，表示类别

    """

    data=torch.randn(num_samples,input_dim)

    labels=torch.randint(low=0,high=num_classes,size=(num_samples,))

    return data,labels

# 4. 定义训练函数

def train_model(model,

                train_data,

                train_labels,

                num_epochs=5,

                batch_size=32,

                lr=0.001):

    """

    训练模型，使用随机小批量梯度下降进行多分类任务

    参数：

        model: 神经网络模型

        train_data: 训练数据张量 [N,input_dim]

        train_labels: 训练标签张量 [N]

        num_epochs: 训练轮数

        batch_size: 批大小

        lr: 学习率

    """

    model.train()

    optimizer=optim.Adam(model.parameters(),lr=lr)

    criterion=nn.CrossEntropyLoss()

    num_samples=train_data.size(0)

    for epoch in range(num_epochs):

        permutation=torch.randperm(num_samples)

        epoch_loss=0.0

        total_batches=0

        for i in range(0,num_samples,batch_size):

            indices=permutation[i:i+batch_size]

            batch_x=train_data[indices]

            batch_y=train_labels[indices]

            optimizer.zero_grad()

            outputs=model(batch_x)

            loss=criterion(outputs,batch_y)

            loss.backward()

            optimizer.step()

            epoch_loss += loss.item()

            total_batches += 1

        avg_loss=epoch_loss/total_batches

        print(f'Epoch [{epoch+1}/{num_epochs}], Avg Loss: {avg_loss:.4f}')

# 5. 定义评估函数，返回准确率

def evaluate_model(model,test_data,test_labels,batch_size=32):

    """

    测试模型在给定数据集上的分类准确率

    参数：

        model: 神经网络模型

        test_data: 测试数据 [N,input_dim]

        test_labels: 测试标签 [N]

        batch_size: 批大小

    返回：

        accuracy: 分类准确率 (百分比)

    """

    model.eval()

    correct=0

    total=0

    num_samples=test_data.size(0)

    with torch.no_grad():

        for i in range(0,num_samples,batch_size):

            batch_x=test_data[i:i+batch_size]

            batch_y=test_labels[i:i+batch_size]

            outputs=model(batch_x)

            _,preds=torch.max(outputs,1)

            correct += (preds == batch_y).sum().item()

            total += batch_y.size(0)

    accuracy=100.0*correct/total

    return accuracy

# 6. 剪枝前后权重稀疏率统计

def calculate_sparsity(model):

    """

    统计模型参数中的稀疏率(即为0的参数占比)

    返回：

        total_params: 模型总参数数量

        zero_params: 值为零的参数数量

        sparse_ratio: 稀疏率(百分比)

    """

    total_params=0

    zero_params=0

    for param in model.parameters():

        if param is not None:

            total_params += param.numel()

            zero_params += (param == 0).sum().item()

    sparse_ratio=100.0*zero_params/total_params

    return total_params,zero_params,sparse_ratio

# 7. 定义剪枝流程

# 此处演示对fc2层进行L1非结构化剪枝，移除若干权重

def prune_weights(model,prune_ratio=0.2):

    """

    对模型的某些层应用基于L1范数的非结构化剪枝

    参数:

        model: 神经网络模型