
```javascript
//将 base64 编码的文件转化成 Blob 格式的文件
function dataURItoBlob(base64Data) {
    var byteString;
    if (base64Data.split(',')[0].indexOf('base64') >= 0) {
        byteString = atob(base64Data.split(',')[1]);
    } else {
        byteString = unescape(base64Data.split(',')[1]);
    }
    var mimeString = base64Data.split(',')[0].split(':')[1].split(';')[0];
    var ia = new Uint8Array(byteString.length);
    for (var i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i);
    }
    return new Blob([ia], {
        type: mimeString
    });
}
</script>
```

### 2.4.1.4 基于AiCam平台的边缘视觉应用开发工具

AiCamTools是一款测试AiCam平台算法的工具，通过该工具可以快速理解算法的应用交互，实现算法的调用和数据返回，AiCamTools的运行界面如图2.46所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_117_0.png)

图2.46 AiCamTools的运行界面

## 2.4.2 开发步骤与验证

### 2.4.2.1 项目部署

- 1) 硬件部署

详见2.1.2.1节。

- 2) 工程部署

(1) 运行MobaXterm工具，通过SSH登录边缘计算网关。

(2) 在SSH终端通过以下命令，创建项目工程目录。

```
$ mkdir -p ~/aiedge-exp
```

(3) 将aicam项目压缩包上传到/home/zonesion/，并通过unzip命令进行解压缩。

```
$ cd ~/aiedge-exp
$ unzip aicam.zip
```

### 2.4.2.2 工程运行

在SSH终端输入以下命令运行项目工程。

```
$ cd ~/aicam
$ chmod 755 start_aicam.sh
$ conda activate py36_tf114_torch15_cpu_cv345    //Ubuntu 20.04 操作系统下需要切换环境
$ ./start_aicam.sh
//开始运行脚本
* Serving Flask app "start_aicam" (lazy loading)
* Environment: production
WARNING: Do not use the development server in a production environment.
Use a production WSGI server instead.
* Debug mode: off
* Running on http://0.0.0.0:4000/ (Press CTRL+C to quit)
```

### 2.4.2.3 接口测试

- 1) 实时推理

人脸检测算法（face_detection）基于深度学习实现了视频流中的人脸实时检测，通过AiCam平台的实时推理接口可调用人脸检测算法，相关的调用接口如下：

```
//摄像头视频的链接
let linkData = 'http://192.168.100.200:4000/stream/face_detection?camera_id=0'
```

打开AiCamTools，在“地址”栏中输入“http://192.168.100.200:4000”，在“算法”栏中输入“face_detection”，在“摄像头”栏中输入“camera_id=0”，在“接口”栏中选择“stream”，单击“连接”按钮即可调用人脸检测算法的实时推理接口，并在窗口中显示实时的推理视频图像，以及返回的结果数据。通过AiCamTools显示视频图像如图2.47所示，AiCamTools可以截图、清除、停止、复制等操作。

![](img/1e9ab73eb32544626458aa1c625efb7c_119_0.png)

图2.47 通过AiCamTools显示视频图像

- 2) 单次推理

百度的人脸识别算法（baidu_face_recognition）可实现人脸注册和人脸识别，通过AiCam平台的单次推理接口可调用百度的人脸识别算法。

（1）人脸注册。在前端应用中截取图像，通过Ajax接口将图像，以及包含百度账号信息的数据传递给算法进行人脸注册。百度人脸识别算法中人脸注册调用的参数如表2.12所示。

表2.12 百度人脸识别算法中人脸注册调用的参数

| 参数 | 示例 |
| --- | --- |
| url | "/file/baidu_face_recognition?camera_id=0" |
| method | 'POST' |
| processData | false |
| contentType | false |
| dataType | 'json' |
| data | let img = $('#face').attr('src')<br>let id = $('#userName').val()<br>let blob = dataURLtoBlob(img);<br>let formData = new FormData();<br>formData.append('file_name','blob','image.png'); |

续表

| 参数 | 示例 |
| --- | --- |
| data | //type=0 表示人脸注册<br>formData.append(param_data,JSON.stringify({ "APP_ID":config.user.baidu_id,<br>"API_KEY":config.user.baidu_apikey,<br>"SECRET_KEY":config.user.baidu_secretkey,"userId":id,"type":0})); |
| success | function(res){}内容：<br>return result = {'code': 200, 'msg': None, 'origin_image': None, 'result_image': None, 'result_data': None}<br>示例：<br>code/msg: 200 表示人脸注册成功，404 表示没有检测到人脸，408 表示注册超时，500 表示人脸接口调用失败 |

打开AiCamTools，在“地址”栏中输入http://192.168.100.200:4000，在“算法”栏中输入baidu_face_recognition，在“摄像头”栏中输入“camera_id=0”，在“接口”栏中选择“file”，单击“连接”按钮即可显示原始视频流图像，如图2.48所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_121_0.png)

