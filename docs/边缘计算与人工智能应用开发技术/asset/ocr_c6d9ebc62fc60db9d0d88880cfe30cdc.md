```
###############################################################################
#文件： face_recognition.py
#说明： 人脸识别
###############################################################################
import numpy as np
import cv2 as cv
import os
import json
import base64
import copy
import math
import time
from PIL import Image, ImageDraw, ImageFont

c_dir = os.path.split(os.path.realpath(__file__))[0]

def load_json_file(name):
    jo = {}
    if os.path.exists(name):
        with open(name,"r") as f:jo = json.loads(f.read())
    return jo

def save_json_file(name, jo):
    with open(name,"w") as f:f.write(json.dumps(jo))

def image_to_base64(img):
```