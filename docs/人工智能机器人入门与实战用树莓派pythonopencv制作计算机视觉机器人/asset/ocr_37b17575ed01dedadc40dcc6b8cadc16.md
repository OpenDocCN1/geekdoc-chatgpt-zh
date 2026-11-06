```
if gpio.input(b1) == gpio.HIGH:
    winner = 1
elif gpio.input(b2) == gpio.HIGH:
    winner = 2
# 1 号获胜亮红灯，跳出循环
elif winner == 1:
    gpio.output(R, gpio.LOW)
    gpio.output(B, gpio.HIGH)
    gpio.output(G, gpio.HIGH)
    break
# 2 号获胜亮绿灯，跳出循环
elif winner == 2:
    gpio.output(R, gpio.HIGH)
    gpio.output(B, gpio.HIGH)
    gpio.output(G, gpio.LOW)
    break
# winner = 3 表示 2 号作弊，等待 1 号按下按钮即可判令 1 号获胜
elif winner == 3:
    if gpio.input(b1) == gpio.HIGH:
        winner = 1
# winner = 4 表示 1 号作弊，等待 2 号按下按钮即可判令 2 号获胜
elif winner == 4:
    if gpio.input(b2) == gpio.HIGH:
        winner = 2
# winner = 5 表示双方都作弊，直接跳出循环
elif winner == 5:
    break
# 5 秒后清除对 GPIO 的调用
sleep(5)
gpio.cleanup()
```

# 5.2.3 连接树莓派摄像头，用 OpenCV 预览实时视频。

参考程序：

```
import cv2 # 导入 OpenCV 扩展包
cap = cv2.VideoCapture(0) # 读取摄像头信号
while cap.isOpened(): # 当摄像头信号存在时，进行循环
    (ret, frame) = cap.read() # 将读取的信号中的一帧存为 frame 变量
    cv2.imshow('test', frame) # 预览帧
    cv2.waitKey(5) # 每帧等待 5 毫秒
```

# 5.3.3 找一个颜色均匀的物体，用 OpenCV 输出这个物体在摄像头中的中心位置和大小。请根据目标物体的颜色调整 inrange 函数的参数值，注意尽量排除背景中颜色相近的其他物体。

参考程序：

```
import cv2
```