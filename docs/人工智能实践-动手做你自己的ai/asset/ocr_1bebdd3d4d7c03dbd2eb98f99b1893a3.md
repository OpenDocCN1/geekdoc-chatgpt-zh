□ Activation：激活层。

■ Adam优化器：用于最小化损失函数。

■ 激活函数relu、tanh和LeakyReLu。

■ RandomNormal：用于神经网络参数的初始化。

```
# 如果没有安装 PIL(Pillow)
# 请使用 pip install pillow 安装
from PIL import Image
import numpy as np
import glob
import random
import time

# 如果没有安装 keras 和 tensorflow 库
# 请使用 pip install keras tensorflow 安装

import keras.backend as K

from keras.models import Model
from keras.layers import Conv2D, BatchNormalization, Input, Add
from keras.layers import Conv2DTranspose, Activation
from keras.optimizers import Adam

from keras.layers.advanced_activations import LeakyReLU
from keras.activations import relu,tanh
from keras.initializers import RandomNormal
```

# ## （一） 数据的读取

```
def load_image(fn, image_size):
    """
    加载一张图像
    fn:图像文件路径
    image_size:图像大小
    """
    im = Image.open(fn).convert('RGB')

    # 切割图像(截取图像中间的最大正方形，然后将大小调整至输入大小)
    if (im.size[0] >= im.size[1]):
        im = im.crop(((im.size[0] - im.size[1])//2, 0, (im.size[0] +
```