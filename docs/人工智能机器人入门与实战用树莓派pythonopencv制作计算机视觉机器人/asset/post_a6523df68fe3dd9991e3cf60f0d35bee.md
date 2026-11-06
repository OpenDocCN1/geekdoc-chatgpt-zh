
## 7.3 尝试编写程序使用哈尔特征检测找到实时图像中的人脸并标注出来。如果识别不准确，请调整现场的光亮度或改变 detectMultiScale 函数的参数。

参考程序：

```python
import cv2
face_cascade = cv2.CascadeClassifier('/home/pi/cascade/haarcascade_frontalface_default.xml') # 载入人脸分类器
cap = cv2.VideoCapture(0) # 开始读取摄像头信号
while(cap.isOpened()):
    (ret, frame) = cap.read() # 提取每一视频图像为 frame
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY) # 将图像转换为灰度图
    faces = face_cascade.detectMultiScale(gray, minSize=(100, 100),
    flags=cv2.CASCADE_SCALE_IMAGE) # 检测人脸的位置
    for (left, top, width, height) in faces: # 遍历找到的人脸的位置信息
        frame = cv2.rectangle(frame, (left, top), (left + width, top +
        height), (0, 0, 255), 2) # 绘制矩形框
    cv2.imshow('cascade', frame) # 预览图像
    cv2.waitKey(5) # 每帧等待 5 毫秒
cap.release()
cv2.destroyAllWindows()
```

## 7.7 本节的示例程序中只采集了一张人脸作为已知数据，也只在实时图像中识别最大的一张人脸。试改写程序，收集更多照片组成更大的已知人脸数据库，并在实时图像中识别所有人脸并用不同颜色的框标识出来。

提示：程序中可能需要知道列表中的某个元素在列表中的序号，这可以使用“列表.index(列表中的元素)”的形式得到。

参考程序：

```python
import cv2
import face_recognition
# 载入包含已知面部的图片并对它们编码
my_image_1 = cv2.imread("/home/pi/photos/1.jpg")
my_image_1 = cv2.cvtColor(my_image_1, cv2.COLOR_BGR2RGB)
my_face_encoding_1 = face_recognition.face_encodings(my_image_1)[0]
my_image_2 = cv2.imread("/home/pi/photos/2.jpg")
my_image_2 = cv2.cvtColor(my_image_2, cv2.COLOR_BGR2RGB)
my_face_encoding_2 = face_recognition.face_encodings(my_image_1)[0]
my_face_encodings = [my_face_encoding_1, my_face_encoding_2]
# 载入人脸分类器
face_cascade = cv2.CascadeClassifier('/home/pi/cascade/haarcascade_frontalface_default.xml')
cap = cv2.VideoCapture(0) # 开始读取摄像头信号
while cap.isOpened():
    (ret, frame) = cap.read() # 提取每一视频图像为 frame
    frame = cv2.flip(frame, 0) # 将图像上下镜像翻转
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY) # 将图像转换为灰度图
    faces = face_cascade.detectMultiScale(gray, minSize=(100, 100),
flags=cv2.CASCADE_SCALE_IMAGE) # 检测所有人脸的位置
    face_locations = [] # 定义一个列表存储人脸的位置信息
    for (left, top, width, height) in faces: # 遍历找到的人脸的位置信息
        # 计算出右下角的坐标
        right = left + width
        bottom = top + height
        # 为 face_locations 添加一个人脸的位置信息, 注意需要将其强制转换为 int 类型
        face_locations.append((int(top), int(right), int(bottom),
int(left)))
        rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB) # 得到每一帧的 RGB
图像信息
        face_encodings = face_recognition.face_encodings(rgb_frame, face_
locations) # 计算特定位置的人脸特征信息
        for i, face_encoding in enumerate(face_encodings): # 遍历得到的所有人脸
特征信息
            matches = face_recognition.compare_faces(my_face_encodings,
face_encoding, tolerance=0.5) # 将它与已知的脸进行比较
            if True in matches: # 如果检测成功
                (top, right, bottom, left) = face_locations[i] # 获得该人脸的
位置信息
                # 匹配第一张人脸时绘制红色矩形框, 匹配第二张人脸时绘制蓝色矩形框
                if matches.index(True) == 0:
                    cv2.rectangle(frame, (left, top), (right, bottom), (0,
0, 255), 2)
                elif matches.index(True) == 1:
                    cv2.rectangle(frame, (left, top), (right, bottom),
(255, 0, 0), 2)
    cv2.imshow("Recognition", frame) # 预览图像
    cv2.waitKey(5) # 每帧等待 5 毫秒
cap.release()
cv2.destroyAllWindows()
```

