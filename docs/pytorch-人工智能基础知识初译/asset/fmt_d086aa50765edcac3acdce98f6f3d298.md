
```python
    writer.add_scalar('Test Loss', total_loss, epoch)
    writer.add_scalar('Accuracy', accuracy, epoch)
    writer.flush()

    return total_loss, accuracy
```

通过此秘籍，我们完成了测试函数。

# 测试函数

## 工作原理

在此秘籍中，我们编写了逆归一化函数以撤消在将图像转换为具有 ImageNet 统计信息的张量时建立的归一化。我们还定义了`misclassified_images()`方法，用于记录预测错误的图像。然后使用`SummaryWriter`对象中的`add_image()`方法将分类错误的图像添加到 TensorBoard 中，该方法将输入图像名称，图像和计数器。

然后，我们定义了`test()`方法，该方法在模型的验证数据集上运行验证，并使用`add_scalar()`方法记录测试损失和准确率，就像在训练函数中一样。最后，我们在验证数据集上返回了测试损失和模型准确率。

# 加载数据集

在此秘籍中，我们将加载肺炎数据集并将其转换为张量。该模型需要张量形式的数据，因此我们将需要对图像进行预处理以为其提供所需的数据。我们将执行数据扩充以增加数据集的大小。在将其输入模型之前，我们还将根据 ImageNet 数据集执行图像归一化。

## 操作步骤

在此秘籍中，我们将加载数据集：

1. 首先，我们将定义转换：
```
>>image_transforms = {
```
以下代码显示了训练集转换：
```
    'train':
    transforms.Compose([
        transforms.RandomResizedCrop(size=300, scale=(0.8, 1.1)),
        transforms.RandomRotation(degrees=10),
        transforms.ColorJitter(0.4, 0.4, 0.4),
        transforms.RandomHorizontalFlip(),
        transforms.CenterCrop(size=256), # Image net standards
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406],
                             [0.229, 0.224, 0.225]) # Imagenet standards
    ]),
```
以下代码显示了验证集转换：
```
    'val':
    transforms.Compose([
        transforms.Resize(size=300),
        transforms.CenterCrop(size=256),
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])
    ]),
```
以下代码显示了测试集转换：
```
    'test':
    transforms.Compose([
        transforms.Resize(size=300),
        transforms.CenterCrop(size=256),
        transforms.ToTensor(),
        transforms.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225])
    ]),
}
```
2. 然后，我们将定义图像路径，批量大小，日志路径和保存模型的路径：
```
>>datadir = '../input/chest-xray-pneumonia/chest_xray/chest_xray/'
>>traindir = datadir + 'train/'
>>validdir = datadir + 'test/'
>>testdir = datadir + 'val/'
>>model_path = "model.pth"
>>batch_size = 128
>>PATH_to_log_dir = 'logdir/'
```
3. 接下来，让我们从文件夹中加载图像：
```
>>data = {
    'train':
    datasets.ImageFolder(root=traindir, transform=image_transforms['train']),
    'val':
    datasets.ImageFolder(root=validdir, transform=image_transforms['val']),
    'test':
    datasets.ImageFolder(root=testdir, transform=image_transforms['test'])
}
```
4. 现在，我们将创建迭代器：
```
>>dataloaders = {
    'train': DataLoader(data['train'], batch_size=batch_size, shuffle=True),
    'val': DataLoader(data['val'], batch_size=batch_size, shuffle=True),
    'test': DataLoader(data['test'], batch_size=batch_size, shuffle=True)
}
```
5. 然后，我们将构建类标签：
```
>>LABEL = dict((v,k) for k,v in data['train'].class_to_idx.items())
```
有了这个秘籍，我们就可以准备好数据集。

## 工作原理

在此秘籍中，我们定义了训练，验证和测试数据集中图像所需的转换。根据数据集选择变换，并且归一化变换中的值来自 ImageNet 统计信息。然后，我们定义了数据集路径，模型名称，批量大小和日志目录。然后，我们使用`datasets.ImageFolder()`方法根据文件夹名称加载数据，并为每个数据集创建一个迭代器。

