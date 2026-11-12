```
#Finish
f = [str(x) for x in f if x] #filter out " and None
if any(f):
    LOGGER.info(f'\nExport complete ({time.time() - t:.2f}s'
        f"\nResults saved to {colorstr('bold', file.parent.resolve())}"
        f"\nDetect: python detect.py --weights {f[1]}"
        f"\nPyTorch Hub: model = torch.hub.load('ultralytics/yolov5', 'custom', '{f[1]}')")"
        f"\nValidate: python val.py --weights {f[1]}"
        f"\nVisualize: https://netron.app")
return f #return list of exported files/dirs.
```

# 3.3.2 开发步骤与验证

# 3.3.2.1 项目部署

本项目基于YOLOv5目标检测算法进行口罩检测模型的训练，项目工程包object_detection_yolov5-6.1有NCNN（官方原版）和RKNN（瑞芯优化版本）版本，训练的步骤完全一样，下面以NCNN版本的项目工程包为例进行介绍。

+   1）硬件部署

详见2.1.2.1节。

+   2）工程部署

+   (1）运行MobaXterm工具，通过SSH登录到深度学习服务器。

+   (2）在SSH终端执行以下命令，创建项目工程目录。

```
$ mkdir -p ~/aiedge-exp
```

+   (3）将本项目工程代码上传到~/aicam-exp目录下，并采用unzip命令按照3.2.2.1节的方法进行解压缩。

# 3.3.2.2 数据处理

通过voc2yolo工具将VOC格式的数据集转换为yolo格式的数据集，并切分训练集和验证集。voc2yolo工具位于object_detection_yolov5-6.1