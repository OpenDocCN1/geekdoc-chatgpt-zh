# 基于手势识别的智能风扇控制系统实战

## 核心交互逻辑实现

在前端交互层，我们建立了一套基于置信度阈值与状态机制的手势指令判断流程。系统会持续监听识别服务返回的数据包，仅当检测到目标（`obj_num > 0`）、通道处于非节流态（`throttle` 为真）且云端服务在线（`online` 为真）时，才会触发后续的控制指令分发。

针对“食指伸出”（标签 `one`）与“五指张开”（标签 `five`）两类关键手势，设定了 95% 的最低置信度门槛，并结合当前设备图标状态（`icon-on` 或 `icon-off`）实现互斥控制：识别到“食指”且风扇正在运行时发送关闭指令；识别到“五指”且风扇处于关闭态时发送开启指令。每次有效触发后立即激活 5 秒冷却期，防止抖动导致的重复操作，同时设置 1 秒的交互节流恢复窗口。

[PRE0]python
##############################################################################

# 文件： handpose_detection.py

# 说明：手势识别核心处理模块

##############################################################################

from PIL import Image, ImageDraw, ImageFont

import numpy as np

import cv2 as cv

import os

import json

import base64

c_dir = os.path.split(os.path.realpath(__file__))[0]

class HandposeDetection(object):

    def __init__(self, model_path="models/handpose_detection"):

        self.model_path = model_path

        self.handpose_model = HandDetector()

        self.handpose_model.init(self.model_path)

    def image_to_base64(self, img):

        """将 OpenCV 图像编码为 Base64 字符串，便于网络传输"""

        image = cv.imencode('.jpg', img, [cv.IMWRITE_JPEG_QUALITY, 60])[1]

        image_encode = base64.b64encode(image).decode()

        return image_encode

    def base64_to_image(self, b64):

        """将 Base64 字符串解码还原为 OpenCV 图像对象"""

        img = base64.b64decode(b64.encode('utf-8'))

        img = np.asarray(bytearray(img), dtype="uint8")

        img = cv.imdecode(img, cv.IMREAD_COLOR)

        return img

    def draw_pos(self, img, objs):

        """在图像上绘制检测框、关键点骨架及置信度标签"""

        img_rgb = cv.cvtColor(img, cv.COLOR_BGR2RGB)

        pilimg = Image.fromarray(img_rgb)

        draw = ImageDraw.Draw(pilimg)

        font_size = 20

        font_path = c_dir + './../font/wqy-microhei.ttc'

        font_hei = ImageFont.truetype(font_path, font_size, encoding="utf-8")

        for obj in objs:

            loc = obj["location"]

            # 绘制边界框

            draw.rectangle((loc["left"], loc["top"], loc["left"] + loc["width"], loc["top"] + loc["height"]),

                             outline='green', width=2)

            # 绘制类别与分数标签

            msg = obj["name"] + ": %.2f%%" % obj["score"]

            draw.text((loc["left"], loc["top"] - font_size * 1), msg, (0, 255, 0), font=font_hei)

            # 定义五指骨架颜色

            colors = [(10, 215, 255), (255, 115, 55), (5, 255, 55), (25, 15, 255), (225, 15, 55)]

            marks = obj["mark"]

            for j in range(len(marks)):

                kp = marks[j]

                # 绘制关键点圆圈

                draw.ellipse(((kp["x"] - 4, kp["y"] - 4), (kp["x"] + 4, kp["y"] + 4)),

                             fill=None, outline=(255, 0, 0), width=2)

                # 绘制骨架连线：指尖连接及掌心连指根

                finger_idx = j // 4

                # 相邻关键点连线（指骨）

                if j == 0 or j / 4 != finger_idx:

                    draw.line(((marks[j]["x"], marks[j]["y"]), (marks[j + 1]["x"], marks[j + 1]["y"])),

                              fill=colors[finger_idx], width=2)

                # 掌心(0) 连接 各指根部 (5, 9, 13, 17)

                if j == 0:

                    for tip_idx, color_idx in [(5, 1), (9, 2), (13, 3), (17, 4)]:

                        draw.line(((marks[0]["x"], marks[0]["y"]), (marks[tip_idx]["x"], marks[tip_idx]["y"])),

                                  fill=colors[color_idx], width=2)

        result = cv.cvtColor(np.array(pilimg), cv.COLOR_RGB2BGR)

[PRE1]python

        return result

    def inference(self, image, param_data):

        """

        统一推理入口

        返回结构: code, msg, origin_image, result_image(base64), result_data

        """

        return_result = {'code': 200, 'msg': None, 'origin_image': None,

                         'result_image': None, 'result_data': None}

        # 调用底层检测器

        result = self.handpose_model.detect(image)

        result = json.loads(result)

        # 若检测成功且存在目标，绘制可视化结果

        if result["code"] == 200 and result["result"]["obj_num"] > 0:

            r_image = self.draw_pos(image, result["result"]["obj_list"])

        else:

            r_image = image

        return_result["code"] = result["code"]

        return_result["msg"] = result["msg"]

        return_result["result_image"] = self.image_to_base64(r_image)

        return_result["result_data"] = result["result"]

        return return_result

# 单元测试入口

if __name__ == '__main__':

    from handpose import HandDetector

    cap = cv.VideoCapture(0)

    if not cap.isOpened():

        print("无法打开摄像头")

        exit()

    # 实例化检测器 (测试时需指定绝对路径)

    detector = HandposeDetection(c_dir + './../models/handpose_detection')

    while True:

        ret, frame = cap.read()

        if not ret:

            break

        # 执行推理

        result = detector.inference(frame, None)

        # 解码回显图像

        vis_frame = detector.base64_to_image(result["result_image"])

        cv.imshow('Handpose Demo', vis_frame)

        if cv.waitKey(1) & 0xFF == ord('q'):

            break

    cap.release()

    cv.destroyAllWindows()

else:

    from .handpose import HandDetector

[PRE2]shell

$ mkdir -p ~/aiedge-exp

[PRE3]shell

$ unzip edge_fan.zip

$ unzip aicam.zip -d edge_fan

[PRE4]shell

$ cd ~/aiedge-exp/edge_fan

$ chmod 755 start_aicam.sh

$ conda activate py36_tfl14_torch15_cpu_cv345    # Ubuntu 20.04 下需切换至指定 Conda 环境

$ ./start_aicam.sh