## 7.8.1 试结合追踪与识别人脸的程序，完成对特定人脸的识别与追踪程序。请在追踪程序的段落中加入上述重置到识别阶段的判断程序。

参考程序：

```python
import cv2
import serial
from roboticarm import get_message
import face_recognition
import time
# 载入包含你面部的图片并对其编码
my_image = cv2.imread("/home/pi/photos/1.jpg")
my_image = cv2.cvtColor(my_image, cv2.COLOR_BGR2RGB)
my_face_encoding = face_recognition.face_encodings(my_image)
ser = serial.Serial("/dev/ttyAMA0", 9600) # 初始化串口
# 用两个变量记录两个舵机的 PWM 值
pwm1 = 1500
pwm2 = 1500
# 令云台转到初始位置
ser.write(get_message(1, pwm1, 40))
ser.write(get_message(2, pwm2, 40))
stage = 0 # 顺序流程变量，0 表示识别，1 表示追踪
get_face = False # 表示当前是否找到人脸
start_time = time.time() # 记录最近一次失去人脸的时间信息
face_cascade = cv2.CascadeClassifier('/home/pi/cascade/haarcascade_frontalface_default.xml') # 载入人脸分类器
cap = cv2.VideoCapture(0) # 开始读取摄像头信号
while cap.isOpened():
    (ret, frame) = cap.read() # 读取每一帧视频图像为 frame
    frame = cv2.flip(frame, 0) # 将图像上下镜像翻转
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY) # 将图像转换为灰度图
    faces = face_cascade.detectMultiScale(gray, minSize=(100, 100), flags=cv2.CASCADE_FIND_BIGGEST_OBJECT) # 检测人脸的位置
    if stage == 0: # 若处于识别阶段
        face_locations = [] # 定义一个列表存储人脸的位置信息
        for (left, top, width, height) in faces: # 遍历找到的人脸位置信息
            # 计算出右下角的坐标
            right = left + width
            bottom = top + height
            # 为 face_locations 添加一个人脸的位置信息，注意需要将其强制转换为 int
            face_locations.append((int(top), int(right), int(bottom), int(left)))
            rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB) # 得到每一帧的 RGB 图像信息
            face_encodings = face_recognition.face_encodings(rgb_frame, face_locations) # 计算特定位置的人脸特征信息
            for face_encoding in face_encodings: # 遍历得到的所有人脸特征信息
                matches = face_recognition.compare_faces(my_face_encoding, face_encoding, tolerance=0.5) # 将它与已知的人脸进行比较
                if True in matches: # 如果检测成功
                    stage = 1 # 进入追踪阶段
                    get_face = True # 改变 get_face 的取值
    elif stage == 1: # 若处于追踪阶段
        if len(faces) > 0: # 如果寻找到人脸
            get_face = True # 改变 get_face 的取值
        else: # 如果没有找到人脸
            if get_face: # 若上一帧找到了人脸，更新 start_time 取值
                start_time = time.time()
            if time.time() - start_time > 5: # 若连续几秒未找到人脸，令舵机复位，并回到识别阶段
                pwm1 = 1500
                pwm2 = 1500
                stage = 0
                get_face = False # 改变 get_face 的取值
        for (left, top, width, height) in faces: # 遍历找到的人脸的位置信息
            # 计算出人脸位置中心的坐标
            x0 = left + width / 2
            y0 = top + height / 2
            # 比较坐标与中心点的差别，调整舵机的 PWM 值
            if x0 > 330:
                pwm1 += (x0 - 320) / 6
            elif x0 < 310:
                pwm1 -= (320 - x0) / 6
            if y0 > 250:
                pwm2 += (y0 - 240) / 6
            elif y0 < 230:
                pwm2 -= (240 - y0) / 6
            # 指令舵机转向
            ser.write(get_message(1, pwm1, 40))
            ser.write(get_message(2, pwm2, 40))
cap.release()
ser.close()
```

