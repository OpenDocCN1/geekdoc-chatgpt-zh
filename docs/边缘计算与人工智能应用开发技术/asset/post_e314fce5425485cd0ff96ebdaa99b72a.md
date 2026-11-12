
```
font_size = 20
font_path = c_dir+"/.././font/wqy-microhei.ttc"
font_hei = ImageFont.truetype(font_path, font_size, encoding="utf-8")

for obj in objs:
    loc = obj["location"]
    draw.rectangle((loc["left"], loc["top"], loc["left"]+loc["width"],
    loc["top"]+loc["height"]), outline='green', width=2)
    msg = obj["name"]+": %.2f"%obj["score"]
    draw.text((loc["left"], loc["top"]-font_size*1), msg, (0, 255, 0), font=font_hei)

color1 = (10, 215, 255)
color2 = (255, 115, 55)
color3 = (5, 255, 55)
color4 = (25, 15, 255)
color5 = (225, 15, 55)
marks = obj["mark"]
for j in range(len(marks)):
    kp = obj["marks"][j]
    draw.ellipse(((kp["x"]-4, kp["y"]-4), (kp["x"]+4,kp["y"]+4)),
    fill=None,outline=(255,0,0),width=2)
    color = (color1,color2,color3,color4,color5)
    ii = j //4
    if j==0 or j / 4 != ii:
        draw.line(((marks[j]["x"],marks[j]["y"]),(marks[j+1]["x"],marks[j+1]["y"])),
        fill=color[ii],width=2)

draw.line(((marks[0]["x"],marks[0]["y"]),(marks[5]["x"],marks[5]["y"])), fill=color[1],width=2)
draw.line(((marks[0]["x"],marks[0]["y"]),(marks[9]["x"],marks[9]["y"])), fill=color[2],width=2)
draw.line(((marks[0]["x"],marks[0]["y"]),(marks[13]["x"],marks[13]["y"])), fill=color[3],width=2)
draw.line(((marks[0]["x"],marks[0]["y"]),(marks[17]["x"],marks[17]["y"])), fill=color[4],width=2)

result = cv.cvtColor(np.array(pilimg), cv.COLOR_RGB2BGR)
return result
```

```
def inference(self, image, param_data):
    #code: 识别成功返回 200
    #msg: 相关提示信息
    #origin_image: 原始图像
    #result_image: 处理之后的图像
    #result_data: 结果数据
    return_result = {'code': 200, 'msg': None, 'origin_image': None,
    'result_image': None, 'result_data': None}
    #实时视频接口： @__app.route('/stream/<action>')
    #image: 摄像头实时传递过来的图像
    #param_data: 必须为 None
    result = self.handpose_model.detect(image)
    result = json.loads(result)
    if result["code"] == 200 and result["result"]["obj_num"] > 0:
        r_image = self.draw_pos(image, result["result"]["obj_list"])
    else:
        r_image = image
        return_result["code"] = result["code"]
        return_result["msg"] = result["msg"]
        return_result["result_image"] = self.image_to_base64(r_image)
        return_result["result_data"] = result["result"]
        return return_result
```

```
#单元测试，如果处理类中引用了文件，则在单元测试中要修改文件路径
if __name__=='__main__':
    from handpose import HandDetector
    #创建视频捕获对象
    cap=cv.VideoCapture(0)
    if cap.isOpened()!=1:
        pass
    #循环获取图像、处理图像、显示图像
    while True:
        ret,img=cap.read()
        if ret==False:
            break
        #创建图像处理对象
        img_object=HandposeDetection(c_dir+'../../models/handpose_detection')
        #调用图像处理函数对图像进行加工处理
        result=img_object.inference(img,None)
        frame = img_object.base64_to_image(result["result_image"]))
        #图像显示
        cv.imshow('frame',frame)
        key=cv.waitKey(1)
        if key==ord('q'):
            break
    cap.release()
    cv.destroyAllWindows()
else:
    from .handpose import HandDetector
```

## 5.2.2 开发步骤与验证

### 5.2.2.1 项目部署

- 1）硬件部署

  见4.1.2.1节。

- 2) 工程部署

  - (1) 运行MobaXterm工具，通过SSH登录到边缘计算网关。
  - (2) 在SSH终端创建项目工程目录。
  - (3) 通过SSH将本项目开发工程代码和aicam工程包上传到~/aiedge-exp目录下，并采用unzip命令解压。

    ```
    $ unzip edge_autopilot.zip
    $ unzip aicam.zip -d edge_autopilot
    ```

  - (4) 参考5.1.2.1节的内容，修改工程配置文件static/edge_fan/js/config.js内的智云账号、硬件地址、边缘服务地址等信息。
  - (5) 通过SSH将修改好的文件上传到边缘计算网关。

- 3) 工程运行

  - (1) 在SSH终端输入以下命令运行项目工程。

    ```
    $ cd ~/aiedge-exp/edge_autopilot
    $ chmod 755 start_aicam.sh
    $ conda activate py36_tf114_torch15_cpu_cv345  //Ubuntu 20.04 操作系统下需要切换环境
    $ ./start_aicam.sh
    ```

  - (2) 在客户端或者边缘计算网关端打开Chrome浏览器，输入项目页面地址https://192.168.100.200:1446/static/edge_autopilot/index.html，即可查看项目内容。