请注意，我们翻转了验证数据集和测试数据集目录。这是因为给定数据集的验证数据集确实很小，因此我们将测试数据集用于验证数据集。

我们还使用`DataLoader`为我们的训练，测试和验证数据集创建了迭代器。然后，我们创建了一个`LABEL`常量，这是一个字典，将分类器输出索引映射到类名。

# 定义 TensorBoard 编写器

在此秘籍中，我们将创建一个写入 TensorBoard 的对象。我们使用`SummaryWriter`对象写入 TensorBoard。我们可以使用 TensorBoard 编写标量值，绘图图和绘图图像以及其他。我们将定义一个返回 TensorBoard `SummaryWriter`对象以记录我们的模型指标的函数。

## 准备

此秘籍要求我们安装 TensorBoard 库。

我们需要安装 TensorBoard 夜间版本：
```
pip install tb-nightly
```
这样，我们准备实现该秘籍。

## 操作步骤

在此秘籍中，我们将创建编写器对象以将数据登录到 TensorBoard。

1. 我们将在命令行中输入以下内容来加载 TensorBoard：
```
tensorboard --logdir=log_dir/ --port 6006
```
您可以通过转到`http://localhost:6006/`在浏览器上访问 TensorBoard。

2. 接下来，我们将导入 TensorBoard：
```
>>from torch.utils.TensorBoard import SummaryWriter
```
3. 然后，我们将定义一个函数来获取 TensorBoard 编写器：
```
>>def tb_writer():
      timestr = time.strftime("%Y%m%d_%H%M%S")
      writer = SummaryWriter(PATH_to_log_dir + timestr)
      return writer
```
4. 然后，我们将创建一个图像网格以可视化数据集中的图像：
```
>>writer = tb_writer()
>>dataiter = iter(dataloaders['train'])
>>images, labels = dataiter.next()
>>grid = utils.make_grid([inv_normalize(image) for image in images[:32]])
>>writer.add_image('X-Ray grid', grid, 0)
>>writer.flush()
```
这样，我们就为 TensorBoard 做好了准备。

## 工作原理

在本秘籍中，我们首先从命令行加载 TensorBoard 以从`logdir/`目录中读取。即使目录不存在，TensorBoard 也不抛出错误，而是等待目录出现，因此传递正确的目录很重要。

然后，我们将 TensorBoard 导入代码中，并定义了`tb_writer()`函数，该函数返回一个新的`SummaryWriter`对象。我们向`writer`对象传递了 TensorBoard 日志要保存到的目录名称，并在时间戳`timestr`的帮助下，确保每个编写器都有一个唯一的目录来写入。

然后，我们使用`utils.make_grid()`创建了一个图像网格以查看训练数据集中的样本图像，并使用`add_image()`方法将其传递给 TensorBoard。我们使用`iter(dataloaders['train'])`提取了图像，然后选择了 32 个图像的样本并进行反归一化，然后制作了网格。我们还使用写入器中的`flush()`方法将缓冲区数据写入存储器。

# 训练模型和解冻层

在本秘籍中，我们将为数据集的预定义迭代次数完成模型训练。我们将在模型训练期间保存最佳模型。一旦针对给定的周期数训练了模型，我们将使用最佳模型的权重加载模型。然后，我们将解冻模型的先前冻结的 ResNet 层，并训练模型以较低的学习率微调权重。

## 操作步骤

在本秘籍中，我们将完成模型训练。

