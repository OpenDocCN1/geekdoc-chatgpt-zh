# 4.6.2 开发步骤与验证

# 4.6.2.1 项目部署

+   1) 硬件部署

见4.1.2.1节。

+   2) 工程部署

+   (1) 运行MobaXterm工具，通过SSH登录到边缘计算网关。

+   (2) 在SSH终端执行以下命令，创建项目工程目录。

```
$ mkdir -p ~/aiedge-exp
```

+   (3) 通过SSH将本项目开发工程代码和aicam工程包上传到~/aiedge-exp目录下，并采用unzip命令进行解压缩。

```
$ unzip edge_meter.zip
$ unzip aicam.zip -d edge_meter
```

+   (4) 参考4.1.2.1节的内容，修改工程配置文件 static/edge_fan/js/config.js 内的智云账号、硬件地址、边缘服务地址等信息。

+   (5) 通过SSH将修改后的文件上传到边缘计算网关。

+   3) 工程运行

+   (1) 在SSH终端输入以下命令运行项目工程。

```
$ cd ~/aiedge-exp/edge_meter
$ chmod 755 start_aicam.sh
$ conda activate py36_tfl14_torch15_cpu_cv345 //Ubuntu 20.04 操作系统下需要切换环境
$ ./start_aicam.sh
```

+   (2) 在客户端或者边缘计算网关端打开Chrome浏览器，输入项目页面地址http：//192.168.100.200：