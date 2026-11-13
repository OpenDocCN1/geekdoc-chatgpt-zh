```
from torchvision import models

# 使用 ResNet101 作为主干网络，模型使用 ImageNet 预训练
pretrained_net = models.resnet101(pretrained='imagenet')

import torch.nn as nn

# FCN-8s 模型
class FCN8s(nn.Module):
    def __init__(self, num_classes):
        super(FCN8s, self).__init__()
        # 这里使用 children() 调用 ResNet101 的部分网络
        # 该深度特征图的大小为输入图像的 1/8
        self.stage1 = nn.Sequential(*list(pretrained_net.children())[:-4])
        # 该深度特征图的大小为输入图像的 1/16
        self.stage2 = list(pretrained_net.children())[-4]
        # 该深度特征图的大小为输入图像的 1/32
        self.stage3 = list(pretrained_net.children())[-3]

        # 调整 stage3 输出的通道数
        self.scores1 = nn.Conv2d(2048, num_classes, 1)
        # 调整 stage2 输出的通道数
        self.scores2 = nn.Conv2d(1024, num_classes, 1)
        # 调整 stage1 输出的通道数
        self.scores3 = nn.Conv2d(512, num_classes, 1)

        # 8 倍上采样
        self.upsample_8x = nn.ConvTranspose2d(
            num_classes, num_classes, 16, 8, 4, bias=False)
        self.upsample_8x.weight.data = bilinear_kernel(
            num_classes, num_classes, 16) # 使用双线性 kernel

        # 2 倍上采样
        self.upsample_2x = nn.ConvTranspose2d(
            num_classes, num_classes, 4, 2, 1, bias=False)
        self.upsample_2x.weight.data = bilinear_kernel(
            num_classes, num_classes, 4) # 使用双线性 kernel

    def forward(self, x):
        x = self.stage1(x)
        # s1 1/8
        s1 = x

        x = self.stage2(x)
        # s2 1/16
        s2 = x

        x = self.stage3(x)
        # s3 1/32
        s3 = x

        # 调整 pool5 输出特征图的通道数
```