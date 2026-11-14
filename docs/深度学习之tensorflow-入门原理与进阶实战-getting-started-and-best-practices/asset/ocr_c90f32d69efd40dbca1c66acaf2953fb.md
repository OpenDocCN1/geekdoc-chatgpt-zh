• Scripts: 运行网络模型的一些案例脚本，这些脚本只能在支持 shell 的系统下使用。
在这里重点介绍 Datasets、nets 和 Preprocessing 这 3 个文件夹。

# 1. Dataset——数据集处理模块

Dataset 里放着常用的图片训练数据集相关的代码。主要支持的数据集主要有 cifar10、
flowers、mnist、imagenet。
代码文件的名字与数据集相对应，可以使用这些代码下载或获取数据集中的数据。以
imagenet 为例，可以使用如下函数从网上获取 imagenet 标签。

```
imagenet.create_readable_names_for_imagenet_labels()
```

上面代码返回的是 imagenet 中 1000 个类的分类标签名字（与样本序列对应）。

# 2. nets模块

nets 文件夹下包含前面介绍的各种网络模型，如图 11-8 所示。

![](bbox=[0.29, 0.415, 0.75, 0.595])

图 11-8 nets 文件结构

每个网络模型文件都是以自己的名字命名的，而且里面的代码结构框架也大致相同，
以 inception_resnet_v2 为例，如表 11-1 中列出了比较常用的函数接口。

# 表 11-1 slim中nets的代码框架接口

| 操作 | 说明 |
| :--- | :--- |
| inception_resnet_v2.default_image_size | 默认图片大小 |
| inception_resnet_v2.inception_resnet_v2 | 同名的网络结构函数，这个函数有两个输出，一个是预测结
果logits，另一个是辅助信息AuxLogits。辅助信息为了显示或
分析使用，主要包括summaries或losses |
| inception_resnet_v2_arg_scope | 命名空间的名字。在外层修改或使用模型时，可以使用与模
型相同的命名空间 |
| inception_resnet_v2_base | 为inception_resnet_v2的基本结构实现，输出inception_resnet_
v2网络中最原始的数据，默认是传到inception_resnet_
v2.inception_resnet_v2函数中，一般不会改动内部。当要使用
自定义的输出层时，会将传入自己的函数来替换inception_
resnet_v2.inception_resnet_v2 |