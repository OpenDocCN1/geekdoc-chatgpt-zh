```
block1_conv1 (Conv2D)               (None, 150, 150, 64)   1792
block1_conv2 (Conv2D)               (None, 150, 150, 64)   36928
block1_pool (MaxPooling2D)          (None, 75, 75, 64)     0
block2_conv1 (Conv2D)               (None, 75, 75, 128)    73856
block2_conv2 (Conv2D)               (None, 75, 75, 128)    147584
block2_pool (MaxPooling2D)          (None, 37, 37, 128)    0
block3_conv1 (Conv2D)               (None, 37, 37, 256)    295168
block3_conv2 (Conv2D)               (None, 37, 37, 256)    590080
block3_conv3 (Conv2D)               (None, 37, 37, 256)    590080
block3_pool (MaxPooling2D)          (None, 18, 18, 256)    0
block4_conv1 (Conv2D)               (None, 18, 18, 512)    1180160
block4_conv2 (Conv2D)               (None, 18, 18, 512)    2359808
block4_conv3 (Conv2D)               (None, 18, 18, 512)    2359808
block4_pool (MaxPooling2D)          (None, 9, 9, 512)      0
block5_conv1 (Conv2D)               (None, 9, 9, 512)      2359808
block5_conv2 (Conv2D)               (None, 9, 9, 512)      2359808
block5_conv3 (Conv2D)               (None, 9, 9, 512)      2359808
block5_pool (MaxPooling2D)          (None, 4, 4, 512)      0
==================================================
Total params: 14,714,688
Trainable params: 14,714,688
Non-trainable params: 0
None
```

加载预训练的 VGG16 模型，仅加载具有分类作用的卷积部分，不包含主要承担分类作用的全连接层。

在编译和训练模型之前，冻结一个或多个层，使其在训练过程中保持权重不变。我们会