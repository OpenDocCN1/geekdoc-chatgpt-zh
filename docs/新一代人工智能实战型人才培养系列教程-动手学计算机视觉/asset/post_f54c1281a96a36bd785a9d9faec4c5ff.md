
```
voc_train = VOCSegDataset(True, input_shape, img_transforms)
voc_test = VOCSegDataset(False, input_shape, img_transforms)
```

Read 1114 images
Read 1078 images

```
# 设置 batch_size, 生成数据加载器
BATCH_SIZE = 64
train_data = DataLoader(voc_train, batch_size=BATCH_SIZE, shuffle=True)
test_data = DataLoader(voc_test, batch_size=BATCH_SIZE)
```

展示数据集中的部分图像与其对应的标签。

```
for i, (img, label) in enumerate(voc_train):
    plt.figure(figsize=(10, 10))
    plt.subplot(221)
    plt.imshow(img.moveaxis(0, 2))
    plt.subplot(222)
    plt.imshow(label)
    plt.show()
    plt.close()
    if i == 1:
        break
```

Clipping input data to the valid range for imshow with RGB data ([0..1] for floats
or [0..255] for integers).

![](img/0e497601c0ca26be98c5399cbd72eb4c_172_0.png)

Clipping input data to the valid range for imshow with RGB data ([0..1] for floats
or [0..255] for integers).

![](img/0e497601c0ca26be98c5399cbd72eb4c_172_1.png)

### 编写函数计算 mIoU。

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

### 加载 FCN-8s 模型，并对其进行训练。

```
device = torch.device('cuda' if torch.cuda.is_available()
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
    train_mean_iu = 0

    # 开始训练
    model = model.train()

    for data in train_data:
        x = data[0].to(device)
        y = data[1].to(device)

        # 梯度清零
        optimizer.zero_grad()

        # 获得模型预测概率分布
        outputs = model(x)
        # 获得模型预测的标签，大小为(b,n,h,w)
        outputs = F.log_softmax(outputs, dim=1)

        # 计算损失
        loss = criterion(outputs, y)

        # 反向传播
        loss.backward()
        optimizer.step()

        # 记录损失
        train_loss += loss.item()

        # 获得预测标签
        label_pred = outputs.max(dim=1)[1].data.cpu().numpy()
        label_true = y.data.cpu().numpy()

        for lbt, lbp in zip(label_true, label_pred):
            # 返回每幅图像的 pred 和 gt 标签，计算 mIoU
            mean_iu = mIoU(lbt, lbp, num_classes)
            train_mean_iu += mean_iu

    # 测试损失
    eval_loss = 0
    # 测试 mIoU
    eval_mean_iu = 0

    # 进行模型测试
    model = model.eval()

    for data in test_data:
        x_test = data[0].to(device)
        y_test = data[1].to(device)
        outputs_test = model(x_test)
        outputs_test = F.log_softmax(outputs_test, dim=1)

        loss = criterion(outputs_test, y_test)
        eval_loss += loss.item()

        label_pred = outputs_test.max(dim=1)[1].data.cpu().numpy()
        label_true = y_test.data.cpu().numpy()

        for lbt, lbp in zip(label_true, label_pred):
            mean_iu = mIoU(lbt, lbp, num_classes)
            eval_mean_iu += mean_iu

    epoch_str = ('Epoch: {}, Train Loss: {:.5f}, Train Mean IU: {:.5f},\
        Valid Loss: {:.5f}, Valid Mean IU: {:.5f}'.format(
        epoch, train_loss / len(train_data),
        train_mean_iu / len(voc_train),
        eval_loss / len(test_data),
        eval_mean_iu / len(voc_test)))

    print(epoch_str)
```

为了方便，在这里不展示具体的训练信息输出。我们随机选择一些图像，并展示其实际标签与 FCN-8s 模型预测的标签。

