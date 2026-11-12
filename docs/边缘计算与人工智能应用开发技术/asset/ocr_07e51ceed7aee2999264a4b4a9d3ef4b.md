(1) 为边缘计算网关GW3588连接Wi-Fi天线、摄像头。

(2) 启动AiCam平台，连接局域网内的Wi-Fi网络，记录AiCam平台的IP地址，如192.168.100.200。

# 2) 工程部署

(1) 运行MobaXterm工具，通过SSH登录到边缘计算网关。

(2) 在SSH终端执行以下命令，创建项目工程目录。

```
$ mkdir -p ~/aiedge-exp
```

(3) 通过SSH将项目工程代码上传到~/aicam-exp目录下，并采用unzip命令进行解压缩，代码如下：

```
$ cd ~/aiedge-exp
$ unzip image_face_recognition.zip
```

# 2. 2. 2. 2 算法测试

# 1) 人脸注册

(1) 在SSH终端输入以下命令：

```
$ cd ~/aiedge-exp/image_face_recognition
$ conda activate py36_tf114_torch15_cpu_cv345	//Ubuntu 20.04 操作系统下需要切换环境
$ python3 image_face_recognition.py 0
```

进行人脸注册的单元测试，本测试将读取图像，并提交给算法接口进行人脸注册，返回的注册结果信息为：

```
//已保存人脸
{'result_data': None, 'msg': '注册成功！', 'code': 200, 'result_image': None, 'origin_image': None}
```

(2) 在算法文件夹下可以看到生成的人脸特征文件lilianjie.npy。

# 2) 人脸比对

在SSH终端输入以下命令：