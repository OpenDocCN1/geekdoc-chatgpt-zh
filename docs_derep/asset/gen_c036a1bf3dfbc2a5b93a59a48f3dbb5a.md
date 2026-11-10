[PRE0]

 # 在灰度图像上调用人脸检测器

    face_rects = face_cascade.detectMultiScale(gray, 1.3, 5) 

[PRE1]

 # 为每张检测到的人脸框出绿色矩形

    for (x,y,w,h) in face_rects:

        cv2.rectangle(frame, (x,y), (x+w,y+h), (0,255,0), 3) 

[PRE2]

 # 实时展示处理后的画面

    cv2.imshow('Face Detector', frame) 

[PRE3]

 # 监听用户是否按下 Esc 键准备退出

    c = cv2.waitKey(1)

    if c == 27:

        break 

[PRE4]
# 释放摄像头资源
cap.release()

# 关闭所有 OpenCV 窗口
cv2.destroyAllWindows() 

[PRE5]
import cv2

import numpy as np 

[PRE6]
# 加载人脸与眼睛的 Haar 级联分类器文件
face_cascade = cv2.CascadeClassifier('haar_cascade_files/haarcascade_frontalface_default.xml')

eye_cascade = cv2.CascadeClassifier('haar_cascade_files/haarcascade_eye.xml')

# 校验人脸模型是否加载成功
if face_cascade.empty():

    raise IOError('Unable to load the face cascade classifier xml file')

# 校验眼睛模型是否加载成功
if eye_cascade.empty():

    raise IOError('Unable to load the eye cascade classifier xml file') 

[PRE7]
# 创建本地摄像头采集对象
cap = cv2.VideoCapture(0)

# 设定画面缩放比例以提升处理速度
ds_factor = 0.5 

[PRE8]
# 进入持续读取帧的循环，直到退出指令
while True:

    # 读取当前一帧图像
    _, frame = cap.read() 

[PRE9]
 # 按比例缩小当前帧
    frame = cv2.resize(frame, None, fx=ds_factor, fy=ds_factor, interpolation=cv2.INTER_AREA) 

[PRE10]
 # 转为灰度图方便后续检测
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY) 

[PRE11]
 # 在灰度图上搜索人脸区域
    faces = face_cascade.detectMultiScale(gray, 1.3, 5) 

[PRE12]
 # 遍历所有人脸，对每个区域进行眼睛检测
    for (x,y,w,h) in faces:

        # 截取出人脸对应的灰度子图
        roi_gray = gray[y:y+h, x:x+w] 

[PRE13]
 # 截取出人脸对应的彩色子图
        roi_color = frame[y:y+h, x:x+w]

        # 在该子图上执行眼睛定位
        eyes = eye_cascade.detectMultiScale(roi_gray) 

[PRE14]
 # 为每个眼睛绘制绿色圆圈
        for (x_eye,y_eye,w_eye,h_eye) in eyes:

            center = (int(x_eye + 0.5*w_eye), int(y_eye + 0.5*h_eye))

            radius = int(0.3 * (w_eye + h_eye))

            color = (0, 255, 0)

            thickness = 3

            cv2.circle(roi_color, center, radius, color, thickness)

    # 展示带标注的实时画面
    cv2.imshow('Eye Detector', frame) 

[PRE15]
 # 检测 Esc 键是否按下以结束程序
    c = cv2.waitKey(1)

    if c == 27:

        break 

[PRE16]
# 释放摄像头资源
cap.release()

# 关闭所有 OpenCV 窗口
cv2.destroyAllWindows() 

[PRE17]
$ pip3 install neurolab 

[PRE18]
import numpy as np

import matplotlib.pyplot as plt

import neurolab as nl 

[PRE19]
# 从文本文件读取原始训练数据
text = np.loadtxt('data_perceptron.txt') 

[PRE20]
# 把特征与标签拆分开
data = text[:, :2]

labels = text[:, 2].reshape((text.shape[0], 1)) 

[PRE21]
# 绘制输入数据的散点分布
plt.figure()

plt.scatter(data[:,0], data[:,1])

plt.xlabel('Dimension 1')

plt.ylabel('Dimension 2')

plt.title('Input data') 

[PRE22]
# 设定两个特征的取值范围
dim1_min, dim1_max, dim2_min, dim2_max = 0, 1, 0, 1 

[PRE23]
# 确定输出层神经元数量
num_output = labels.shape[1] 

[PRE24]
# 根据二维特征创建两输入神经元的感知器
# 输入范围对应数据两列
dim1 = [dim1_min, dim1_max]

dim2 = [dim2_min, dim2_max]

perceptron = nl.net.newp([dim1, dim2], num_output) 

[PRE25]
# 用准备好的数据对感知器进行训练
error_progress = perceptron.train(data, labels, epochs=100, show=20, lr=0.03) 

[PRE26]
# 绘制训练过程中误差变化曲线
plt.figure()

plt.plot(error_progress)

plt.xlabel('Number of epochs')

plt.ylabel('Training error')

plt.title('Training error progress')

plt.grid()

plt.show()