图2.48 原始视频流图像

在“数据”栏中选择“图像”后，单击“截图”按钮即可将当前视频截图作为需要注册的人脸并保存下来，在“参数”栏中输入{{"APP_ID": "***", "API_KEY": "***", "SECRET_KEY": "***", "userId": "***", "type": 0}}（***因不同的用户或应用而不同），单击“执行”按钮即可调用百度的人脸识别算法和单次推理接口进行人脸注册，并显示返回的结果数据，如图2.49所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_122_0.png)

图2.49 人脸注册时返回的结果数据

- (2) 人脸识别。在前端应用中截取图像，通过Ajax接口将图像和包含百度账号信息的数据传递给百度人脸识别算法进行人脸识别。百度人脸识别算法中人脸识别调用的参数如表2.13所示。

表2.13 百度人脸识别算法中人脸识别调用的参数

| 参数 | 示例 |
| :--- | :--- |
| url | "/file/baidu_face_recognition?camera_id=0" |
| method | "POST" |
| processData | false |
| contentType | false |
| dataType | 'json' |
| data | let img = $(".camera>img").attr('src')<br>let blob = dataURLtoBlob(img)<br>var formData = new FormData();<br>formData.append('file_name',blob,'image.png');<br>//type=1 表示人脸识别<br>formData.append('param_data', JSON.stringify({"APP_ID":config.user.baidu_id, "API_KEY":config.user.baidu_apikey, "SECRET_KEY":config.user.baidu_secretkey,"type":1})); |

续表

| 参数 | 示例 |
| :--- | :--- |
| success | function(res){}内容：<br>return_result = {'code': 200, 'msg': None, 'origin_image': None, 'result_image': None, 'result_data': None}<br>示例：<br>code/msg: 200 表示人脸注册成功，404 表示没有检测到人脸，500 表示人脸接口调用失败。<br>origin_image/result_image: 原始图像和结果图像。<br>result_data: 返回的人脸信息 |

打开AiCamTools，在“地址”栏中输入“http://192.168.100.200:4000”，在“算法”栏中输入“baidu_face_recognition”，在“摄像头”栏中输入“camera_id=0”，在“接口”栏中选择“file”，单击“连接”按钮即可显示原始视频流图像。

在“数据”栏中选择“图像”，单击“截图”按钮即可将当前视频截图作为需要识别的对象，在“参数”栏中输入{{"APP_ID": "***", "API_KEY": "***", "SECRET_KEY": "***", "userId": "***", "type": 1}}（***因不同的用户或应用而不同），单击“执行”按钮即可调用百度的人脸识别算法和单次推理接口进行人脸识别，并在右侧边窗中显示原始图像和结果图像，在下方显示返回的结果数据，如图2.50所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_124_0.png)

图2.50 人脸识别结果

### 2.4.2.4 实时推理（人脸检测）

AiCam平台中的stream-exp示例通过调用AiCam平台内置的人脸检测算法，实现了实时的人脸检测，并将识别的图像结果和数据结果显示在页面中。

解压缩该示例的代码stream-exp.zip，用记事本打开index.html文件，将AiCam平台的服务链接修改为边缘计算网关地址：

```
//摄像头视频的链接
let linkData = 'http://192.168.100.200:4000/stream/face_detection?camera_id=0'
```

通过Chrome浏览器打开index.html文件，即可调用人脸检测算法（face_detection）进行实时的人脸检测，如图2.51所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_125_0.png)

图2.51 通过Chrome浏览器调用人脸检测算法进行实时的人脸检测

### 2.4.2.5 单次推理（人脸检测）

- 1) 工程部署

AiCam平台中的file-exp示例通过调用AiCam平台内置的百度人脸识别算法，实现了人脸注册和人脸识别，并将识别的图像结果和数据结果显示在页面中。

解压缩file-exp示例的代码file-exp.zip，用记事本打开index.html文件，修改AiCam平台的服务地址、百度账号信息。

```
//用户信息
let user = {
  edge_addr:'http://192.168.100.200:4000',
  baidu_id:'12345678',
  baidu_apikey:'12345678',
  baidu_secretkey:'12345678'
}
```

通过Chrome浏览器打开index.html文件，即可显示原始视频流图像，如图2.52所示。

- 2) 人脸注册

- (1) 单击“人脸注册”按钮，将调用百度的人脸识别算法（baidu_face_recognition）进行人脸注册。

- (2) 在弹出的对话框中填写需要注册的人脸名称ID，如图2.53所示，填写完成后单击“确认”按钮退出对话框，当前的视频截图会被上传到算法层进行人脸注册。

![](img/1e9ab73eb32544626458aa1c625efb7c_126_0.png)

图2.52 通过Chrome浏览器显示的原始视频流图像

![](img/1e9ab73eb32544626458aa1c625efb7c_127_0.png)