## 7.8.2 寻找一些机器人声音的音频文件，并改写追踪人脸的机器人程序，让它在识别到 特定人脸或丢失人脸目标等特殊时刻发出对应的声音。

参考程序：

```python
import cv2
import serial
from roboticarm import get_message
import face_recognition
import time
import pygame.mixer as player
from threading import Thread
# 初始化 pygame 播放器
player.init(frequency=44100)
# 自定义播放音频的函数，参数为音频文件地址
def playaudio(address):
    Sound = player.Sound(address)
    Sound.play()
    while True:
        if not player.get_busy():
            break
# 设定线程 t1、t2，功能分别为空找到人脸和丢失人脸时的音频
t1 = Thread(target = playaudio, args = ["/home/pi/audios/1.wav"])
t2 = Thread(target = playaudio, args = ["/home/pi/audios/2.wav"])
# 载入包含你面部的图片并对它编码
my_image = cv2.imread("/home/pi/photos/1.jpg")
my_image = cv2.cvtColor(my_image, cv2.COLOR_BGR2RGB)
my_face_encoding = face_recognition.face_encodings(my_image)
ser = serial.Serial("/dev/ttyAMA0", 9600) # 初始化串口
# 用两个变量记录两个舵机的 PWM 值
pwm1 = 1500
pwm2 = 1500
# 令云台转到初始位置
ser.write(get_message(1, pwm1, 40))
ser.write(get_message(2, pwm2, 40))
stage = 0 # 顺序流程变量, 0 表示识别, 1 表示追踪
get_face = False # 表示当前是否找到人脸
start_time = time.time() # 记录最近一次失去人脸的时间信息
face_cascade = cv2.CascadeClassifier('/home/pi/cascade/haarcascade_frontalface_default.xml') # 载入人脸分类器
cap = cv2.VideoCapture(0) # 开始读取摄像头信号
while cap.isOpened():
    (ret, frame) = cap.read() # 读取每一帧视频图像为 frame
    frame = cv2.flip(frame, 0) # 将图像上下镜像翻转
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY) # 将图像转换为灰度图
    faces = face_cascade.detectMultiScale(gray, minSize=(100, 100), flags=cv2.CASCADE_FIND_BIGGEST_OBJECT) # 检测人脸的位置
    if stage == 0: # 若处于识别阶段
        face_locations = [] # 定义一个列表存储人脸的位置信息
        for (left, top, width, height) in faces: # 遍历找到的人脸的位置信息
            # 计算出右下角的坐标
            right = left + width
            bottom = top + height
            # 为 face_locations 添加一个人脸的位置信息, 注意需要将其强制转换为 int
            face_locations.append((int(top), int(right), int(bottom), int(left)))
            rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB) # 得到每一帧的 RGB 图像信息
            face_encodings = face_recognition.face_encodings(rgb_frame, face_locations) # 计算特定位置的人脸特征信息
            for face_encoding in face_encodings: # 遍历得到的所有人脸特征信息
                matches = face_recognition.compare_faces(my_face_encoding, face_encoding, tolerance=0.5) # 将它与已知的人脸进行比较
                if True in matches: # 如果检测成功
                    stage = 1 # 进入追踪阶段
                    get_face = True # 改变 get_face 的取值
    elif stage == 1: # 若处于追踪阶段
        if len(faces) > 0: # 如果寻找到人脸
            # 如果此前 get_face 为 False, 说明找到了人脸, 如果距离上次失去人脸超过 5 秒, 则播放音频
            if not get_face and time.time() - start_time > 5:
                # 只有当前未播放任何音频时才播放
                if (not t1.isAlive()) and (not t2.is_alive()):
                    t1.start()
            get_face = True # 改变 get_face 的取值
        else: # 如果没有找到人脸
```