### 5.2.2.2 辅助驾驶系统的验证

- 1) 智慧物联

  - （1）应用设置：第一次登录辅助驾驶系统后，单击首页右上角的“设置”按钮可以设置相关的参数（在默认情况下，系统会读取static/edge_autopilot/js/config.js内的初始配置），如智云账号、节点地址、百度AI账号等。设置参数后，在智云账号界面单击“连接”按钮即可连接到数据服务，连接成功后会弹出消息提示。辅助驾驶系统的应用设置页面如图5.17所示。

    ![](img/1e9ab73eb32544626458aa1c625efb7c_557_0.png)

    ### 图5.17 辅助驾驶系统的应用设置界面

  - （2）应用交互。在辅助驾驶系统的首页可以看到车内硬件的数据，包括Sensor-A采集类传感器的数据[如温度、湿度、空气质量（TVOC）]，Sensor-B控制类传感器的数据[车窗、风扇（通风）、空调]，如图5.18所示。通过观察虚拟仿真平台或实际硬件的变化，可判断应用交互的结果。

    ![](img/1e9ab73eb32544626458aa1c625efb7c_558_0.png)

    ### 图5.18 辅助驾驶系统控制平台（手动）

    Sensor-A采集类传感器在默认情况下每15 s更新一次数据，首页的数据会相应地进行更新。Sensor-B控制类传感器可通过单击首页中的相应图标来进行开关操作。

  - （3）模式控制。单击辅助驾驶系统首页中的模式按钮可切换手动模式和智能模式。当设置为智能模式时，AI识别功能将不可用，系统在默认情况下会每隔15 s获取摄像头实时拍摄的驾驶行为图像，并调用百度驾驶行为分析算法进行分析。

    - ① 未检测到驾驶员时的界面如图5.19所示。

      ![](img/1e9ab73eb32544626458aa1c625efb7c_559_0.png)

      ### 图5.19 未检测到驾驶员时的界面

    - ② 检测到驾驶员时的界面如图5.20所示。

      ![](img/1e9ab73eb32544626458aa1c625efb7c_559_1.png)

      ### 图5.20 检测到驾驶员时的界面

    - ③ 驾驶行为检测图像如图5.21所示。

      ![](img/1e9ab73eb32544626458aa1c625efb7c_560_0.png)

      ### 图5.21 驾驶行为检测图像（不正常行为）

- 2) 手势交互

  - (1) 在手动模式下，在摄像头的视线内摆好手势实时进行手势识别，成功识别到手势后系统可自动对相应的设备进行开关操作，如图5.22所示。系统连续5次识别同一个手势则进行相应的操作，手势1表示对车窗进行开关操作，手势2表示对风扇进行开关操作，手势3表示对空调进行开关操作。

    ![](img/1e9ab73eb32544626458aa1c625efb7c_561_0.png)

    ### 图5.22 手势识别成功（打开车窗）

  - (2) 手势识别成功后，再次识别到同样的手势时，如果之前设备处于打开状态，则本次操作将关闭该设备，如图5.23所示。

    ![](img/1e9ab73eb32544626458aa1c625efb7c_561_1.png)

    ### 图5.23 手势识别成功（关闭车窗）

- 3) 语音交互

  - (1) 在手动模式下，单击首页中的“语音识别”按钮即可开始录制语音，再次单击该按钮可结束录制并进行语音识别，根据语音识别结果对车内设备进行开关操作。例如，录制“打开车窗”语音后，辅助驾驶系统将打开车窗，如图5.24所示。

    ![](img/1e9ab73eb32544626458aa1c625efb7c_562_0.png)

    ### 图5.24 语音识别控制成功的界面（打开车窗）

## 5.2.3 本节小结

本节首先分析了智能辅助驾驶的需求和发展趋势；然后介绍了辅助驾驶系统的系统框架和系统功能，在此基础上完成了硬件设计和通信设计；接着借助AiCam平台给出了辅助驾驶系统的开发流程、算法交互、语音识别控制设备、驾驶行为分析算法接口设计、百度语音识别接口设计、手势识别算法接口设计；最后对辅助驾驶系统进行了验证。

## 5.2.4 思考与拓展

- (1) 智能辅助驾驶需要什么前提条件？
- (2) 如何实现驾驶行为分析？
- (3) 简述辅助驾驶系统的开发过程。

## 参考文献

