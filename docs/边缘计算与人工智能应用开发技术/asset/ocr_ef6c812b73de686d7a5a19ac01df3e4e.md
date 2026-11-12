```
import cv2
import time
import random
import numpy as np
from rknnlite.api import RKNNLite

"""
RK3588 yolov5s 口罩检测模型
"""

def get_max_scale(img, max_w, max_h):
    h, w = img.shape[:2]
    scale = min(max_w / w, max_h / h, 1)
    return scale

def get_new_size(img, scale):
    return tuple(map(int, np.array(img.shape[2:][::-1]) * scale))

class AutoScale:
    def __init__(self, img, max_w, max_h):
        self.src_img = img
        self.scale = get_max_scale(img, max_w, max_h)
        self.__new_size = get_new_size(img, self.scale)
        self.__new_img = None

    @property
    def size(self):
        return self.__new_size

    @property
    def new_img(self):
        if self.__new_img is None:
            self.__new_img = cv2.resize(self.src_img, self.__new_size)
        return self.__new_img

def sigmoid(x):
    return 1 / (1 + np.exp(-x))

def filter_boxes(boxes, box_confidences, box_class_probs, conf_thres):
    #条件概率， 在该区域存在物体的概率是某个类别的概率
    box_scores = box_confidences * box_class_probs
    box_classes = np.argmax(box_scores, axis=-1) #找出概率最大的类别索引
```