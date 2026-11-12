
if __name__ == '__main__':
    RKNN_MODEL_PATH = r"./yolov5s-mask.rknn"
    SIZE = (640, 640)
    MASKS = [[0, 1, 2], [3, 4, 5], [6, 7, 8]]
    ANCHORS = [[10, 13], [16, 30], [33, 23], [30, 61], [62, 45], [59, 119], [116, 90],
        [156, 198], [373, 326]]

    CLASSES = ('face','face_mask')
    #初始化
    detector = Yolov5_Rknn_Detection(RKNN_MODEL_PATH, SIZE, MASKS, ANCHORS,
                    CLASSES)

    #读取测试图像
    img = cv2.imread("./test.jpg")
    respond= detector.predict_resize(img)
    print(respond)

## 3.7.2 开发步骤与验证

### 3.7.2.1 项目部署

- 1) 硬件部署

详见2.1.2.1节。

- 2) 工程部署

  - (1) 运行MobaXterm工具，通过SSH登录到边缘计算网关。

  - (2) 在SSH终端中执行以下命令，创建项目工程目录。

  ```
  $ mkdir -p ~/aiedge-exp
  ```

  - (3) 通过SSH将本项目工程代码上传到~/aicam-exp目录下，并采用unzip命令进行解压缩。

  ```
  $ unzip mask_detection_interface_ncnn.zip
  $ unzip mask_detection_interface_rknn.zip
  ```

### 3.7.2.2 NCNN框架模型接口开发验证

### 1) 参数配置

- (1) 打开mask_detection_interface_ncnn/cpp/mask_detection.cpp 文件，修改198、199行的代码，将需要加载的bin和param文件名称修改为转换好的NCNN框架模型文件名称。

  ```
  #修改的内容
  std::string mask_det_param = model_path + "/yolov5s-mask.ncnn.param";
  std::string mask_det_bin = model_path + "/yolov5s-mask.ncnn.bin";
  ```

- (2) 若模型类别不同，则需要修改 mask_detection_interface_ncnn/cpp/mask_detection.cpp文件中的其他类别名称（对应于程序代码的211行），即：

  ```
  static const char* class_names[] = {"face","face_mask"};
  ```

### 2) 编译SO库文件

通过cmake工具直接进行编译，如果已经存在build目录，则需要删除该目录后重新创建build目录，否则会无法生成so文件（这是因为无法替换已经存在的so文件）。编译完成后生成的so文件为 build/maskdet.cpython-38-aarch64-linux-gnu.so，将其复制到 build的上一级目录。

```
$ cd ../aiedge-exp/mask_detection_interface_ncnn
$ mkdir build && cd build
$ cmake ..
$ make -j4
Scanning dependencies of target maskdet
[ 50%] Building CXX object CMakeFiles/maskdet.dir/cpp/mask_detection.cpp.o
[100%] Linking CXX shared module maskdet.cpython-38-aarch64-linux-gnu.so
[100%] Built target maskdet
$ cp maskdet.cpython-38-aarch64-linux-gnu.so ../
```

### 3) 接口测试

- (1) 运行MobaXterm工具，通过SSH登录到GW3588边缘计算网关。

- (2) 在SSH终端输入以下命令，测试口罩检测模型。

```
$ cd ~/aiedge-exp/mask_detection_interface_ncnn
$ python3 mask_detection.py
{
    "code": 200,
    "msg": "SUCCESS",
    "result": {
        "obj_list": [
            {
                "location": {
                    "height": 317.11355590820312,
                    "left": 390.5355224609375,
                    "top": 112.06796264648438,
                    "width": 209.1123046875
                },
                "name": "face_mask",
                "score": 0.902835309050546265
            }],
        "obj_num": 1
    },
    "time": 88.098876953125
}
```

从测试结果可以看出，mask_detection.py加载了maskdet.cpython-38-aarch64-linux-gnu.so文件，并对本地图像test.jpg进行了检测。口罩检测结果如图3.47所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_309_0.png)

图 3.47 口罩检测结果

### 3.7.2.3 RKNN框架模型测试

在GW3588边缘计算网关的SSH终端输入以下命令，测试口罩检测模型接口，程序将打开测试图像进行口罩检测并将返回检测结果。

