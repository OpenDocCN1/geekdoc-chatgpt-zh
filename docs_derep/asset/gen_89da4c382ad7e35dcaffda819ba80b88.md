# ResNet18 在 CIFAR-10 上跑通训练的实操记录

把一个经典残差网络搭起来、把 CIFAR-10 的 pickle 数据读进来、再用 PyTorch 2.0 的编译加速跑一轮完整训练，整个链路其实非常清晰。下面按搭建网络、读取数据、训练闭环三步走复盘。

先定义网络骨架。残差阶段按步长依次组装模块，forward 里依次经过 conv1、四个阶段的残差块、全局平均池化后用 view 拉平成向量再送入全连接做分类。提供18层和34层的便捷工厂函数，最后用随机张量验证输出形状是否合理。
        layers = []

        for stride in strides:

            layers.append(block(self.in_channels, out_channels, stride))

            self.in_channels = out_channels * block.expansion

        return nn.Sequential(*layers)

    def forward(self, x):

        output = self.conv1(x)

        output = self.conv2_x(output)

        output = self.conv3_x(output)

        output = self.conv4_x(output)

        output = self.conv5_x(output)

        output = self.avg_pool(output)

        #首先使用view作为全局池化层，fc是最终的分类函数，为每层对应的类别进行分类计算

        output = output.view(output.size(0), -1)

        output = self.fc(output)

        return output

#18层的ResNet

def resnet18():

    return ResNet(BasicBlock, [2, 2, 2, 2])

#34层的ResNet

def resnet34():

    return ResNet(BasicBlock, [3, 4, 6, 3])

if __name__ == '__main__':

    image = torch.randn(size=(5,3,224,224))

    resnet = ResNet(BasicBlock, [2, 2, 2, 2])

    img_out = resnet(image)

    print(img_out.shape)

CIFAR-10 的原始文件是 pickle 格式，先封装一个统一的读取工具，指定 latin1 编码避免解压错误。
[PRE0]python

import pickle

def load_file(filename):

    with open(filename, 'rb') as fo:

        data = pickle.load(fo, encoding='latin1')

    return data
读取第一批数据并查看键名，可以确认包含标签、原始像素、文件名等字段。
[PRE1]python

data = load_file('data_batch_1')

print(data.keys())
[PRE2]

dict_keys([ 'batch_label', 'labels', 'data', 'filenames' ])
基于读取函数封装训练集和测试集的批量拼接。训练集由 data_batch_1 到 data_batch_5 拼合，测试集使用 test_batch，返回的是原始的 NumPy 数组。
[PRE3]python

import pickle

import numpy as np

import os

def get_cifar10_train_data_and_label(root=""):

    def load_file(filename):

        with open(filename, 'rb') as fo:

            data = pickle.load(fo, encoding='latin1')

        return data

    data_batch_1 = load_file(os.path.join(root, 'data_batch_1'))

    data_batch_2 = load_file(os.path.join(root, 'data_batch_2'))

    data_batch_3 = load_file(os.path.join(root, 'data_batch_3'))

    data_batch_4 = load_file(os.path.join(root, 'data_batch_4'))

    data_batch_5 = load_file(os.path.join(root, 'data_batch_5'))

    dataset = []

    labelset = []

    for data in [data_batch_1, data_batch_2, data_batch_3, data_batch_4, data_batch_5]:

        img_data = (data["data"])

        img_label = (data["labels"])

        dataset.append(img_data)

        labelset.append(img_label)

    dataset = np.concatenate(dataset)

    labelset = np.concatenate(labelset)

    return dataset, labelset

def get_cifar10_test_data_and_label(root=""):

    def load_file(filename):

        with open(filename, 'rb') as fo:

            data = pickle.load(fo, encoding='latin1')

        return data

    data_batch_1 = load_file(os.path.join(root, 'test_batch'))

    dataset = []

    labelset = []

    for data in [data_batch_1]:

        img_data = (data["data"])

        img_label = (data["labels"])

        dataset.append(img_data)

        labelset.append(img_label)

    dataset = np.concatenate(dataset)

    labelset = np.concatenate(labelset)

    return dataset, labelset

