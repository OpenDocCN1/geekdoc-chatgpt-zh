# 编写函数计算 mIoU。

```
def _fast_hist(label_true, label_pred, n_class):
    mask = (label_true >= 0) & (label_true < n_class)
    hist = np.bincount(n_class * label_true[mask].astype(int) +
                       label_pred[mask],
                       minlength=n_class ** 2).reshape(n_class, n_class)
    return hist

def mIoU(label_trues, label_preds, n_class):
    # 计算mIoU
    hist = np.zeros((n_class, n_class))
    for lt, lp in zip(label_trues, label_preds):
        hist += _fast_hist(lt.flatten(), lp.flatten(), n_class)
    iu = np.diag(hist) / (hist.sum(axis=1) +
                          hist.sum(axis=0) - np.diag(hist))
    mean_iu = np.nanmean(iu)
    return mean_iu
```

# 加载 FCN-8s 模型，并对其进行训练。

```
device = device = torch.device('cuda' if torch.cuda.is_available()
                               else 'cpu')

# 加载模型
model = FCN8s(num_classes)
model.to(device)
model = nn.DataParallel(model)

# 设置优化器
optimizer = torch.optim.SGD(model.parameters(), lr=1e-2,
                            weight_decay=1e-4)

# 设置损失函数
criterion = nn.NLLLoss()

# 由于没有验证集，每训练完一次模型便在测试集上进行测试

# 不显示无关信息
np.seterr(divide='ignore', invalid='ignore')

Epoch = 120
for epoch in range(Epoch):
    # 训练损失
    train_loss = 0
    # 训练 mIoU
```