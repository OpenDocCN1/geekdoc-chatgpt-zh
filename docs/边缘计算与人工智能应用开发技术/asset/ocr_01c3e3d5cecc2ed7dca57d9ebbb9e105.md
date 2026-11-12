```
import xml.etree.ElementTree as ET
import pickle
import os
from os import listdir, getcwd
from glob import glob
import random
from os.path import join

classes = ['face','face_mask']
dataset_path = "./dataset/"
txt_label_path = dataset_path + '/labels'
test_ratio = 0.1

if not os.path.exists(txt_label_path):
    os.mkdir(txt_label_path)

def convert(size, box):
    dw = 1. / size[0]
    dh = 1. / size[1]
    x = (box[0] + box[1]) / 2.0
    y = (box[2] + box[3]) / 2.0
    w = box[1] - box[0]
    h = box[3] - box[2]
    x = x * dw
    w = w * dw
    y = y * dh
    h = h * dh
    return (x, y, w, h)
    #return (int(x), (int(y), int(w), int(h))

def convert_annotation(image_id):
    #这里改为.xml 文件夹的路径
    in_file = open(os.path.join(dataset_path, 'Annotations/%s.xml' % (image_id)))
    #这里是生成每幅图像对应的.txt 文件的路径
    out_file = open(os.path.join(txt_label_path, "%s.txt" % (image_id)), 'w')
    tree = ET.parse(in_file)
    root = tree.getroot()
    size = root.find('size')
    w = int(size.find('width').text)
    h = int(size.find('height').text) #

for obj in root.iter('object'):
```