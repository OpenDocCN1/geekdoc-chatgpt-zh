# 11.4 FCN 代码实现

下面，我们将编程实现 FCN。先导入所需要的库。

```
import os
import random
import cv2
import d2l.torch as d2l
from PIL import Image
import matplotlib.pyplot as plt

import torch.nn.functional as F
from torch.utils.data import DataLoader
from torch.utils.data import Dataset
from torch.autograd import Variable
from torchvision.transforms import transforms as tfs
```

我们使用 PASCAL VOC 作为实验的数据集。

```
# 数据集地址
# 可自行下载数据集
voc_root = './voc/VOCdevkit/VOC2012'

# 定义 PASCAL VOC 类
class VOCSegDataset(Dataset):
    def __init__(self, self, train, crop_size, transforms):
        # 定义数据大小
        self.crop_size = crop_size
        # 定义数据增强类型
        self.transforms = transforms

        # 读取数据以及对应标签
        data_list, label_list = read_images(voc_root, train=train)
        self.data_list = self._filter(data_list)
        self.label_list = self._filter(label_list)
        print('Read'+str(len(self.data_list))+'images')

    def _filter(self, images):
        return [im for im in images if
                (Image.open(im).size[1] >= self.crop_size[0] and
                 Image.open(im).size[0] >= self.crop_size[1])]

    # 定义数据如何进行传输
    def __getitem__(self, idx):
        img = self.data_list[idx]
        label = self.label_list[idx]
        # 读取图像
        img = cv2.imread(img)
        # 将图像通道从 BGR 转换成 RGB
        img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```