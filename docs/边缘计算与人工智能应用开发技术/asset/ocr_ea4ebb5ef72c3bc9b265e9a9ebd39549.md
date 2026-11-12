```
[net]
# Testing
batch=16
subdivisions=1
# Training
# batch=64
# subdivisions=2
width=416
height=416
channels=3
momentum=0.9
decay=0.0005
angle=0
flip=0
saturation = 1.5
exposure = 1.5
hue=.1
```

图3.30 修改后的交通标志识别模型的配置文件（二）

至此，交通标志识别模型的参数文件修改完毕。

# 3.2.2.4 模型训练

通过Darknet框架可以完成交通标志识别模型的训练，可选择普通的计算机或者集成了支持模型训练GPU的计算机进行模型训练。

（1）在SSH终端输入以下命令进入工程目录，改变Darknet的执行权限。

```
$ cd ~/aiedge-exp/object_detection_darknet
$ chmod 755 darknet-*
```

（2）通过以下两种方式进行模型训练。

方式一：通过普通的计算机进行模型训练（至少需要300 h以上）。