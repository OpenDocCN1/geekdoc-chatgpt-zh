```
##############################
#文件： Driving_behavior_analysis.py
#说明： 驾驶行为分析算法
##############################
from PIL import Image, ImageDraw, ImageFont
import numpy as np
import cv2 as cv
import os,sys,time
import json
import base64
from aip import AipBodyAnalysis

class BaiduDrivingBehaviorAnalysis(object):
```