def get_CIFAR10_dataset(root=""):

    train_dataset, label_dataset = get_cifar10_train_data_and_label(root=root)

    test_dataset, test_label_dataset = get_cifar10_train_data_and_label(root=root)

    return train_dataset, label_dataset, test_dataset, test_label_dataset

if __name__ == "__main__":

    train_dataset, label_dataset, test_dataset, test_label_dataset = get_CIFAR10_dataset(root="../dataset/cifar-10-batches-py/")

    train_dataset = np.reshape(train_dataset,[len(train_dataset),3,32,32]).astype(np.float32)/255.

    test_dataset = np.reshape(test_dataset,[len(test_dataset),3,32,32]).astype(np.float32)/255.

    label_dataset = np.array(label_dataset)

    test_label_dataset = np.array(test_label_dataset)
数据重塑成 3×32×32 并归一化到 0-1 区间后即可进入训练。选用 ResNet18，模型送到可用 GPU，开启 torch.compile 加速，Adam 学习率设为 2e-5，交叉熵损失，batch 128，累计 63 个 epoch，每轮打印训练损失、即时准确率和少量测试集的评估准确率。
[PRE4]python

import torch

import resnet

import get_data

import numpy as np

train_dataset, label_dataset, test_dataset, test_label_dataset = get_data.get_CIFAR10_dataset(root="../dataset/cifar-10-batches-py/")

train_dataset = np.reshape(train_dataset,[len(train_dataset),3,32,32]).astype(np.float32)/255.

test_dataset = np.reshape(test_dataset,[len(test_dataset),3,32,32]).astype(np.float32)/255.

label_dataset = np.array(label_dataset)

test_label_dataset = np.array(test_label_dataset)

device = "cuda" if torch.cuda.is_available() else "cpu"

model = resnet.resnet18() #导入ResNet模型

model = model.to(device) #将计算模型传入GPU硬件等待计算

model = torch.compile(model) #PyTorch 2.0的特性，加速计算速度

optimizer = torch.optim.Adam(model.parameters(), lr=2e-5) #设定优化函数

loss_fn = torch.nn.CrossEntropyLoss()

batch_size = 128

train_num = len(label_dataset)//batch_size

for epoch in range(63):

    train_loss = 0.

    for i in range(train_num):

        start = i * batch_size

        end = (i + 1) * batch_size

        x_batch = torch.from_numpy(train_dataset[start:end]).to(device)

        y_batch = torch.from_numpy(label_dataset[start:end]).to(device)

        pred = model(x_batch)

        loss = loss_fn(pred, y_batch.long())

        optimizer.zero_grad()

        loss.backward()

        optimizer.step()

        train_loss += loss.item()  # 记录每个批次的损失值

    # 计算并打印损失值

    train_loss /= train_num

    accuracy = (pred.argmax(1) == y_batch).type(torch.float32).sum().item() / batch_size

    #2048可根据读者GPU显存大小调整

    test_num = 2048

    x_test = torch.from_numpy(test_dataset[:test_num]).to(device)

    y_test = torch.from_numpy(test_label_dataset[:test_num]).to(device)

    pred = model(x_test)

    test_accuracy = (pred.argmax(1) == y_test).type(torch.float32).sum().item() / test_num

    print("epoch: ",epoch,"train_loss:",round(train_loss,2),";accuracy:",round(accuracy,2),";test_accuracy:",round(test_accuracy,2))
读取工具的最小实现片段，方便在其他脚本中复用。
[PRE5]python

import pickle

import numpy as np

import os

def get_cifar10_train_data_and_label(root=""):

    def load_file(filename):

        with open(filename, 'rb') as fo:

            data = pickle.load(fo, encoding='latin1')