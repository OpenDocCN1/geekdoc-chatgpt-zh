
```python
last_nearest = np.zeros(box_number)
np.random.seed()
clusters = boxes[np.random.choice(
    box_number, k, replace=False)] #init k clusters
while True:

    distances = 1 - self.iou(boxes, clusters)

    current_nearest = np.argmin(distances, axis=1)
    if (last_nearest == current_nearest).all():
        break   #clusters won't change
    for cluster in range(k):
        clusters[cluster] = dist(   #update clusters
            boxes[current_nearest == cluster], axis=0)

    last_nearest = current_nearest

return clusters

def result2txt(self, data):
    f = open("yolo_anchors.txt", 'w')
    row = np.shape(data)[0]
    for i in range(row):
        if i == 0:
            x_y = "%d,%d" % (data[i][0], data[i][1])
        else:
            x_y = ",%d,%d" % (data[i][0], data[i][1])
        f.write(x_y)
    f.close()

def txt2boxes(self):
    f = open(self.filename, 'r')
    dataSet = []
    for line in f:
        infos = line.strip().split(" ")
        length = len(infos)
        for i in range(1, length):
            print(i,length,infos[0],len(infos[-1]))
            width = int(float(infos[i].split(",")[2])) - int(float(infos[i].split(",")[0]))
            height = int(float(infos[i].split(",")[3])) - int(float(infos[i].split(",")[1]))
            dataSet.append([width, height])
    result = np.array(dataSet)
    f.close()
    return result

def txt2clusters(self):
    all_boxes = self.txt2boxes()
    result = self.kmeans(all_boxes, k=self.cluster_number)
    result = result[np.lexsort(result.T[0, None])]
    self.result2txt(result)
    print("K anchors:\n {}".format(result))
    print("Accuracy: {:.2f}%".format(
        self.avg_iou(all_boxes, result) * 100))

if __name__ == "__main__":
    cluster_number = 6
    filename = "train_anchors.txt"
    kmeans = YOLO_Kmeans(cluster_number, filename)
    kmeans.txt2clusters()
```

## 3.2.2 开发步骤与验证

### 3.2.2.1 项目部署

- 1) 硬件部署

详见2.1.2.1节。

- 2) 工程部署

  - (1) 运行MobaXterm工具，通过SSH登录深度学习服务器。

  - (2) 在SSH终端执行以下命令，创建项目工程目录。

    ```
    $ mkdir -p ~/aiedge-exp
    ```

  - (3) 将本项目的工程代码上传到~/aiedge-exp目录下，并采用unzip命令进行解压缩。

    ```
    $ cd ~/aiedge-exp
    $ unzip object_detection_darknet.zip
    ```

### 3.2.2.2 数据处理

通过voc2yolo工具将voc格式的数据转换为yolo格式的数据，并分开训练集和验证集。

- (1) 将制作好的数据集traffic_dataset_v1.3.zip通过SSH上传到~/aiedge-exp目录下。

说明：模型的精度依赖于数据集，本项目采用已标注好的交通标志数据集进行模型训练。

- (2) 解压缩数据集，并将数据集复制到darknet工程目录下：

    ```
    $ cd ~/aiedge-exp
    $ unzip traffic_dataset_v1.3.zip
    $ cd object_detection_darknet/
    $ cp -a ../traffic_dataset_v1.3/dataset ./
    ```

- (3) 根据交通标志的类别修改voc2yolo.py文件内的目标类别，通过SSH将修改后的文件上传到边缘计算网关。修改后的voc2yolo.py文件内容如下：

    ```
    classes = ['red','green','left','straight','right']
    ```

- (4) 在SSH终端输入以下命令进行数据格式的转换和数据集的切分，在dataset文件夹中生成yolo格式的数据集labels，以及训练集object_train.txt和验证集object_val.txt。

    ```
    $ cd ~/aiedge-exp/object_detection_darknet
    $ conda activate py36_tf114_torch15_cpu_cv345    //Ubuntu 20.04 操作系统下需要切换环境
    $ python3 voc2yolo.py
    files: ['1638757925381593', '16387579716174064', '1640220985345892', '1640220986479758',
    '16387579476261072', '16387579697302487', '1640221005206378', '16387579900259972', '1638757972922645',
    '159238212456671337387']
    ```

### 3.2.2.3 参数配置

创建交通标志识别模型的配置文件。

- (1) 在dataset文件夹下创建交通标志数据集的标签文件traffic.names，内容如下：

    ```
    red
    green
    left
    straight
    right
    ```

