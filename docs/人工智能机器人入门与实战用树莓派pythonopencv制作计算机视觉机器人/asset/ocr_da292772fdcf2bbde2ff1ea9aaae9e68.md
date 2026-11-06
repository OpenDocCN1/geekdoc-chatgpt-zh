```
```python
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
```

# 7.8.2 寻找一些机器人声音的音频文件，并改写追踪人脸的机器人程序，让它在识别到 特定人脸或丢失人脸目标等特殊时刻发出对应的声音。

参考程序：

```
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
```
```