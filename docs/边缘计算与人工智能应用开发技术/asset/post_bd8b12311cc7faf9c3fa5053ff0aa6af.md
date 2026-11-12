
| 类别 | 接口名称 | 接口描述 |
|---|---|---|
| 人脸识别 | baidu_face_recognition | 人脸注册及识别 |
| 人体识别 | baidu_body_attr | 人体检测与属性识别算法 |
| 车辆检测 | baidu_vehicle_detect | 车辆属性及检测算法 |
| 手势识别 | baidu_gesture_recognition | 手势识别算法 |
| 数字识别 | baidu_numbers_detect | 数字识别算法 |
| 文字识别 | baidu_general_characters_recognition | 通用文字识别算法 |
| 语音识别 | baidu_speech_recognition | 百度语音识别（标准版）应用 |
| 语音合成 | baidu_speech_synthesis | 百度语音合成服务应用 |

## 3）AiCam平台的部分案例截图

AiCam平台的部分案例截图如图2.7到图2.11所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_36_0.png)

### ▲图2.7 基础算法案例截图

![](img/1e9ab73eb32544626458aa1c625efb7c_37_0.png)

### ▲图2.8 基础应用案例截图

![](img/1e9ab73eb32544626458aa1c625efb7c_37_1.png)

### ▲图2.9 深度学习案例截图

![](img/1e9ab73eb32544626458aa1c625efb7c_37_2.png)

### ▲图2.10 云边应用案例截图

![](img/1e9ab73eb32544626458aa1c625efb7c_38_0.png)

### ▲图2.11 边缘智能案例截图

## 2.1.1.4 边缘计算的硬件设计平台

本书采用的是GW3588边缘计算网关。为了深化无线节点在无线传感网络中的使用，本书中的部分项目需要使用传感器和控制设备，涉及xLab开发平台，该平台设计了丰富的硬件设备，包括采集类、控制类、安防类、显示类、识别类等开发平台。

### 1) GW3588边缘计算网关

GW3588边缘计算网关采用全新的商业产品级一体机外观设计，以及搭载AI嵌入式边缘计算处理器的RK3399开发板。RK3399采用了4核Cortex-A76和4核Cortex-A55的微处理器，4核的Mali-G610的GPU、6 TOP算力的神经网络处理器（Neural Processing Unit, NPU）、16 GB的RAM和128 GB的ROM，15.6"的高清电容屏（LCD），可运行Ubuntu、Android等操作系统。

GW3588边缘计算网关不仅提供了丰富的外设接口（如图2.12所示），可方便用户开发调试；还提供了丰富的扩展模块，可完成人工智能机器视觉、语音语言、边缘计算、综合项目等的开发任务。

![](img/1e9ab73eb32544626458aa1c625efb7c_39_0.png)

### 图2.12 GW3588边缘计算网关的外设接口

### 2) 采集类开发平台（Sensor-A）

采集类开发平台包括温湿度传感器、光照度传感器、空气质量传感器、气压海拔传感器、三轴加速度传感器、距离传感器、继电器、语音识别传感器等，如图2.13所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_40_0.png)

### 图2.13 采集类开发平台

- 两路RJ45工业接口，包含I/O、DC 3.3 V、DC 5 V、UART、RS-485、两路继电器输出等功能，提供两路3.3 V、5 V电源输出。
- 温湿度传感器的型号为HTU21D，采用数字信号输出和I2C通信接口，测量范围为-40～125℃，以及5%RH～95%RH。
- 光照度传感器的型号为BH1750，采用数字信号输出和I2C通信接口，对应广泛的输入光范围，相当于1～65535 lx。
- 空气质量传感器的型号为MP503，采用模拟信号输出，可以检测气体酒精、烟雾、异丁烷、甲醛，检测浓度为10～1000 ppm（酒精）。
- 气压海拔传感器的型号为FBM320，采用数字信号输出和I2C通信接口，测量范围为300～1100 hPa。
- 三轴加速度传感器的型号为LIS3DH，采用数字信号输出和I2C通信接口，量程可设置为±2g、±4g、±8g、±16g（g为重力加速度），16位数据输出。
- 距离传感器的型号为GP2D12，采用模拟信号输出，测量范围为10～80 cm，更新频率为40 ms。
- 采用继电器控制，输出节点有两路继电器接口，支持5V电源开关控制。
- 语音识别传感器的型号为LD3320，支持非特定人识别，具有50条识别容量，采用串口通信。

### 3）控制类开发平台（Sensor-B）

