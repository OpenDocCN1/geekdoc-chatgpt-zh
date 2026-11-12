```
############################################################################
#文件： traffic_detection.py
#说明： 交通标志识别模型接口测试程序
############################################################################
import numpy as np
import cv2 as cv

import os
import json
c_dir = os.path.split(os.path.realpath(__file__))[0]
import trafficcdet

#单元测试，如果处理类中引用了文件，则在单元测试中要修改文件路径
if __name__=='__main__':
    #读取测试图像
    img = cv.imread("./test.jpg")
    #创建图像处理对象
    img_object=trafficcdet.TrafficDet()
    img_object.init(c_dir+'/models/traffic_detection')
    result=img_object.detect(img)
    print(result)
```

# 3.6.2 开发步骤与验证

# 3.6.2.1 项目部署

# 1) 硬件部署

详见2.1.2.1节。

# 2) 工程部署

(1) 运行MobaXterm工具，通过SSH登录到边缘计算网关。

(2) 在SSH终端执行以下命令，创建项目工程目录。

```
$ mkdir -p ~/aiedge-exp
```

(3) 通过SSH将本项目工程代码上传到~/aicam-exp目录下，并采用unzip命令进行解压缩。

# 3.6.2.2 模型接口编译