- (2) 在SSH终端输入以下命令复制yolov3-tiny-pill.cfg/yolov3-tiny-pill.data文件，并命名为yolov3-tiny-traffic.cfg/yolov3-tiny-traffic.data。

    ```
    $ cd ~/aiedge-exp/object_detection_darknet
    $ cp cfg/yolov3-tiny-pill.cfg cfg/yolov3-tiny-traffic.cfg
    $ cp cfg/yolov3-tiny-pill.data cfg/yolov3-tiny-traffic.data
    $ ls cfg/
    yolov3-tiny-pill.cfg yolov3-tiny-pill.data yolov3-tiny-traffic.cfg yolov3-tiny-traffic.data
    ```

- (3) 根据交通标志的类别可知classes为5，修改yolov3-tiny-traffic.data文件内相关参数：

    ```
    classes= 5
    train = ./dataset/object_train.txt
    valid = ./dataset/object_val.txt
    names = ./dataset/traffic.names
    backup = ./backup/
    ```

- (4) 生成锚点。根据交通标志的类别修改train_anchors.py文件中与classes相关的参数，通过SSH将修改好的文件上传到边缘计算网关。修改后的train_anchors.py文件如下：

    ```
    classes = ['red','green','left','straight','right']
    ```

在SSH终端输入以下命令运行train_anchors.py，生成用于锚点聚类的数据文件train_anchors.txt：

    ```
    $ cd ~/aiedge-exp/object_detection_darknet
    $ conda activate py36_tf114_torch15_cpu_cv345 //Ubuntu 20.04 操作系统下需要切换环境
    $ python3 train_anchors.py
    ./dataset/Annotations/1638757980448458.xml
    ./dataset/Annotations/16387579456090543.xml
    ./dataset/Annotations/16387579667065635.xml
    ./dataset/Annotations/164022096542211.xml
    ./dataset/Annotations/16387579403672035.xml
    ......
    ```

在SSH终端输入以下命令运行kmeans.py，生成锚点的数据文件yolo_anchors.txt：

    ```
    $ cd ~/aiedge-exp/object_detection_darknet
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
    [163 175]
    Accuracy: 84.59%
    ```

从yolo_anchors.txt文件获取的锚点为（43，51）、（62，79）、（86，95）、（104，109）、（125, 132）、（163, 175）。

- (5) 修改交通标志识别配置文件cfg/yolov3-tiny-traffic.cfg。根据交通标志的类别（classes=5）修改对应的卷积数[filters=（classes+5）×3=30]，搜索cfg/yolov3-tiny-traffic.cfg文件内的yolo关键字（有两处），修改filters、anchors、classes。修改后的交通标志识别模型的配置文件如图3.29所示。

## 图3.29 修改后的交通标志识别模型的配置文件（一）

由于左转和右转交通标志是垂直镜像的关系，所以配置文件内要增加选项flip=0，如图3.30所示。

## 图3.30 修改后的交通标志识别模型的配置文件（二）

至此，交通标志识别模型的参数文件修改完毕。

### 3.2.2.4 模型训练

通过Darknet框架可以完成交通标志识别模型的训练，可选择普通的计算机或者集成了支持模型训练GPU的计算机进行模型训练。

- (1) 在SSH终端输入以下命令进入工程目录，改变Darknet的执行权限。

    ```
    $ cd ~/aiedge-exp/object_detection_darknet
    $ chmod 755 darknet-*
    ```

- (2) 通过以下两种方式进行模型训练。

方式一：通过普通的计算机进行模型训练（至少需要300 h以上）。

    ```
    $ cd ~/aiedge-exp/object_detection_darknet
    $ conda activate py36_tf114_torch15_cpu_cv345      //Ubuntu 20.04 操作系统下需要切换环境
    $ /darknet-cpu detector train cfg/yolov3-tiny-traffic.data cfg/yolov3-tiny-traffic.cfg yolov3-tiny.conv.15 15
    GPU isn't used
    OpenCV isn't used - data augmentation will be slow
    yolov3-tiny-traffic
    mini_batch = 16, batch = 16, time_steps = 1, train = 1
    ```

