```
################################################################################
#文件： plate_recognition.py
#说明： 车牌识别
################################################################################
from PIL import Image,ImageDraw,ImageFont
import numpy as np
import cv2 as cv
import os
import json
import base64
c_dir = os.path.split(os.path.realpath(__ file__))[0]

load = False
class PlateRecognition(object):
    def __init__(self, model_path="models/plate_recognition/"):
        global load
        if load:
            model_path="./"
        self.plate_model = PlateRecognizer()
        self.plate_model.init(model_path)
        load = True

    def image_to_base64(self, img):
        image = cv.imencode('.jpg', img, [cv.IMWRITE_JPEG_QUALITY, 60])[1]
        image_encode = base64.b64encode(image).decode()
```