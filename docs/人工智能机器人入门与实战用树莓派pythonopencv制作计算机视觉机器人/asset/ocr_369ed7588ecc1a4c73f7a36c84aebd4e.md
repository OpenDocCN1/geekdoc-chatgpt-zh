# 5.4.3 利用列表与 for 循环遍历，让屏幕上的图像显示魔棒经过的所有点，实现基本的程序功能。

参考程序：

```
import cv2
cap = cv2.VideoCapture(0) # 开始读取摄像头信号
pointlist = [] # 声明一个列表用于存储点的位
start = 0 # 声明一个变量表示是否开始记录点的位
while(cap.isOpened()): # 当读取到 信号时
    (ret, frame) = cap.read() # 读取每一帧视频图像为 frame
    frame = cv2.flip(frame,1) # 将图像左右镜像翻转
    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV) # 将颜色空间转换为 HSV
    yellow_lower = (26, 43, 46) # 指定目标颜色的下限
    yellow_upper = (34, 255, 255) # 指定目标颜色的上限
    mask = cv2.inRange(hsv, yellow_lower, yellow_upper) # 使用目标范围分割图像并二值化
    (mask, cnts, hierarchy) = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE) # 寻找其中的所有外轮廓
    if len(cnts) > 0: # 如果至少找到一个轮廓
        c = max(cnts, key=cv2.contourArea) # 找出其中面积最大的轮廓
        ((x, y), radius) = cv2.minEnclosingCircle(c) # 分析轮廓的中心位置和大小
        if radius > 20: # 仅当半径大于 20 时
            if start == 1: # start = 1 说明开始记录
                pointlist.append([x,y]) # 将点的位置追加到 pointlist 列表中
            elif start == 0: # start = 0 说明需要清除图像上的点
                pointlist = [] # 将 pointlist 重新置空
    for point in pointlist: # 遍历 pointlist 中所有点的位
        x = point[0]
        y = point[1]
        cv2.circle(frame, (int(x), int(y)), 2, (0, 255, 255), -1) # 在这些点的位上绘制一个彩色小圆点
    cv2.imshow('MagicWand', frame) # 将图像显示到屏幕上
    k = cv2.waitKey(5) # 每一帧后等待 5 毫秒，并将键盘的按键值存为 k
```

# 5.4.3 制作一个可以按键控制开始、停止、清除绘制图案的魔棒。

参考程序：

```
import cv2
cap = cv2.VideoCapture(0) # 开始读取摄像头信号
pointlist = [] # 声明一个列表用于存储点的位
start = 0 # 声明一个变量表示是否开始记录点的位
while(cap.isOpened()): # 当读取到 信号时
    (ret, frame) = cap.read() # 读取每一帧视频图像为 frame
    frame = cv2.flip(frame,1) # 将图像左右镜像翻转
    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV) # 将颜色空间转换为 HSV
    yellow_lower = (26, 43, 46) # 指定目标颜色的下限
```