图2.53 填写需要注册的人脸名称ID

- (3) 人脸注册的结果信息将会在页面的右侧窗口中，信息如下：

```
08:32:13 —— {"origin_image":null,"result_image":null,"code":200,"result_data":{"timestamp":1654475532,"cached":0,"error_code":0,"log_id":1932140814,"error_msg":"SUCCESS","result":{"face_token":"cf4186d79250fdd248bbce074ee14ede","location":{"width":121,"rotation":0,"top":177.79,"height":115,"left":250.72}}},"msg":"注册成功，已成功添加至人脸库！"}
```

- 3) 人脸识别

单击“人脸识别”按钮，将会调用百度的人脸识别算法（baidu_face_recognition）进行人脸比对，如果成功识别人脸，则显示识别到的人脸结果图像，并返回识别的结果信息，如图2.54所示。

![](img/1e9ab73eb32544626458aa1c625efb7c_128_0.png)

图2.54 人脸识别结果

## 2.4.3 本节小结

本节首先介绍了边缘视觉的应用开发逻辑，以及基于AiCam平台的边缘视觉应用开发框架、开发接口和开发工具；然后介绍了具体的开发步骤和验证步骤，包括项目部署、工程运行、接口测试等内容。通过本节的学习，读者可以了解边缘视觉的应用开发逻辑，掌握基于AiCam平台的边缘视觉开发过程，学习AiCamTools的使用方法，实现人脸识别的边缘视觉应用。

## 2.4.4 思考与拓展

- (1) 简述基于AiCam的边缘视觉应用开发逻辑框架。

- (2) 在AiCam平台上，如何实现算法的应用交互、算法调度和数据返回？

## 第3章

### 边缘计算与人工智能模型开发

本章结合AiCam平台学习边缘计算与人工智能模型开发全流程。本章内容包括：

- (1) 数据采集与标注：掌握数据采集和标注的过程，以及基于CreateImg进行图像采集、基于labelImg进行图像标注的过程。

- (2) YOLOv3模型的训练与验证：掌握模型训练和验证的作用，了解主流深度学习开发框架、深度学习目标检测算法原理、YOLO的原理，结合YOLOv3和Darknet框架对交通标志识别模型进行训练与验证。

- (3) YOLOv5模型的训练与验证：掌握YOLOv5原理，结合PyTorch框架和YOLOv5对口罩检测模型进行训练与验证。

- (4) YOLOv3模型的推理与验证：掌握从开发框架到推理框架的转换过程，了解典型的常用移动端边缘推理框架，结合YOLOv3和NCNN框架对交通标志识别模型进行推理与验证。

- (5) YOLOv5模型的推理与验证：掌握RKNN框架的工作原理，结合RKNN框架和YOLOv5对口罩检测模型进行推理与验证。

- (6) YOLOv3模型的接口应用：了解常用的模型接口，掌握NCNN框架的模型接口设计，结合YOLOv3和NCNN框架设计交通标志识别模型的接口。

- (7) YOLOv5模型的接口应用：掌握RKNN框架的模型接口设计，结合YOLOv5和RKNN框架设计口罩检测模型的接口。

- (8) YOLOv3模型的算法设计：掌握AiCam平台的开发框架，结合AiCam平台和YOLOv3设计交通标志识别算法。

- (9) YOLOv5模型的算法设计：结合AiCam平台和YOLOv5设计交通标志识别算法。

## 3.1 数据采集与标注

数据采集与标注是深度学习和人工智能开发中的关键步骤，对构建高性能、鲁棒性强的模型至关重要。数据采集与标注的作用如下：

- (1) 训练模型：深度学习需要大量的数据来训练模型，以便从中学到模式和规律。通过采集并标注有代表性的数据集，可以帮助模型更好地理解输入数据的特征和关系。

- (2) 验证模型：采集的数据集可以用来验证模型的性能，标注的数据集可以用来测试模型的准确性、鲁棒性和泛化能力，从而评估模型在真实世界中的表现。

- (3) 改进模型：通过分析模型在采集和标注的数据集上的表现，可以识别模型的弱点和错误，并进一步优化和改进模型的性能，有助于不断提升模型的质量。

- (4) 解决样本偏差：数据采集与标注有助于解决样本偏差问题，通过在数据集中包含各种各样的样本，可以更好地确保模型在各种情境下都能有良好的表现。

- (5) 应对标签不平衡：在某些应用中，不同类别的样本可能具有不平衡的标签分布，通过采集并标注标签分布平衡的数据，可以帮助模型更好地应对标签的不平衡。

本节的知识点如下：

- 掌握数据采集与标注的过程。

- 结合边缘计算平台掌握通过CreateImg采集图像的步骤。

- 结合边缘计算平台掌握通过labelImg标注图像的步骤。

### 3.1.1 原理分析与开发设计

#### 3.1.1.1 数据采集和标注流程