```
$ cd ~/aiedge-exp/mask_detection_interface_rknn/
$ python3 yolov5_rknn_detection.py
{'code': 200, 'msg': 'SUCCESS', 'result': {'obj_list': [{'location': {'left': 449, 'top': 0,
'width': 142, 'height': 84}, 'name': 'face', 'score': 0.8385043144226074},
{'location': {'left': 804, 'top': 181, 'width': 130, 'height': 175},
'name': 'face', 'score': 0.8091291785240173}, {'location': {'left': 348, 'top': 76,
'width': 148, 'height': 168}, 'name': 'face_mask', 'score': 0.7066819071769714}],
'obj_num': 2, 'time': 46.52}}
```

## 3.7.3 本节小结

本节以口罩检测模型为例，介绍了RKNN框架模型的接口设计与算法设计，以及基于YOLOv5的RKNN框架模型的接口设计过程。

## 3.7.4 思考与拓展

如何在SSH终端进行yolov5_rknn模型的推理？

## 3.8 YOLOv3模型的算法设计

在计算机和深度学习领域中，模型通常用来解决特定的问题，一般是数学方程、规则和计算步骤的组合。模型可以使得计算机执行某些任务或预测某些结果。以下是一些常见的模型算法类型：

- (1) 线性回归（Linear Regression）：用于构建输入特征与输出标签之间的线性关系，如预测房价等。

- (2) 逻辑回归（Logistic Regression）：用于解决二分类问题，通过将输入映射到一个概率范围内，从而预测某个样本属于哪一类。

- (3) 决策树（Decision Trees）：使用树状结构进行决策，每个节点代表一个特征，每个分支代表一个可能的决策，用于分类和回归。

- (4) 支持向量机（Support Vector Machines，SVM）：用于分类，通过在特征空间中找到一个最优超平面来进行分类。

- (5) 神经网络（Neural Networks）：由神经元和层组成的模型，用于处理复杂的非线性关系，广泛用于深度学习领域。

- (6) K近邻算法（K-Nearest Neighbors，KNN）：通过查找最接近输入样本的k个邻居来进行分类或回归。

- (7) 聚类算法（Clustering Algorithms）：用于将数据分为不同的簇，如K均值聚类（K-Means Clustering）。

- (8) 朴素贝叶斯（Naive Bayes）：在贝叶斯定理的基础上处理分类问题。

- (9) 随机森林（Random Forest）：由多个决策树组成的集成模型，用于提高预测的准确性和鲁棒性。

- (10) 强化学习算法（Reinforcement Learning Algorithms）：用于训练智能体进行决策，以最大化累积奖励，如Q学习、深度强化学习算法。

本节的知识点如下：

- 掌握基于AiCam平台的机器视觉应用框架。

- 基于YOLOv3和AiCam平台，掌握交通标志识别模型的算法设计过程。

## 3.8.1 原理分析与开发设计

### 3.8.1.1 基于AiCam平台的机器视觉应用框架

AiCam平台采用RESTful接口，以Flask服务的方式为应用层提供算法服务。根据实际的AI应用逻辑，AiCam平台的算法提供两种交互接口，分别用于处理实时推理和单次推理的应用场景。AiCam平台的开发框架见图2.4所示。

### 3.8.1.2 基于YOLOv3和AiCam平台的交通标志识别模型的算法设计

- 1) 模型推理

通过前面的项目我们完成了交通标志识别模型的接口库，通过Python程序可以调用模型进行推理：

```
#实时视频接口：@__app.route('/stream/<action>')
#image：摄像头实时传递过来的图像
#param_data：必须为 None
result = self.traffic_model.detect(image)
```

返回的结果如下：

```
//函数：TrafficDet.detect(image)
//参数：image 表示图像数据
//结果：JSON 格式的字符串
//code：200 表示执行成功，301 表示执行失败
{
    "code": 200,    //返回码
    "msg": "SUCCESS",    //返回消息
    "result": {    //返回结果
        "obj_list": [    //返回内容
            {
                "location": {    //目标坐标
                    "height": 400,
                    "left": 1215,
                    "top": 1052,
                    "width": 570
                },
                "name": "right",    //目标名称
                "score": 0.9994969367980957    //置信度
            }
        ],
        "obj_num": 1    //目标数量
    },
    "time": 33.180908203125    //推理时间（ms）
}
```

