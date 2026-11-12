```
./dataset/Annotations/16387579403672035.xml
......
```

在SSH终端输入以下命令运行kmeans.py，生成锚点的数据文件
yolo_anchors.txt：

```
$ cd ~\aiedge-exp\object_detection_darknet
$ conda activate py36_tf114_torch15_cpu_cv345 //Ubuntu 20.04 操作系统下需要切换环境
$ python3 kmeans.py
1 4 ./dataset/JPEGImages/1638757980448458.jpg 17
2 4 ./dataset/JPEGImages/1638757980448458.jpg 17
3 4 ./dataset/JPEGImages/1638757980448458.jpg 17
1 2 ./dataset/JPEGImages/16387579456090543.jpg 15
......
K anchors:
[43 51]
[62 79]
[86 95]
[104 109]
[125 132]
[163 175]]
Accuracy: 84.59%
```

从yolo_anchors.txt文件获取的锚点为（43，51）、（62，79）、（86，95）、（104，109）、（125，132）、（163，175）。

(5) 修改交通标志识别配置文件cfg/yolov3-tiny-traffic.cfg。根据交通标志的类别（classes=5）修改对应的卷积数[filters=（classes+5）×3=30]，搜索cfg/yolov3-tiny-traffic.cfg文件内的yolo关键字（有两处），修改filters、anchors、classes。修改后的交通标志识别模型的配置文件如图3.29所示。