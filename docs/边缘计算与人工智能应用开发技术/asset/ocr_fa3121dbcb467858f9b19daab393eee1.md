```
if __name__ == '__main__':
    RKNN_MODEL_PATH = r"./yolov5s-mask.rknn"
    SIZE = (640, 640)
    MASKS = [[0, 1, 2], [3, 4, 5], [6, 7, 8]]
    ANCHORS = [[10, 13], [16, 30], [33, 23], [30, 61], [62, 45], [59, 119], [116, 90],
        [156, 198], [373, 326]]

    CLASSES = ('face','face_mask')
    #初始化
    detector = Yolov5_Rknn_Detection(RKNN_MODEL_PATH, SIZE, MASKS, ANCHORS,
                    CLASSES)

    #读取测试图像
    img = cv2.imread("./test.jpg")
    respond= detector.predict_resize(img)
    print(respond)
```

# 3.7.2 开发步骤与验证

# 3.7.2.1 项目部署

+   1) 硬件部署

详见2.1.2.1节。

+   2) 工程部署

+   (1) 运行MobaXterm工具，通过SSH登录到边缘计算网关。

+   (2) 在SSH终端中执行以下命令，创建项目工程目录。

```
$ mkdir -p ~/aiedge-exp
```

+   (3) 通过SSH将本项目工程代码上传到~/aicam-exp目录下，并采用unzip命令进行解压缩。

```
$ unzip mask_detection_interface_ncnn.zip
$ unzip mask_detection_interface_rknn.zip
```

# 3.7.2.2 NCNN框架模型接口开发验证