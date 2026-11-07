```
#####################
# -*- coding: UTF-8 -*-
import face_recognition
import cv2
import os
import numpy as np
from PIL import Image, ImageDraw, ImageFont
# 在计算机摄像头上实时运行人脸识别
video_capture = cv2.VideoCapture(0)
# 加载示例图片并学习如何识别它
path ="d:\\facelib\\" # 在同级目录下的 images 文件中存放需要被识别出的人物图
total_image=[]
total_image_name=[]
total_face_encoding=[]
for fn in os.listdir(path):                     # fn 表示的是文件名
    total_face_encoding.append(face_recognition.face_encodings
        (face_recognition.load_image_file(path+fn))[0])
    fn=fn[: (len(fn)-4)]    # 截取图片名 (这里应该把 images 文件中的图片名命名为人物名)
    total_image_name.append(fn)                     # 图片名字列表
while True:
    # 抓取一帧视频
    ret, frame = video_capture.read()               # 捕获一帧图片
    small_frame = cv2.resize(frame, (0, 0), fx=0.25, fy=0.25)
    # 将图片缩小 1/4, 为人脸识别提速
    rgb_small_frame = small_frame[:, :, ::-1]  # 将 opencv 的 BGR 格式转为 RGB 格式
    # 发现视频帧中的所有的脸和 face_encodings
    #face_locations = face_recognition.face_locations(frame)
    #face_encodings = face_recognition.face_encodings(frame, face_locations)
    #face_locations = face_recognition.face_locations(rgb_small_frame)
    face_locations = face_recognition.face_locations(rgb_small_frame)
    face_encodings = face_recognition.face_encodings(rgb_small_frame, face_
locations)
    # 在这个视频帧中循环遍历每个人脸
    for (top, right, bottom, left), face_encoding in zip(face_locations,
face_encodings):
        top *= 4
        # 还原人脸的原始尺寸
        right *= 4
        bottom *= 4
        left *= 4
        # 看看面部是否与已知人脸相匹配
        for i,v in enumerate(total_face_encoding):
            match = face_recognition.compare_faces([v], face_encoding,
tolerance=0.42)
            name = "Unknown"
            if match[0]:
                name = total_image_name[i]
```