# 附：识别黄色物体的完整程序代码

```
import cv2
cap = cv2.VideoCapture(0) # 开始读取摄像头信号
while cap.isOpened(): # 当读取到信号时
    (ret, frame) = cap.read() # 读取每一帧视频图像为 frame
    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV) # 将颜色空间转换为 HSV
    yellow_lower = (26, 43, 46) # 指定目标颜色的下限
    yellow_upper = (34, 255, 255) # 指定目标颜色的上限
    mask = cv2.inRange(hsv, yellow_lower, yellow_upper) # 使用目标范围分割图像并二值化
    (mask, cnts, hierarchy) = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE) # 寻找其中的所有外轮廓
    if len(cnts) > 0: # 如果至少找到一个轮廓
        c = max(cnts, key=cv2.contourArea) # 找出其中面积最大的轮廓
        ((x, y), radius) = cv2.minEnclosingCircle(c) # 分析轮廓的中心位置和大小
        print("中心坐标", (x, y))
        print("半径", radius)
    cv2.imshow('test', frame) # 将图像显示到屏幕上
    cv2.waitKey(5) # 每一帧后等待 5 毫秒
```

# 5.4 做一个魔法棒吧

获得了物体的坐标后，可以用它来完成一些有趣的事情，例如把物体当作“笔”在图像上绘制出图样。我们可以选择一种颜色的黏土，将其固定在任意棒状物（例如铅笔）的一端并揉成球形，做一个 DIY 的“魔法棒”（见图 5.17）。如果没有黏土，也可以任意颜色均匀的小物体代替。

![](bbox=[0.34, 0.61, 0.67, 0.78])

图 5.17 用“魔法棒”绘制图样

# 5.4.1 用 OpenCV 绘制小圆点

为了让魔法棒实现画图的效果，我们需要学习用 OpenCV 进行图形的绘制。画小圆点可以利用 circle 函数来实现：