```
# 将预测的标签映射到 colormap 上
cm = np.array(colormap).astype('uint8')

def predict(im, label):
    im = Variable(im.unsqueeze(0)).cuda()
    out = model(im)
    pred = out.max(1)[1].squeeze().cpu().data.numpy()
    pred = cm[pred]
    return pred, cm[label.numpy()]

_, figs = plt.subplots(10, 3, figsize=(12, 10))

for i in range(10):
    x, y = voc_test[i]
    x.to(device)
    y.to(device)
    pred, label = predict(x, y)
    figs[i, 0].imshow(Image.open(voc_test.data_list[i]))
    figs[i, 0].axes.get_xaxis().set_visible(False)
    figs[i, 0].axes.get_yaxis().set_visible(False)
    figs[i, 1].imshow(label)
    figs[i, 1].axes.get_xaxis().set_visible(False)
    figs[i, 1].axes.get_yaxis().set_visible(False)
    figs[i, 2].imshow(pred)
    figs[i, 2].axes.get_xaxis().set_visible(False)
    figs[i, 2].axes.get_yaxis().set_visible(False)
```

![](img/0e497601c0ca26be98c5399cbd72eb4c_176_0.png)

![](img/0e497601c0ca26be98c5399cbd72eb4c_176_1.png)

## 11.5 小结

本章介绍了语义分割的相关知识。我们介绍了分割网络与分类网络的区别，在此基础上讲解了用于语义分割的网络 FCN，之后动手实现了 FCN 的相应功能并在 PASCAL VOC 数据集上进行了训练和测试。作为计算机视觉的重要研究内容之一，语义分割为后续的图像语义理解，如目标检测、实例分割等奠定了一定的基础。

- ### 习题

- （1）在 FCN 中，融合主干网络不同层级输出的特征图是通过跳跃连接后逐像素相加。还能想到别的融合方式吗？动手实现你想到的融合方式，并和原来的融合方式进行比较。

- （2）如果不同类别的像素差别很大，采用本章中介绍的逐像素交叉熵损失函数训练 FCN 做语义分割可能存在什么问题？如果存在问题，应该如何修改逐像素交叉熵损失函数？

- （3）在 11.1 节中，我们说语义分割需要建模条件概率 P(Y|I)，但是在 FCN 中建模的是每个像素的条件概率 P(y_i | I)。这是为了规避 Y 的求解空间太大所采取的一种近似求解。这种近似会导致 FCN 的分割结果出现什么问题？

- （4）在第 9 章中，我们介绍了无监督图像分割。无监督图像分割中的思想或者方法是否有助于改进 FCN，提升语义分割的结果？给出解决思路，并动手实现予以验证。

## 11.6 参考文献

- [1] EVERINGHAM M, VAN GOOL L, WILLIAMS C K I, et al. The pascal visual object classes (voc) challenge[J]. International Journal of Computer Vision, 2010, 88:303-338.

- [2] CORDTS M, OMRAN M, RAMOS S, et al. The cityscapes dataset for semantic urban scene understanding[C]//IEEE Conference on Computer Vision and Pattern Recognition, 2016:3213-3223.

- [3] ZHOU B, ZHAO H, PUIG X, et al. Semantic understanding of scenes through the ade20k dataset[J]. International Journal of Computer Vision, 2019, 127:302-321.

- [4] LONG J, SHELHAMER E, DARRELL T. Fully convolutional networks for semantic segmentation [C]//IEEE Conference on Computer Vision and Pattern Recognition, 2015: 3431-3440.

- [5] DUMOULIN V, VISIN F. A guide to convolution arithmetic for deep learning[EB/OL]. (2016-03-23)[2024-09-25]. arXiv: 1603.07285.

## 第 12 章

### 目标检测

### 12.1 简介

![](img/0e497601c0ca26be98c5399cbd72eb4c_177_0.png)

目标检测（object detection）是计算机视觉中的一个核心问题，其目的是识别图像中每个目标的类别并且对它们进行定位。定位（localization）即确定目标的位置和尺寸，通常需要勾勒出目标轮廓的包围盒（bounding box），确定该包围盒的中心点坐标及宽和高。用数学语言来描述目标检测的问题，即定义图像空间 I 和预定义的类别集合 C，给定数据集 D = {(I^(n), Y^(n))}^N_{n=1}，其中，I^(n) ∈ I 是数据集中的第 n 幅图像，Y^(n) = {<c^(n,m), b^(n,m)>}^M_{m=1} 是其对应的目标包围盒标签，M^(n) 是这幅图像中目标的个数，二元组 <c^(n,m), b^(n,m)> 是这幅图像中第 m 个目标的包围盒标签，c^(n,m) ∈ C 是类别标签，b^(n,m) = (b_x, b_y, b_h, b_w)^(n,m) 是一个四元组，表示包围盒坐标（由于该四元组 b^(n,m) 表示了一个唯一确定的包围盒，我们称之为包围盒的坐标，本章提到的包围盒坐标均指代该四元组），包括包围盒中心点坐标 (b_x, b_y) 以及包围盒的高 b_h 和宽 b_w；图像分类的任务是从 D 中学习得到一个从图像空间到包围盒集合的映射 f : I -> Y，从而给定任意一幅测试图像 I，可以用学习得到的映射函数 f 预测该图像中目标包围盒集合：\hat{Y} = f(I)。

