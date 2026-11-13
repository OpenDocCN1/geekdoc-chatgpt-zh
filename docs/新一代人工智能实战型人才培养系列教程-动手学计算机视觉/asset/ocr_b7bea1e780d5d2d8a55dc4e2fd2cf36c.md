测的标签与真实标签的误差，其定义如下：

\(\ell = -\frac{1}{N} \sum_{n=1}^{N} \log p(y^{(n)} | I^{(n)})\)

接下来，编写训练模型、验证模型和测试模型 top-1 错误率的函数。

```
# 定义训练函数
def fit(model, dataloader):

    model.train()

    # 初始化模型损失以及模型 top-1 错误率
    running_loss = 0.0
    running_top1_error = 0

    # 开始迭代
    for i, data in enumerate(dataloader):
        # 准备好训练的图像和标签，每次传入的数据量为 batch_size
        x, y = data[0].to(device), data[1].to(device)
        # 需要在开始进行反向传播之前将梯度设置为零
        # 因为 PyTorch 会在随后的反向传播中累积梯度
        optimizer.zero_grad()

        # 将数据传入模型，获得输出的预测标签
        outputs = model(x)

        # 将预测标签与真实标签比较，计算损失
        loss = criterion(outputs, y)

        # 记录当前损失
        running_loss += loss.item()

        # 记录当前 top-1 错误率
        _, preds = torch.max(outputs.data, 1)
        running_top1_error += torch.sum(preds != y)

        # 反向传播
        loss.backward()
        optimizer.step()

    loss = running_loss / len(dataloader.dataset)
    top1_error = running_top1_error / len(dataloader.dataset)

    print(f"Train Loss: {loss:.4f}, Train Top-1 Error: {top1_error:.4f}")

    return loss, top1_error
```

```
# 定义验证函数，可以通过验证集对模型的参数进行调整
def validate(model, dataloader):
```