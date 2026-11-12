
![](img/1e9ab73eb32544626458aa1c625efb7c_280_0.png)

## 图3.45 NCNN框架模型的推理过程

推理流程的描述如下：

- (1) 获取摄像头的图像数据。
- （2）图像预处理：对图像数据进行灰度化处理，并采用双线性插值等算法进行图像尺寸变换，最后进行图像数据的归一化处理。
- （3）加载NCNN框架模型文件：使用创建的Net神经网络对象，加载.param文件和.bin文件，其中，.param文件是神经网络结构说明，.bin文件是神经网络权重参数文件。
- （4）创建深层神经网络：调用NCNN框架接口，创建Net神经网络对象。
- （5）创建推理提取器（Extractor）：调用NCNN框架接口创建推理提取器，设置提取器线程数量和轻量化模式开关等。
- （6）获取推理结果：为提取器输入经过步骤（2）处理后的图像数据，输出推理结果，推理结果包括目标物体坐标、类别等数据。
- （7）展示推理结果：将推理结果绘制在原始图像上向客户展示，并返回推理结果数据。

## 3.6.1.2 交通标志识别模型的接口设计

NCNN框架是基于C/C++实现的，而AiCam平台的算法层采用Python语言，需要采用pybind11将NCNN框架模型的接口封装成so库，供Python算法调用。

AiCam平台对NCNN框架模型接口的调用与返回的数据做了标准化处理，返回的数据采用JSON格式，方便AiCam平台的算法层进行解析。NCNN框架模型接口函数如下（以交通标志识别模型为例）：

```cpp
PYBIND11_MODULE(trafficdet, m) {
  py::class_<TrafficDet>(m, "TrafficDet")
      .def(py::init<>())
      .def("init", &TrafficDet::init)
      .def("detect", &TrafficDet::detect_yolov3);
}
```

模型接口的返回如下：

```json
{
    "code" : 200,
    "msg" : "SUCCESS",
    "result" : {
        "obj_list" : [
            {
                "location" : {
                    "height" : 58,
                    "left" : 215,
                    "top" : 137,
                    "width" : 45
                },
                "mark" : [
                    {
                        "x" : 227,
                        "y" : 160
                    },
                    {
                        "x" : 247,
                        "y" : 157
                    },
                    {
                        "x" : 239,
                        "y" : 169
                    },
                    {
                        "x" : 231,
                        "y" : 180
                    },
                    {
                        "x" : 249,
                        "y" : 177
                    }
                ],
                "score" : 0.99148327112197876
            }
        ],
        "name" : "one",
        "obj_num" : 1
    },
    "time" : 17.5849609375
}
```

## 3.6.1.3 交通标志识别模型的算法设计

- 1) 模型接口算法

交通标志识别模型接口对模型的调用和推理相关方法进行了封装，并编译成so库，供AiCam平台的算法层调用。算法文件
（traffic_detection_interface\cpp\traffic_detection.cpp）的相关代码如下：

```cpp
###############################################################################
#文件： traffic_detection.cpp
#说明： 交通标志识别模型接口
###############################################################################
#include "traffic_detection.h"
//数据信息列表
std::vector<std::string> data_info {};

//将 Numpy 三通道 UINT8 型数据转换为 OpenCV 矩阵数据
cv::Mat numpy_uint8_3c_to_cv_mat(py::array_t<unsigned char>& input) {
    if (input.ndim() != 3)
        throw std::runtime_error("3-channel image must be 3 dims ");
    py::buffer_info buf = input.request();
    cv::Mat mat(buf.shape[0], buf.shape[1], CV_8UC3, (unsigned char*)buf.ptr);

    return mat;
}

//将 OpenCV 矩阵数据转换为 Numpy 三通道 UINT8 型数据
py::array_t<unsigned char> cv_mat_uint8_3c_to_numpy(cv::Mat& input) {
    py::array_t<unsigned char> dst = py::array_t<unsigned char>({ input.rows,input.cols,3 }, input.data);
    return dst;
}

//创建交通标志识别对象
TrafficDet::~TrafficDet(){
    yolov3.clear();
}
//初始化交通标志识别对象
int TrafficDet::init(std::string model_path)
{
    //模型参数文件
    std::string mask_det_param = model_path + "/yolov3-tiny-traffic-opt.param";
    //模型权重文件
    std::string mask_det_bin = model_path + "/yolov3-tiny-traffic-opt.bin";
    //加载模型参数
    yolov3.load_param(mask_det_param.data());
    //加载模型权重
    yolov3.load_model(mask_det_bin.data());
}

std::string TrafficDet::detect_yolov3(py::array_t<unsigned char>& pyimage)
{
Json::Value root;
double dStart = ncnn::get_current_time();

Json::Value obj_list, result;
//数据格式转换
cv::Mat input_image = numpy_uint8_3c_to_cv_mat(pyimage);
const int target_size = 416;

int img_w = input_image.cols;
int img_h = input_image.rows;
//图像数据尺寸裁剪
ncnn::Mat in = ncnn::Mat::from_pixels_resize(input_image.data, ncnn::Mat::PIXEL_BGR2RGB,
 img_w, img_h, target_size, target_size);

//图像数据归一化处理
const float mean_vals[3] = {127.5f, 127.5f, 127.5f};
const float norm_vals[3] = {0.007843f, 0.007843f, 0.007843f};
in.substract_mean_normalize(mean_vals, norm_vals);
//创建推理提取器
ncnn::Extractor ex = yolov3.create_extractor();
//输入图像数据
ex.input("data", in);
//获取推理结果
ncnn::Mat out;
//ex.extract("detection_out", out);
ex.extract("output", out);

static const char* class_names[] = {"background","red","green","left","straight","right"};
//遍历推理结果，组装 JSON 对象
for (int i = 0; i < out.h; i++)
{
const float* values = out.row(i);

Json::Value obj;
int idx = (int)values[0];
//设置目标名称
if (idx >= 0 && idx < sizeof(class_names) / sizeof(class_names[0])) {
    obj["name"] = class_names[idx];
} else obj["name"] = "unknown";
//设置置信度的分值
obj["score"] = values[1];
//设置目标位置坐标
int x = (int)(values[2] * img_w);
int y = (int)(values[3] * img_h);
int w = (int)(values[4] * img_w - x);
int h = (int)(values[5] * img_h - y);
Json::Value location;
location["left"] = x;
location["top"] = y;
location["width"] = w;
location["height"] = h;
obj["location"] = location;
obj_list.append(obj);
}
result["obj_num"] = obj_list.size();
result["obj_list"] = obj_list;
root["result"] = result;
root["code"] = 200;
root["msg"] = "SUCCESS";
root["time"] = ncnn::get_current_time()-dStart;
std::string r = root.toStyledString();
return r;
}

//使用 pybind11 加载交通标志识别模型
PYBIND11_MODULE(trafficdet, m) {
py::class_<TrafficDet>(m, "TrafficDet")
.def(py::init<>()) 
.def("init", &TrafficDet::init)
.def("detect", &TrafficDet::detect_yolov3);
}
```

