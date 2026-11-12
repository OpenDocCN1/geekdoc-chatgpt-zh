```
name: "drop7"
type: DROPOUT
bottom: "fc7"
top: "fc7"
dropout_param {
dropout_ratio: 0.5
}
}
```

(6) 第 6 层：为输出层，对于性别来说是二分类，输入神经元个数为 2，代码如下。

```
layers {
name: "fc8"
type: INNER_PRODUCT
bottom: "fc7"
top: "fc8"
blobs_lr: 10
blobs_lr: 20
weight_decay: 1
weight_decay: 0
inner_product_param {
num_output: 2
weight_filler {
type: "gaussian"
std: 0.01
}
bias_filler {
type: "constant"
value: 0
}
}
}
layers {
name: "accuracy"
type: ACCURACY
bottom: "fc8"
bottom: "label"
top: "accuracy"
include: { phase: TEST }
}
layers {
name: "loss"
type: SOFTMAX_LOSS
bottom: "fc8"
bottom: "label"
top: "loss"
}
```

# 11.5.3 模型训练

模型训练包括以下 4 个步骤。

(1) 初始化参数：权重初始化方法采用标准差为 0.01，均值为 0 的高斯正态分布。

(2) 网络训练：采用 dropout 来限制过拟合， dropout 比例采用 0.5。还要进行数据扩充，数据扩充是通过输入 256px×256px 的图片进行随机裁剪，裁剪为 227px×227px 的图片，当然要以人脸的中心为基点进行裁剪。