控制类开发平台包括：风扇、步进电机、蜂鸣器、LED、RGB灯、继电器接口，如图2.14所示。

- 两路RJ45工业接口，包含IO、DC 3.3 V、DC 5 V、UART、RS-485、两路继电器输出等功能，提供两路3.3 V、5 V电源输出。
- 风扇为小型风扇，采用低电平驱动。
- 步进电机为小型42步进电机，驱动芯片为A3967SLB，逻辑电源的电压范围为3.0～5.5 V。
- 使用小型蜂鸣器，采用低电平驱动。

![](img/1e9ab73eb32544626458aa1c625efb7c_42_0.png)

### 图2.14 控制类开发平台

- 两路高亮LED灯，采用低电平驱动。
- RGB灯采用低电平驱动，可组合出任何颜色。
- 采用继电器控制，输出节点有两路继电器接口，支持5V电源开关控制。

### 4）安防类开发平台（Sensor-C）

安防类开发平台包括：火焰传感器、光栅传感器、人体红外传感器、燃气传感器、触摸传感器、振动传感器、霍尔传感器、继电器接口、语音合成传感器等，如图2.15所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_43_0.png)

### 图2.15 安防类开发平台

两路RJ45工业接口，包含IO、DC 3.3 V、DC 5 V、UART、RS-485、两路继电器输出等功能，提供两路3.3 V、5 V红外光电电源输出。

火焰传感器采用5 mm的探头，可检测火焰或波长为760～1100 nm的红外光，探测温度为600℃左右，采用数字开关量输出。

光栅传感器的槽式光栅槽宽为10 mm，工作电压为5 V，采用数字开关量信号输出。

人体红外传感器的型号为AS312，电源电压为3 V，感应距离为12 m，采用数字开关量信号输出。

燃气传感器的型号为MP-4，采用模拟信号输出，传感器加热电压为5 V，供电电压为5 V，可测量天然气、甲烷、瓦斯气、沼气等。

触摸传感器的型号为SOT23-6，采用数字开关量信号输出，检测到触摸时，输出电平翻转。

振动传感器在低电平时有效，采用数字开关量信号输出。

霍尔传感器的型号为AH3144，电源电压为5 V，采用数字开关量输出，工作频率宽（0~100 kHz）。

采用继电器控制，输出节点有两路继电器接口，支持5V电源开关控制。

语音合成传感器的型号为SYN6288，采用串口通信，支持GB2312、GBK、UNICODE等编码，可设置音量、背景音乐等。

### 5）显示类开发平台（Sensor-D）

显示类开发平台包括：OLED显示屏、数码管、五向开关和传感器端子，如图2.16所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_44_0.png)

### 图2.16 显示类开发平台

两路RJ45工业接口，包含IO、DC 3.3 V、DC 5 V、UART、RS-485、两路继电器输出等功能，提供两路3.3 V、5 V电源输出。

硬件采用分区设计，丝印框图清晰易懂，包含传感器编号，模块采用亚克力防护。

- ➤ OLED显示屏：分辨率为128×64、尺寸为0.96"，采用I2C通信接口。
- ➤ 数码管：采用4位共阴极数码管，驱动芯片的型号为ZLG7290，采用I2C通信接口。
- ➤ 五向开关：五向按键，驱动芯片的型号为ZLG7290，采用I2C通信接口。

### 6) 125 kHz & 13.56 MHz二合一开发平台（Sensor-EL）

125 kHz & 13.56 MHz二合一开发平台包括：继电器接口、蜂鸣器、OLED显示屏、RFID、传感器端子、USB调试接口、功能跳线，如图2.17所示。

- ➤ 两路RJ45工业接口，包含IO、DC 3.3 V、DC 5 V、UART、RS-485、两路继电器输出等功能，提供两路3.3 V、5 V电源输出。
- ➤ 硬件采用分区设计，丝印框图清晰易懂，包含传感器编号，模块采用亚克力防护。
- ➤ RFID：采用125 kHz & 13.56 MHz的射频传感器，UART接口（TTL电平），支持ISO/IEC 14443 A/MIFARE、NTAG、MF1xxS20、MF1xxS70、MF1xxS50、EM4100、T5577等射频卡。
- ➤ OLED显示屏：分辨率为128×64、尺寸为0.96"，采用I2C通信接口。
- ➤ 支持USB供电、USB转串口，可连接计算机的USB接口。

![](img/1e9ab73eb32544626458aa1c625efb7c_46_0.png)

### 图2.17  125kHz & 13.56MHz二合一开发平台

### 7) 900 MHz & ETC开发平台（Sensor-EH）

