通过AiCam平台的实时推理接口对视频流内的图像进行实时计算和检测，算法文件（mask_detection_yolov5_ncnn\algorithm\mask_detection_yolov5_ncnn\mask_detection_yolov5_ncnn.py）的相关代码如下：

```
########################################
#文件：mask_detection.py
#说明：口罩检测模型
########################################
from PIL import Image,ImageDraw,ImageFont
import numpy as np
import cv2 as cv
import os
import json
import base64
c_dir = os.path.split(os.path.realpath(__file__))[0]
class MaskDetection(object):
    def __init__(self, model_path="models/mask_detection"):
        self.model_path = model_path
        self.mask_model = MaskDet()
        self.mask_model.init(self.model_path)
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
```