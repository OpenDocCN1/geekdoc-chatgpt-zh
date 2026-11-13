数据加载器非常像一个迭代器，每次返回 batch_size 幅图像与对应的标签。为什么不一次返回所有的图像呢？ batch_size 又是什么呢？

batch_size 表示单次传递给程序用以训练的数据个数。一般情况下，由于 GPU 资源以及内存的限制，无法将数据集中所有的图像数据输入模型进行训练，因此需要设置一个参数 batch_size，每次传入一部分数据进行训练，这样可以减少内存的消耗，还可以提高训练的速度，因为每次完成训练后模型都会变得更加精确。例如训练集中有 5486 幅图像，batch_size=100，那么模型会先调用训练集中的前 100 幅图像，即第 1～100 个数据来训练模型。当训练完成之后，再使用第 101～200 个数据训练，直至训练集中的数据全部输入模型为止。

在实验中，我们设置 batch_size 为 2048。如果你的计算资源不充分，可以将其调小一些。

```
BATCH_SIZE = 2048
trainloader = DataLoader(train_data, batch_size=BATCH_SIZE, shuffle=True)
valloader = DataLoader(val_data, batch_size=BATCH_SIZE, shuffle=True)
testloader = DataLoader(test_data, batch_size=BATCH_SIZE, shuffle=False)
```

加载 ResNet34 模型，这里将直接调用已经封装好的库。

```
# 根据当前设备选择使用 CPU 或者 GPU 训练
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')

# 加载 ImageNet 预训练的模型
model = torchvision.models.resnet34(pretrained='imagenet')
num_ftrs = model.fc.in_features
model.fc = nn.Linear(num_ftrs, 102)
model.to(device)

# 多个 GPU 并行训练
model = nn.DataParallel(model)
```

注意，在上述代码中出现了参数 pretrained，这是神经网络中的一个重要操作——预训练。大多数情况下，能够用于训练模型的算力和数据都很有限，因此我们希望能够尽量重复利用已经训练好的神经网络以节约训练和数据资源。如果在执行预测任务时，能够找到一个曾经执行过相似任务并被训练得很好的大型架构，那就可以使用这个大型架构中位置较浅的那些层来构筑自己的网络。借用已经训练好的模型来构筑新架构的技术就叫作预训练（pretrain）。具体而言，可以直接借用训练好的模型上的权重来初始化我们的模型。这里使用 ImageNet 数据集预训练的网络参数初始化 ResNet34。

定义训练时需要用到的优化器和损失函数。

```
# 定义优化器
optimizer = optim.Adam(model.parameters(), lr=1e-4)
# 定义损失函数
criterion = nn.CrossEntropyLoss()
```

关于优化器的相关知识可参阅《动手学深度学习》。使用常见的交叉熵损失来计算模型预