(a) 线性整流函数 (ReLU)

(b) 带泄露线性整流函数 (Leaky ReLU)

# (四) 整体网络结构的搭建

采用“类”的概念组织GAN的网络结构：

```
def loss_func(output, target):
    ...
    损失函数
    论文中提到使用平方损失更好
    ...
    return K.mean(K.abs(K.square(output-target)))

class CycleGAN(object):
    def __init__(self, image_size=256, lambda_cyc=10, lrd = 2e-4, lrg = 2e-
    4, ndf = 64, ngf = 64, resnet_blocks = 9):
        ...

        构建网络结构

            cyc loss
        +--------------------------------------+
        |         (CycleA)                     |
        |   v                                  |
        | realA -> [GB] -> fakeB -> [GA] -> recA |
        |   |           |                      |
        |   |  +----------------------+        |
        |   |  |                      |        |
        |   v  v                      v        v |
        | [DA] <CycleGAN>           [DB]      |
        |   ^           ^               ^       |
        |   |           |               |       |
        |   +----------------------+   |       |
        |   |                      |   |       |
        | recB <- [GB] <- fakeA <- [GA] <- realB |
        |   |           ^               |       |
        |   |   (CycleB)|               |       |
        |   +----------------------+   |       |
        |           cyc loss           |
        ...
```