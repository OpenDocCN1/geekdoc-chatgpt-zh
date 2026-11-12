(2) 根据口罩检测模型的数据集，修改data/yolov5s-mask.yaml文件内的相关参数：

```
path: ../ #dataset root dir
train: ../object_train.txt #train images (relative to 'path')
val: ../object_val.txt #val images (relative to 'path')

#Classes
nc: 2 #number of classes
names: ['face', 'face_mask'] #class names
```

(3) 修改口罩检测模型的训练超参数配置文件data/hyp.scratch.yaml，禁止图像镜像翻转，这样在训练时可以防止YOLOv5模型因自动进行图像上下左右翻转而导致模型精度差，如图3.38所示。

![](bbox=[0.125, 0.385, 0.88, 0.685])

图3.38 修改口罩检测模型的训练超参数配置文件

注意：在修改口罩检测模型的训练超参数配置文件时，不能修改“flipud: 0.0 #image flip up-down （probability）”和“fliplr: 0.0 #image flip left-right （probability）”，否则会使模型的训练效果变得极差，导致训练出的模型的检测置信度过低。

# 3.3.2.4 模型训练