- [1] 英特尔亚太研发有限公司. 边缘计算技术与应用[M]. 北京: 电子工业出版社, 2021.
- [2] 杨术. 5G新时代与边缘计算[M]. 北京: 电子工业出版社, 2022.
- [3] 卜天聪. 面向计算机视觉的高能效边缘计算架构关键技术研究[D]. 长春: 吉林大学, 2022.
- [4] 雷鑫. 基于边缘计算与深度学习的实时人脸识别关键技术[D]. 南昌: 南昌大学, 2021.
- [5] 冉雪. 基于YOLO的目标检测算法设计与实现[D]. 重庆: 重庆大学, 2020.
- [6] 张明伟. 基于边缘计算的目标检测与识别算法[D]. 福州: 福建师范大学, 2020.
- [7] 顾笛儿, 卢华, 谢人超, 等. 边缘计算开源平台综述[J]. 网络与信息安全学报, 2021, 7 (2) : 22-34.
- [8] 徐坤坤. 面向5G MEC的边缘计算平台实现和部署方案研究[D]. 深圳: 深圳大学, 2020.
- [9] 王健. 轻量级边缘计算平台方案设计与应用研究[D]. 北京: 北京邮电大学, 2019.
- [10] 赵梓铭, 刘芳, 蔡志平, 等. 边缘计算: 平台、应用与挑战[J]. 计算机研究与发展, 2018, 55 (2) : 327-337.
- [11] 刘晶宇, 杨鹏. 基于YOLOv5改进的遥感图像目标检测[J]. 计算机时代, 2023 (7) : 50-55.
- [12] 张勇. 基于YOLOv3的交通目标检测算法研究[D]. 淮南: 安徽理工大学, 2021.
- [13] 康庄. 基于改进YOLOv3的交通枢纽行人检测与跟踪技术研究[D]. 赣州：江西理工大学，2021.
- [14] 李珣，刘瑶，李鹏飞，等. 基于Darknet框架下YOLOv2算法的车辆多目标检测方法[J]. 交通运输工程学报，2018，18（6）：142-158.
- [15] 刘瑶. 改进Darknet框架的多目标检测与识别方法研究[D]. 西安：西安工程大学，2019.
- [16] 郭涛，郭家，李宗南，等. 基于Darknet深度学习框架的桃花检测方法[J]. 中国农业信息，2021，33（6）：25-33.
- [17] 陈旭. 基于深度学习的无人机图像目标检测算法研究[D]. 杭州：杭州电子科技大学，2022.
- [18] 黄煜真，元泽怀，陈嘉瑞，等. PyTorch框架下基于CNN的人脸识别方法研究[J]. 信息与电脑（理论版），2022，34（10）：193-195.
- [19] 李蒋. 基于深度学习PyTorch框架下YOLOv3的交通信号灯检测[J]. 汽车电器，2022（6）：4-7.
- [20] 黄玉萍，梁炜萱，肖祖环. 基于TensorFlow和PyTorch的深度学习框架对比分析[J]. 现代信息科技，2020，4（4）：80-82，87.
- [21] 焦利伟，张敏，麻连伟，等. 基于PyTorch框架搭建U-Net网络模型的遥感影像建筑物提取研究[J]. 河南城建学院学报，2020，29（4）：52-57.
- [22] 王甜. PyTorch至ONNX的神经网络格式转换的研究[D]. 西安：西安电子科技大学，2022.
- [23] 李秉涛. 基于轻量级CNN的实时目标检测研究[D]. 贵阳：贵州大学，2022.
- [24] 张英杰. 深度卷积神经网络嵌入式推理框架的设计与实现[D]. 广州：华南理工大学，2020.
- [25] 边缘计算产业联盟，机器视觉产业联盟，智能视觉产业联盟. 边缘计算视觉基础设施白皮[R]. 北京：边缘计算产业联盟，2022.
- [26] 百度智能云. “云智一体”技术与应用解析系列白皮书（第三期）：智能物联网篇[R].北京：百度，2021.
- [27] 中兴通讯股份有限公司.中兴通讯Common Edge边缘计算白皮书[R].深圳：中兴通讯股份有限公司，2021.
- [28] 边缘计算产业联盟，工业互联网产业联盟.边缘计算参考框架2.0[R].北京：边缘计算产业联盟，2017.
- [29] Zhang J,Yan Y;Lades,M.Face recognition:eigenface,elastic matching,and neural nets[J].Proceedings of the IEEE E,1997,85 (9):1423–1435.
- [30] 葛宏孔，罗恒利，董佳媛.基于深度学习的非实验室场景人脸属性识别[J].计算机科学，2019，46（z2）：246–250.
- [31] Ramadhan M V,Muchtar K,Nurdin Y,et al.Comparative analysis of deep learning models for detecting face mask[J].Procedia Computer Science,2023,216:48–56.
- [32] Li F,Li X,Liu Q,et al..Occlusion handling and multi-scale pedestrian detection based on deep learning:a review[J].IEEE Access,2022,10:19937–19957.
- [33] Kumar A,Kaur A,Kumar M,et al..Face detection techniques:a review.artificial intelligence review[J].Multimedia Tools and Applications,2019,52:927–948.
- [34] Xu C,Li Z,Tian X,et al..Vehicle detection based on modified YOLOv3 and deformable convolutional network[J].IEEE Access,2019,7:65763–65772.
- [35] Wu Y,Ji S,Wang Y,et al..Real-time traffic sign recognition based on YOLO and deep residual network[C].In Proceedings of the 2020 IEEE International Conference on Big Data,Artificial Intelligence and Internet of Things Engineering,2020.