900 MHz & ETC开发平台包括：RFID、传感器端子、ETC栏杆、复位按钮、USB调试接口、功能跳线，如图2.18所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_46_1.png)

### 图2.18 900MHz & ETC开发平台

- ◎ 两路RJ45工业接口，包含IO、DC 3.3 V、DC 5 V、UART、RS-485、两路继电器输出等功能，提供两路3.3 V、5 V、12 V电源输出。
- ◎ 硬件采用分区设计，丝印框图清晰易懂，包含传感器编号，模块采用亚克力防护。
- ◎ RFID：采用900 MHz & ETC的射频传感器，支持ISO 18000-6C协议，工作频率为ISM频段的902~928 MHz，工作模式为跳频工作、定频工作或软件可调，功率在0 dBm~27 dBm内可调，支持的可读标签协议有EPC C1 Co Gen2和ISO-18000-6C，读取距离为1~5 cm，集成了25×25高性能微小型陶瓷天线（板载天线）和ETC栏杆。

## 2.1.1.5 功能与核心代码设计案例

AiCam平台能够完成基于边缘应用的算法开发、模型开发、硬件开发、应用开发，开发例程可通过客户端的浏览器运行。示例如下：

```
################################################################################
#文件： image_face_recognition.py
################################################################################
import glob
import face_recognition
import os
import sys
import cv2 as cv
import numpy as np
import base64
import json

class ImageFaceRecognition(object):
    def __init__(self, dir_path="algorithm/image_face_recognition"):
        #读取注册人脸特征的 npy 文件
        self.dir_path = dir_path
        feature_path = os.path.join(dir_path, "*.npy")
        feature_files = glob.glob(feature_path)
        #解析文件名称， 作为注册人姓名
        self.feature_names = [item.split(os.sep)[-1].replace(".npy", "") for item in feature_files]
        #print(feature_names)
        self.face_cascade = cv.CascadeClassifier(dir_path+"/haarcascade_frontalface_alt.xml")

        self.features = []
        for f in feature_files:
            feature = np.load(f)
            self.features.append(feature)

    def image_to_base64(self, img):
        image = cv.imencode('.jpg', img, [cv.IMWRITE_JPEG_QUALITY, 60])[1]
        image_encode = base64.b64encode(image).decode()
        return image_encode

    def base64_to_image(self, b64):
        img = base64.b64decode(b64.encode('utf-8'))
        img = np.asarray(bytearray(img), dtype="uint8")
        img = cv.imdecode(img, cv.IMREAD_COLOR)
        return img

    def face_id(self, img, classifier):
        gray = cv.cvtColor(img, cv.COLOR_BGR2GRAY)
        faces = classifier.detectMultiScale(gray, 1.3, 5)
        return faces

def inference(self, image, param_data):
    #code: 识别成功返回 200
    #msg: 相关提示信息
    #origin_image: 原始图像
    #result_image: 处理之后的图像
    #result_data: 结果数据
    return_result = {'code': 200, 'msg': None, 'origin_image': None, 'result_image': None,
                     'result_data': None}

    #应用请求接口： @__app.route('/file/<action>', methods=["POST"])
    #image: 应用传递过来的数据 （根据实际应用可能为图像、音频、视频、文本）
    #param_data: 应用传递过来的参数，不能为空
    if param_data != None:
        #读取应用传递过来的图像
        image = np.asarray(bytearray(image), dtype="uint8")
        image = cv.imdecode(image, cv.IMREAD_COLOR)
        #保留原始图像数据
        origin = image.copy()

        #type=0 表示注册
        if param_data["type"] == 0:
            try:
                image_encoding = face_recognition.face_encodings(image)[0]
                if len(image_encoding) != 0:
                    flag = True
                else:
                    return_result["code"] = 404
                    return_result["msg"] = "未检测到人脸！ "
            except:
                return_result["code"] = 500
                return_result["msg"] = "注册失败！ "
            if flag:
                feature_name = param_data["reg_name"] + ".npy"
                feature_path = os.path.join(self.dir_path, feature_name)
                np.save(feature_path, image_encoding)
                print("已保存人脸")
                return_result["code"] = 200
                return_result["msg"] = "注册成功！ "
        #type=1 表示识别
        if param_data["type"] == 1:
            #调用接口进行人脸比对
            rects = self.face_id(image, self.face_cascade)
            for x, y, w, h in rects:
                crop = image[y: y + h, x: x + w]
                #视频流中人脸特征编码
                img_encoding = face_recognition.face_encodings(crop)
                if len(img_encoding) != 0:
```