### 2) 接口设计

交通标志识别模型算法通过AiCam平台的实时推理接口对视频流内的图像进行实时计算和识别，摄像头采集到的视频图像通过交通标志识别模型算法进行实时计算，返回标注了识别框和识别内容的结果图像，并实时推流到应用端以视频的方式显示，同时将计算的结果数据（交通标志坐标、关键点、名称、推理时间、置信度等）返回到应用端，用于业务的处理。交通标志识别模型算法的详细逻辑如下：

- (1) 打开边缘计算网关的摄像头，获取实时的视频图像。

- (2) 将实时视频图像推送给算法接口的inference方法。

- (3) 调用算法接口的inference方法进行图像处理。

- (4) 算法接口的inference方法返回base64编码的结果图像、结果数据。

- (5) AiCam平台将返回的结果图像和结果数据拼接为text/event-stream数据流，供应用层调用。

- (6) 应用层通过EventSource接口获取实时推送的算法数据流（结果图像和结果数据）。

- (7) 应用层解析数据流，提取出结果图像和结果数据进行应用展示。

### 3) 交通标志识别模型算法设计

交通标志识别模型算法通过AiCam平台的实时推理接口对视频流内的图像进行实时计算和识别，算法文件
（traffic_detection\traffic_detection.py）的相关代码如下：

```
#######################################################################
#文件：traffic_detection.py
#说明：交通标志识别模型
#######################################################################
from PIL import Image,ImageDraw,ImageFont
import numpy as np
import cv2 as cv
import os
import json
import base64
c_dir = os.path.split(os.path.realpath(__file__))[0]

class TrafficDetection(object):
    def __init__(self, model_path="models/traffic_detection"):
        self.model_path = model_path
        self.traffic_model = TrafficDet()
        self.traffic_model.init(self.model_path)

    def image_to_base64(self, img):
        image = cv.imencode('.jpg', img, [cv.IMWRITE_JPEG_QUALITY, 60])[1]
        image_encode = base64.b64encode(image).decode()
        return image_encode

    def base64_to_image(self, b64):
        img = base64.b64decode(b64.encode('utf-8'))
        img = np.asarray(bytearray(img), dtype="uint8")
        img = cv.imdecode(img, cv.IMREAD_COLOR)
        return img

    def draw_pos(self, img, objs):
        img_rgb = cv.cvtColor(img, cv.COLOR_BGR2RGB)
        pilimg = Image.fromarray(img_rgb)
        #创建 ImageDraw 绘图类
        draw = ImageDraw.Draw(pilimg)
        #设置字体
        font_size = 20
        font_path = c_dir+"/font/wqy-microhei.ttc"
        font_hei = ImageFont.truetype(font_path, font_size, encoding="utf-8")

        for obj in objs:
            loc = obj["location"]
            draw.rectangle([loc["left"], loc["top"], loc["left"]+loc["width"],
                             loc["top"]+loc["height"]], outline='green',width=2)

            msg = obj["name"]+": %.2f"%obj["score"]
            draw.text((loc["left"], loc["top"]-font_size*1), msg, (0, 255, 0), font=font_hei)
        result = cv.cvtColor(np.array(pilimg), cv.COLOR_RGB2BGR)
        return result

    def inference(self, image, param_data):
        #code: 识别成功返回 200
        #msg: 相关提示信息
        #origin_image: 原始图像
        #result_image: 处理之后的图像
        #result_data: 结果数据
        return_result = {'code': 200, 'msg': None, 'origin_image': None, 'result_image': None,
                         'result_data': None}

        #实时视频接口： @app.route('/<stream>/<action>')
        #image: 摄像头实时传递过来的图像
        #param_data: 必须为 None
        result = self.traffic_model.detect(image)
        result = json.loads(result)
        if result["code"] == 200 and result["result"]["obj_num"] > 0:
            r_image = self.draw_pos(image, result["result"]["obj_list"])
        else:
            r_image = image
        return_result["code"] = result["code"]
        return_result["msg"] = result["msg"]
        return_result["result_image"] = self.image_to_base64(r_image)
        return_result["result_data"] = result["result"]
        return return_result
```
