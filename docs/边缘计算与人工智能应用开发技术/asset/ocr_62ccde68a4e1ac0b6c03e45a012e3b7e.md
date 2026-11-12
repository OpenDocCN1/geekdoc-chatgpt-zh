```
from PIL import Image,ImageDraw,ImageFont
import numpy as np
import cv2 as cv
import os
import json
import base64
c_dir = os.path.split(os.path.realpath(__file__))[0]

class FireDetection(object):
    def __init__(self, model_path="models/fire_detection"):
        self.model_path = model_path
        self.fire_model = FireDet()
        self.fire_model.init(self.model_path)

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
        font_path = c_dir+"/../../font/wqy-microhei.ttc"
        font_hei = ImageFont.truetype(font_path, font_size, encoding="utf-8")

        for obj in objs:
            loc = obj["location"]
            draw.rectangle((loc["left"], loc["top"], loc["left"]+loc["width"],
                            loc["top"]+loc["height"]), outline='green', width=2)
            msg = "%.2f"%obj["score"]
            draw.text((loc["left"], loc["top"]-font_size*1), msg, (0, 255, 0), font=font_hei)
            result = cv.cvtColor(np.array(pilimg), cv.COLOR_RGB2BGR)
            return result

    def inference(self, image, param_data):
        #code：识别成功返回 200
        #msg：相关提示信息
        #origin_image：原始图像
```