| layer | filters | size/stride(dil) | input | output |
|---|---|---|---|---|
| 0 conv | 16 | 3 x 3 / 1 | 416 x 416 x 3 -> | 416 x 416 x 16 0.150 BF |
| 1 max | | 2x 2 / 2 | 416 x 416 x 16 -> | 208 x 208 x 16 0.003 BF |
| 2 conv | 32 | 3 x 3 / 1 | 208 x 208 x 16 -> | 208 x 208 x 32 0.399 BF |
| 3 max | | 2x 2 / 2 | 208 x 208 x 32 -> | 104 x 104 x 32 0.001 BF |
| 4 conv | 64 | 3 x 3 / 1 | 104 x 104 x 32 -> | 104 x 104 x 64 0.399 BF |
| 5 max | | 2x 2 / 2 | 104 x 104 x 64 -> | 52 x 52 x 64 0.001 BF |
| 6 conv | 128 | 3 x 3 / 1 | 52 x 52 x 64 -> | 52 x 52 x 128 0.399 BF |
| 7 max | | 2x 2 / 2 | 52 x 52 x 128 -> | 26 x 26 x 128 0.000 BF |
| 8 conv | 256 | 3 x 3 / 1 | 26 x 26 x 128 -> | 26 x 26 x 256 0.399 BF |
| 9 max | | 2x 2 / 2 | 26 x 26 x 256 -> | 13 x 13 x 256 0.000 BF |
| 10 conv | 512 | 3 x 3 / 1 | 13 x 13 x 256 -> | 13 x 13 x 512 0.399 BF |
| 11 max | | 2x 2 / 1 | 13 x 13 x 512 -> | 13 x 13 x 512 0.000 BF |
| 12 conv | 1024 | 3 x 3 / 1 | 13 x 13 x 512 -> | 13 x 13 x 1024 1.595 BF |
| 13 conv | 256 | 1 x 1 / 1 | 13 x 13 x 1024 -> | 13 x 13 x 256 0.089 BF |
| 14 conv | 512 | 3 x 3 / 1 | 13 x 13 x 256 -> | 13 x 13 x 512 0.399 BF |
| 15 conv | 30 | 1 x 1 / 1 | 13 x 13 x 512 -> | 13 x 13 x 30 0.005 BF |
| 16 yolo | | | | |
| 17 route | 13 | | 13 x 13 x 256 | |
| 18 conv | 128 | 1 x 1 / 1 | 13 x 13 x 256 -> | 13 x 13 x 128 0.011 BF |
| 19 upsample | | 2x | 13 x 13 x 128 -> | 26 x 26 x 128 |
| 20 route | 19 8 | | 26 x 26 x 384 | |
| 21 conv | 256 | 3 x 3 / 1 | 26 x 26 x 384 -> | 26 x 26 x 256 1.196 BF |
| 22 conv | 30 | 1 x 1 / 1 | 26 x 26 x 256 -> | 26 x 26 x 30 0.010 BF |
| 23 yolo | | | | |

```
[yolo] params: iou loss: mse (2), iou_norm: 0.75, obj_norm: 1.00, cls_norm: 1.00, delta_norm: 1.00,
    scale_x_y: 1.00
```

```
[yolo] params: iou loss: mse (2), iou_norm: 0.75, obj_norm: 1.00, cls_norm: 1.00, delta_norm: 1.00,
    scale_x_y: 1.00
```

Total BFLOPS 5.454
avg_outputs = 325691
Loading weights from yolov3-tiny.conv.15...
seen 64, trained: 0 K-images (0 Kilo-batches_64)
Done! Loaded 15 layers from weights-file
Learning Rate: 0.001, Momentum: 0.9, Decay: 0.0005
    Detection layer: 16 - type = 28
    Detection layer: 23 - type = 28
Resizing, random_coef = 1.40

608 x 608
Create 64 permanent cpu-threads
Loaded: 0.069905 seconds

v3 (mse loss, Normalizer: (iou: 0.75, obj: 1.00, cls: 1.00) Region 16 Avg (IOU: 0.462607), count: 19,
    class_loss = 377.545441, iou_loss = 1.748474, total_loss = 379.293915

v3 (mse loss, Normalizer: (iou: 0.75, obj: 1.00, cls: 1.00) Region 23 Avg (IOU: 0.409510), count: 23,
    class_loss = 1121.026611, iou_loss = 3.438599, total_loss = 1124.465210,
    total_bbox = 42, rewritten_bbox = 0.000000 %

1: 751.879578, 751.879578 avg loss, 0.000000 rate, 103.777236 seconds, 16 images, -1.000000 hours left
Loaded: 0.000072 seconds

v3 (mse loss, Normalizer: (iou: 0.75, obj: 1.00, cls: 1.00) Region 16 Avg (IOU: 0.341734), count: 25,
    class_loss = 376.413269, iou_loss = 4.249542, total_loss = 380.662811
v3 (mse loss, Normalizer: (iou: 0.75, obj: 1.00, cls: 1.00) Region 23 Avg (IOU: 0.372197), count: 22,
    class_loss = 1121.051880, iou_loss = 4.764771, total_loss = 1125.816650

......

25009: 0.062177, 0.086965 avg loss, 0.000010 rate, 0.128478 seconds, 400144 images, 0.002451 hours left
Loaded: 0.000057 seconds
v3 (mse loss, Normalizer: (iou: 0.75, obj: 1.00, cls: 1.00) Region 16 Avg (IOU: 0.937889), count: 26,
    class_loss = 0.053363, iou_loss = 0.020515, total_loss = 0.073878
v3 (mse loss, Normalizer: (iou: 0.75, obj: 1.00, cls: 1.00) Region 23 Avg (IOU: 0.914718), count: 20,
