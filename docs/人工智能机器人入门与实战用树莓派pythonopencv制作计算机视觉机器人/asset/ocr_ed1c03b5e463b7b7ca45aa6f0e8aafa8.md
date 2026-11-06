```
ser.write(get_message(1, pwm1, 3))
ser.write(get_message(2, pwm2, 3))
ser.write(get_message(3, pwm3, 3))
ser.write(get_message(4, 2500, 9)) # 打开气泵
time.sleep(3) # 吸住 3 秒
for height in range(80, 1000, 10): # 竖直提起 1000 高度
    (pwm2, pwm3) = get_angle(matrix, height, distance)
    ser.write(get_message(2, pwm2, 9))
    ser.write(get_message(3, pwm3, 9))
    time.sleep(0.01)
ser.write(get_message(4, 500, 9)) # 关闭气泵
# 将 2、3 号舵机还原到初始位置
ser.write(get_message(2, 1500, 4))
ser.write(get_message(3, 1500, 4))
time.sleep(1)
# 旋转轴 1 转至目标区域上方
ser.write(get_message(1, 2100, 4))
time.sleep(1)
ser.write(get_message(5, 2500, 9)) # 打开电子阀门
time.sleep(2) # 等待 2 秒
ser.write(get_message(5, 500, 9)) # 关闭电子阀门
ser.close() # 关闭串口连接
```

# 7.2 尝试编写程序，使用肤色检测找到实时图像中的人脸，并标注出来。如果识别不准确，请调整程序中的 H 值或改变摄像头前方的图像背景和光照强度。

参考程序：

```
import cv2
cap = cv2.VideoCapture(0) # 开始读取摄像头信号
while cap.isOpened(): # 当读取到信号时
    (ret, frame) = cap.read() # 读取每一帧视频图像为 frame
    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV) # 将颜色空间转换为 HSV
    skin_lower = (0, 43, 46) # 指定皮肤颜色的下限
    skin_upper = (17, 255, 255) # 指定皮肤颜色的上限
    mask = cv2.inRange(hsv, skin_lower, skin_upper) # 使用目标范围分割图像
并二值化
    (mask, cnts, hierarchy) = cv2.findContours(mask, cv2.RETR_EXTERNAL,
cv2.CHAIN_APPROX_SIMPLE) # 寻找其中的所有外轮廓
    if len(cnts) > 0: # 如果至少找到一个轮廓
        c = max(cnts, key=cv2.contourArea) # 找出其中面积最大的轮廓
        ((x, y), radius) = cv2.minEnclosingCircle(c) # 分析轮廓的中心位置和大小
        if radius > 100: # 仅当轮廓大小达到一定尺寸时
            cv2.circle(frame, (int(x), int(y)), int(radius), (0, 0,
255), 2) # 绘制最小包围圆
    cv2.imshow('mask', frame) # 预览图像
    cv2.waitKey(5) # 每帧等待 5 毫秒
cap.release()
cv2.destroyAllWindows()
```