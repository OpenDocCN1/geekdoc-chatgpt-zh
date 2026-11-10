prev_frame = get_frame(cap, scaling_factor)

    # 抓取紧接着的一帧
    cur_frame = get_frame(cap, scaling_factor)

    # 再取其后的一帧
    next_frame = get_frame(cap, scaling_factor) 
[PRE0]

# 循环读取摄像头画面
    # 直到按下 Esc 退出
    while True:
        # 展示三帧差分结果
        cv2.imshow('Object Movement', frame_diff(prev_frame, 
                cur_frame, next_frame)) 
[PRE1]

# 滑动三帧指针
        prev_frame = cur_frame
        cur_frame = next_frame 
[PRE2]

# 读取下一帧
        next_frame = get_frame(cap, scaling_factor) 
[PRE3]

# 检测 Esc 是否被按下
        key = cv2.waitKey(10)
        if key == 27:
            break 
[PRE4]

# 释放所有窗口
    cv2.destroyAllWindows() 
[PRE5]

import cv2
import numpy as np 
[PRE6]

# 创建读取摄像头的函数
def get_frame(cap, scaling_factor):
    # 从捕获对象读取原始帧
    _, frame = cap.read() 
[PRE7]

# 按比例缩放画面
    frame = cv2.resize(frame, None, fx=scaling_factor, 
            fy=scaling_factor, interpolation=cv2.INTER_AREA)
    return frame 
[PRE8]

if __name__=='__main__':
    # 初始化摄像头对象
    cap = cv2.VideoCapture(0) 
[PRE9]

# 设置画面缩放倍率
    scaling_factor = 0.5 
[PRE10]

# 不断从摄像头抓取
    # 直到用户按 Esc 停止
    while True:
        # 获取当前帧
        frame = get_frame(cap, scaling_factor) 
[PRE11]

# 转为 HSV 色彩空间
        hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV) 
[PRE12]

# 设定肤色 HSV 范围
        lower = np.array([0, 70, 60])
        upper = np.array([50, 150, 255]) 
[PRE13]

# 用阈值提取肤色区域
        mask = cv2.inRange(hsv, lower, upper) 
[PRE14]

# 将掩码与原图做位运算
        img_bitwise_and = cv2.bitwise_and(frame, frame, mask=mask) 
[PRE15]

# 执行中值滤波降噪
        img_median_blurred = cv2.medianBlur(img_bitwise_and, 5) 
[PRE16]

# 同时展示原始图和处理结果
        cv2.imshow('Input', frame)
        cv2.imshow('Output', img_median_blurred) 
[PRE17]

# 判断是否按下 Esc
        c = cv2.waitKey(5)
        if c == 27:
            break 
[PRE18]

# 关闭所有窗口
    cv2.destroyAllWindows() 
[PRE19]

import cv2
import numpy as np 
[PRE20]

# 定义摄像头帧读取函数
def get_frame(cap, scaling_factor):
    # 读取视频捕获对象的帧
    _, frame = cap.read() 
[PRE21]

# 调整图像尺寸
    frame = cv2.resize(frame, None, fx=scaling_factor, 
            fy=scaling_factor, interpolation=cv2.INTER_AREA)
    return frame 
[PRE22]

if __name__=='__main__':
    # 创建视频捕获实例
    cap = cv2.VideoCapture(0) 
[PRE23]

# 实例化背景减除器
    bg_subtractor = cv2.createBackgroundSubtractorMOG2() 
[PRE24]

# 设置背景学习帧数
    # 该参数决定算法学习速度
    # 学习率表示模型更新背景的速度
    # history 越大学习越慢，可自行调试效果
    history = 100
    # 计算学习率
    learning_rate = 1.0/history 
[PRE25]

# 循环抓取摄像头画面
    # 直到检测到 Esc 键
    while True:
        # 读取一帧
        frame = get_frame(cap, 0.5) 
[PRE26]

 # 生成前景掩码
        mask = bg_subtractor.apply(frame, learningRate=learning_rate) 
[PRE27]

 # 把灰度掩码转成彩色
        mask = cv2.cvtColor(mask, cv2.COLOR_GRAY2BGR) 
[PRE28]

 # 展示原图与检测结果
        cv2.imshow('Input', frame)
        cv2.imshow('Output', mask & frame) 
[PRE29]

 # 监听 Esc 退出信号
        c = cv2.waitKey(10)
        if c == 27:
            break 
[PRE30]

 # 释放摄像头资源
    cap.release()
    # 关闭全部窗口
    cv2.destroyAllWindows() 
[PRE31]

import cv2
import numpy as np 
[PRE32]