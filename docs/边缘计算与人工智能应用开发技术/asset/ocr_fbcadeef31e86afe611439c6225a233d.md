![](bbox=[0.13, 0.09, 0.88, 0.57])

# 图3.43 口罩检测结果（采用NCNN框架模型）

# 3.5.2.3 RKNN框架模型开发验证

# 1) 工程部署

+   (1) 通过SSH登录到计算机或虚拟机。

+   (2) 在SSH终端执行以下命令，创建项目工程目录。

```
$ mkdir -p ~/aiedge-exp/yolov5-rknn
```

+   (3) 通过SSH将mask_model_rknn.zip（训练好的模型文件）和本项目工程代码中的rknn_convert_tools.zip上传到~/aiedge-exp/yolov5-rknn目录下，并采用unzip命令进行解压缩。