1. 将模型移至可用设备：
```
>>device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
>>model = get_model().to(device)
```
2. 定义条件和优化器：
```
>>criterion = nn.NLLLoss()
>>optimizer = optim.Adam(model.parameters())
```
3. 我们将定义一个函数来跨周期训练我们的模型：
```
>>def train_epochs(model, device, dataloaders, criterion, optimizer,epochs, writer):
    print('{0:>20} | {1:>20} | {2:>20} | {3:>20} |'.format('Epoch','Training Loss','Test Loss', 'Accuracy'))

    best_score = np.Inf

    for epoch in epochs:

        train_loss = train(model, device, dataloaders['train'], criterion, optimizer, epoch, writer)

       test_loss, accuracy = test(model, device, dataloaders['val'], criterion, epoch, writer)

        if test_loss < best_score:
            best_score = test_loss
            torch.save(model.state_dict(), model_path)

        print('{0:>20} | {1:>20} | {2:>20} | {3:>20.2f}% |'.format(epoch,train_loss,test_loss, accuracy))

        writer.flush()
```
4. 现在，我们将训练冻结模型：
```
>>train_epochs(model, device, dataloaders, criterion, optimizer, range(0,10), writer)
>>writer.close()
```
这是一个示例输出：
```
Epoch | Training Loss | Test Loss    | Accuracy |
 0    |  0.3617607994 | 0.4970110774 |  79.17%  |
 1    |   0.233799973 | 0.3870731115 |  84.78%  |
 2    |  0.2014380195 |   0.37044851 |  85.26%  |
 3    |   0.190022166 |  0.362625807 |  86.86%  |
 4    |   0.176903085 |   0.40945090 |  85.90%  |
 5    |   0.163670904 | 0.3690894782 |  86.86%  |
 6    |  0.1607481929 |  0.418265098 |  84.46%  |
 7    |  0.1615160162 | 0.4016072392 |  85.58%  |
 8    |  0.1519727988 |  0.481940734 |  84.13%  |
 9    |  0.1441831755 |  0.433110350 |  84.46%  |
```
5. 我们将定义一个函数来解冻模型：
```
>>def unfreeze(model):
    for param in model.parameters():
        param.requires_grad = True
```
6. 到目前为止，我们将加载最佳模型并解冻模型：
```
>>model.load_state_dict(torch.load(model_path))
>>unfreeze(model)
```
7. 我们将更新优化器以降低学习速度：
```
>>optimizer = optim.Adam(model.parameters(), lr=1e-6)
```
8. 我们将训练未冻结的模型：
```
>>writer = tb_writer()
>>train_epochs(model, device, dataloaders, criterion, optimizer, range(9,14), writer)
```
这是一个示例输出：
```
Epoch |        Training Loss |            Test Loss |  Accuracy  |
    9 |  0.15972968554351388 |  0.41342413425445557 |   85.10%   |
   10 |   0.1224460500042613 |   0.3801746487617493 |   86.54%   |
   11 |   0.1217333177422605 |  0.37790409922599794 |   87.18%   |
   12 |  0.11098722713749583 |   0.3712982594966888 |   87.98%   |
   13 |  0.09877484373566581 |  0.41088773012161256 |   86.70%   |
   14 |  0.09256085244620718 |   0.3181425631046295 |   89.42%   |
```
9. 我们最终将关闭 TensorBoard 编写器：
```
>>writer.close()
```
在以下屏幕截图中，我们可以看到训练 TensorBoard 中查看的模型后生成的图：

![](Images/95dfca3aaebc5420a7899db70436c49f.png)

图 1：TensorBoard 主仪表板

这是我们创建的图像网格：

![](Images/856510b4be268814ce9846e012f025fe.png)

图 2：图像网格

这是一个预示肺炎的误分类示例，但这实际上是正常的：

![](Images/07f6bd1461fc6274e6d9cd61d34266ce.png)

图 3：预计的肺炎，实际上是正常的

这是一个预测正常的误分类示例，但实际上是肺炎：

![](Images/943d894cbe33992eb0e2fa79f7396314.png)

图 4：预测的正常，实际上是肺炎

这是显示我们的训练损失随时间减少的图：

![](Images/7937aa1e3081e5f3077d9e130cf20486.png)

图 5：训练损失

这是显示我们的测试损失随时间减少的图：

![](Images/55955cf571843db6620311657fb24af5.png)

图 6：测试损失

以下图表显示了我们的精度随着时间的推移而提高：

![](Images/11cbf08d16bfe6e8c4027687602368b7.png)

图 7：准确率得分

通过此秘籍，我们已经训练了模型并在 TensorBoard 中可视化了其结果。

## 工作原理