### 2） 模型接口测试程序

交通标志识别模型接口测试程序

(traffic_detection_interface\traffic_detection.py) 的代码如下：

```python
############################################################################
#文件： traffic_detection.py
#说明： 交通标志识别模型接口测试程序
############################################################################
import numpy as np
import cv2 as cv

import os
import json
c_dir = os.path.split(os.path.realpath(__file__))[0]
import trafficdet

#单元测试，如果处理类中引用了文件，则在单元测试中要修改文件路径
if __name__=='__main__':
    #读取测试图像
    img = cv.imread("./test.jpg")
    #创建图像处理对象
    img_object=trafficdet.TrafficDet()
    img_object.init(c_dir+'/models/traffic_detection')
    result=img_object.detect(img)
    print(result)
```

## 3.6.2 开发步骤与验证

### 3.6.2.1 项目部署

1) 硬件部署

详见2.1.2.1节。

2) 工程部署

- (1) 运行MobaXterm工具，通过SSH登录到边缘计算网关。
- (2) 在SSH终端执行以下命令，创建项目工程目录。

```
$ mkdir -p ~/aiedge-exp
```

- (3) 通过SSH将本项目工程代码上传到~/aicam-exp目录下，并采用unzip命令进行解压缩。

### 3.6.2.2 模型接口编译

在SSH终端输入以下命令，编译模型接口。

```
$ cd ~/aiedge-exp/traffic_detection_interface
$ mkdir -p build
$ cd build
$ cmake ..
......
......
-- Build files have been written to: /home/zonesion/aiedge-exp/traffic_detection_interface/build
$ make
Scanning dependencies of target trafficdet
[ 50%] Building CXX object CMakeFiles/trafficdet.dir/cpp/traffic_detection.cpp.o
[100%] Linking CXX shared module trafficdet.cpython-35m-x86_64-linux-gnu.so
[100%] Built target trafficdet
```

编译完成后会在build目录中生成trafficdet.cpython-35m-x86_64-linux-gnu.so（模型接口库文件）。注意：在ARM平台中的文件为
trafficdet.cpython-35m-aarch64-linux-gnu.so。

### 3.6.2.3 模型接口测试

在SSH终端输入以下命令，运行模型接口。

```
$ cd ~/aiedge-exp/traffic_detection_interface
$ cp build/*.so ./
$ conda activate py3_tfl14_torch15_cpu_cv345    //Ubuntu 20.04 操作系统下需要切换环境
$ python3 traffic_detection.py
{
    "code" : 200,
    "msg" : "SUCCESS",
    "result" : {
        "obj_list" : [
            {
                "location" : {
                    "height" : 93,
                    "left" : 425,
                    "top" : 158,
                    "width" : 107
                },
                "name" : "right",
                "score" : 0.90108931064605713
            },
            {
                "location" : {
                    "height" : 115,
                    "left" : 149,
                    "top" : 143,
                    "width" : 109
                },
                "name" : "left",
                "score" : 0.89343971014022827
            },
            {
                "location" : {
                    "height" : 116,
                    "left" : 273,
                    "top" : 145,
                    "width" : 124
                },
                "name" : "straight",
                "score" : 0.51184618473052979
            },
        ],
        "obj_num" : 3
    },
    "time" : 61.909912109375
}
```
