```
my_image = cv2.cvtColor(my_image, cv2.COLOR_BGR2RGB)
my_face_encoding = face_recognition.face_encodings(my_image)
# 载入人脸分类器
face_cascade = cv2.CascadeClassifier('/home/pi/cascade/haarcascade_frontalface_default.xml')
cap = cv2.VideoCapture(0) # 开始读取摄像头信号
while cap.isOpened():
    (ret, frame) = cap.read() # 读取每一视频图像为 frame
    frame = cv2.flip(frame, 0) # 将图像上下镜像翻转
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY) # 将图像转换为灰度图
    faces = face_cascade.detectMultiScale(gray, minSize=(100, 100), flags=cv2.CASCADE_FIND_BIGGEST_OBJECT) # 检测人脸的位置
    face_locations = [] # 定义一个列表存储人脸的位置信息
    for (left, top, width, height) in faces: # 遍历找到的人脸位置信息
        # 计算出右下角的坐标
        right = left + width
        bottom = top + height
        # 为 face_locations 添加一个个人脸的位置信息,注意需要强制转换为 int 类型
        face_locations.append((int(top), int(right), int(bottom), int(left)))
    rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB) # 得到每一帧的 RGB 图像信息
        face_encodings = face_recognition.face_encodings(rgb_frame, face_locations) # 计算特定位置的人脸特征信息
        for face_encoding in face_encodings: # 遍历得到的所有人脸特征信息
            matches = face_recognition.compare_faces(my_face_encoding, face_encoding, tolerance=0.5) # 将它与已知的人脸进行比较
            if True in matches: # 如果检测成功
                (top, right, bottom, left) = face_locations[0] # 获取该人脸的位置信息
                cv2.rectangle(frame, (left, top), (right, bottom), (0, 0, 255), 2) # 绘制矩形框
    cv2.imshow("Recognition", frame) # 预览图像
    cv2.waitKey(5) # 每帧等待 5 毫秒
cap.release()
cv2.destroyAllWindows()
```

# 注意事项

+   (1) 由于提取人脸特征信息需要进行复杂的计算，程序载入时通常需要 30~60 秒的等待时间。
+   
+   (2) 如果你的程序卡顿现象严重，可以尝试缩小树莓派摄像头的图像尺寸，例如： frame = cv2.resize(frame, (160, 120))。（参数为改变后图像的宽与高，注意最好等比例缩小。）
+   
+   (3) 由于该开源包训练时使用的人脸图像数据库基本是欧美人脸，因此对东亚人脸的识别效果并不十分理想。若效果不好，可以视情况调整 compare_faces 函数中 tolerance 参数的取值。