图 12-1 展示了一个目标检测的例子，图中的“人”“玩具”和“椅子”被识别出来，它们的包围盒也被勾勒出来。由于目标检测的输出是包围盒这种形状规则的矩形，在工业领域中对下游业务适用性高，所以得到广泛应用，例如辅助驾驶系统中的感知模块的输出就是检测到的目标的包围盒。在本章中，我们将介绍基于深度学习的目标检测方法，并动手实现相应的目标检测框架。

![](img/0e497601c0ca26be98c5399cbd72eb4c_177_1.png)

图 12-1 目标检测示例

### 12.2 数据集和度量

常用于目标检测的数据集包括：PASCAL VOC、MS COCO 和 ImageNet，其中 PASCAL VOC 和 ImageNet 数据集在之前的章节中已经介绍过，此处不再赘述。

MS COCO（Microsoft common objects in context）数据集是一个广泛用于计算机视觉任务的大型数据集，于 2014 年由微软发布。它主要用于目标检测、分割、关键点检测和图像描述生成等任务。MS COCO 数据集的目标是为了推动计算机视觉技术在更复杂的场景中的发展。MS COCO 数据集有以下特点。

- (1) 图像数量多：数据集包含了约 20 万幅标注过的图像，以及约 13 万幅未标注的图像。

- (2) 类别和来源丰富：图像涵盖了 80 个类别的物体，如动物、交通工具、家具等，同时图像来源丰富，包括从网络上抓取的图像、街景图像和室内图像等。

- (3) 场景复杂：数据集中的图像包含了各种复杂的场景，如拥挤的市场、交通堵塞的道路等，这对计算机视觉模型提出了较高的挑战。

最常用的目标检测评价指标是全类平均精度（mean average precision，mAP）。从 mAP 的命名可以看出，这是一个定义在每个类别上的精度指标。因此在定义 mAP 之前，需要先定义一个目标是否被检测到的准则。如图 12-2 所示，对于图像中的一个目标，令其真实的（ground-truth）包围盒内部的像素集合为 A，一个预测的包围盒内部的像素集合为 B，通过这两个包围盒内像素集合的交并比（intersection over union，IoU）来定义两个包围盒的重合度，其中，IoU(A, B) = \frac{A \cap B}{A \cup B}，即图 12-2 (a) 中灰色部分的面积除以图 12-2 (b) 中灰色部分的面积。注意，这里 IoU 的定义与第 11 章中对 IoU 的定义在数学上等价，在目标检测中，IoU 也称为 Box IoU。给定一个阈值 \theta，如果 IoU(A, B) > \theta，则认为 B 所属的预测包围盒命中了该目标；反之，则认为 B 所属的预测包围盒并未命中该目标。根据这个基于 IoU 的准则，给定一种类别，对每个预测类别为该给定类别的预测包围盒，如果它内部的像素集合与某个类别标签为该给定类别的真实包围盒内部的像素集合之间的 IoU 大于给定阈值，则这个预测包围盒是真阳性（true positive，TP）；如果其内部的像素集合与任意一个类别标签为该给定类别的真实包围盒内部的像素集合的 IoU 都小于给定阈值，那么这个预测包围盒是假阳性（false positive，FP）；如果一个类别标签为该给定类别的真实包围盒中的目标没有被任何预测包围盒命中，那么这是假阴性（false negative，FN）。这样，对每幅图像的检测结果，可以计算出对于该给定类别的查全率（recall），即正确检测相对于所有真实包围盒的比例，以及查准率（precision，也称为精度），即正确检测相对于所有预测包围盒的比例：

```
recall = \frac{TP}{TP + FN}

precision = \frac{TP}{TP + FP}
```

![](img/0e497601c0ca26be98c5399cbd72eb4c_178_0.png)
