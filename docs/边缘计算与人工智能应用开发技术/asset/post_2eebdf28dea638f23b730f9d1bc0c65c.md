
```python
def base64_to_image(self, b64):
    img = base64.b64decode(b64.encode('utf-8'))
    img = np.asarray(bytearray(img), dtype="uint8")
    img = cv.imdecode(img, cv.IMREAD_COLOR)
    return img


def inference(self, image, param_data):
    #code: 识别成功返回 200
    #msg: 相关提示信息
    #origin_image: 原始图像
    #result_image: 处理之后的图像
    #result_data: 结果数据
    return_result = {'code': 200, 'msg': None, 'origin_image': None,
                     'result_image': None, 'result_data': None}


#应用请求接口：@__app.route('/file/<action>', methods=["POST"])
#image: 应用传递过来的数据（根据实际应用可能为图像、音频、视频、文本）
#param_data: 应用传递过来的参数，不能为空
if param_data != None:
    #读取应用传递过来的图像
    image = np.asarray(bytearray(image), dtype="uint8")
    image = cv.imdecode(image, cv.IMREAD_COLOR)
    #对图像数据进行压缩，方便传输
    img = self.image_to_base64(image)

    #type=0 表示注册
    if param_data["type"] == 0:
        #调用百度人脸搜索与库管理接口，通过以下用户密钥连接百度服务器
        #APP_ID: 百度应用 ID
        #API_KEY: 百度 API_KEY
        #SECRET_KEY: 百度用户密钥
        client = AipFace(param_data["APP_ID"], param_data["API_KEY"],
                         param_data["SECRET_KEY"])

        #配置可选参数
        options = {}
        options["user_info"] = param_data["userId"]     #用户信息
        options["quality_control"] = "NORMAL"    #图像质量正常
        options["liveness_control"] = "NONE"     #活体检测
        options["action_type"] = "REPLACE"       #替换之前注册用户的数据
        imageType = "BASE64"

        #组名
        groupId = "zonesion"
        st = time.time()
        #避免注册多个用户时 QPS（每秒查询率）不足
        while True:
            #带参数调用人脸注册
            response = client.addUser(img, imageType, groupId, param_data["userId"], options)

            if response["error_msg"] == 'SUCCESS':
                return_result["code"] = 200
                return_result["msg"] = "注册成功，已成功添加至人脸库！"
                return_result["result_data"] = response
                break

            time.sleep(1)
            if time.time() - st > 5:
                return_result["code"] = 408
                return_result["msg"] = "注册超时，请检查网络！"
                return_result["result_data"] = response
                break

            if response["error_msg"] == 'pic not has face':
                return_result["code"] = 404
                return_result["msg"] = "未检测到人脸！"
                return_result["result_data"] = response
                break
    else:
        #调用百度人脸搜索与库管理接口， 通过以下用户密钥连接百度服务器
        client = AipFace(param_data["APP_ID"], param_data["API_KEY"],
                         param_data["SECRET_KEY"])

        #配置可选参数
        options = {}
        options["max_face_num"] = 10     #检测人脸的最大数量
        #匹配阈值 （设置阈值后， score 低于阈值的用户将不会返回信息）
        options["match_threshold"] = 70
        #图像质量控制， NONE 表示不要求图像质量， LOW 表示较低的图像质量要求，
        #NORMAL 表示一般的图像质量要求
        options["quality_control"] = "NORMAL"
        options["liveness_control"] = "NONE"   #活体检测控制 NONE: 不进行控制
        #返回相似度最高的几个用户， 默认为 1 个， 最多返回 50 个
        options["max_user_num"] = 3

        #搜索的组列表， 这里只搜索 zonesion 组中的用户
        groupIdList = "zonesion"
        imageType = "BASE64"
        #带参数调用人脸搜索， M:N 识别接口
        response = client.multiSearch(img, imageType, groupIdList, options)

        #应用部分
        if "error_msg" in response:
            if response["error_msg"] == 'pic not has face':
                return_result["code"] = 404
                return_result["msg"] = "未检测到人脸！"
                return_result["result_data"] = response
                return_result["origin_image"] = self.image_to_base64(image)
                return return_result

        if response['error_msg'] == 'SUCCESS':
            #图像输入
            img_rgb = cv.cvtColor(image, cv.COLOR_BGR2RGB)
            pilimg = Image.fromarray(img_rgb)
            #创建 ImageDraw 绘图类
            draw = ImageDraw.Draw(pilimg)
            #设置字体
            font_size = 20
            font_hei = ImageFont.truetype(self.font_path, font_size, encoding="utf-8")

            #获取数据
            #人脸数据列表
            face_list = response['result']["face_list"]
            #人脸数量
            face_num = response['result']["face_num"]
            for i in range(face_num):
                loc = face_list[i]["location"]
                if len(face_list[i]["user_list"]) > 0:
                    #获取识别分数最高的人脸数据
                    user = face_list[i]["user_list"][0]
                    score = "%.2f" % user['score']
                    user_id = user['user_id']
                    group_id = user['group_id']
                    user_info = user['user_info']
                else:
                    score = user_id = group_id = user_info = 'none'
                #绘制矩形外框
                draw.rectangle(((int(loc["left"]), int(loc["top"])),
                                (int(loc["left"]) + int(loc["width"]),
                                 int(loc["top"]) + int(loc["height"]))),
                               outline='green', width=2)
                #绘制字符
                draw.text(((loc["left"]), loc["top"] - font_size * 4), '用户 ID:' + user_id,
                          (0, 255, 0), font=font_hei)
                draw.text(((loc["left"]), loc["top"] - font_size * 3), '用户组 ID:' + group_id,
                          (0, 255, 0), font=font_hei)
                draw.text(((loc["left"]), loc["top"] - font_size * 2), '用户信息:' + user_info,
                          (0, 255, 0), font=font_hei)
                draw.text(((loc["left"]), loc["top"] - font_size * 1), '置信值:' + score,
                          (0, 255, 0), font=font_hei)

            #输出图像
            result = cv.cvtColor(np.array(pilimg), cv.COLOR_RGB2BGR)
            return_result["code"] = 200
            return_result["msg"] = "人脸识别成功！"
            return_result["origin_image"] = self.image_to_base64(image)
            return_result["result_image"] = self.image_to_base64(result)

        return_result["result_data"] = response
else:
    return_result["code"] = 500
    return_result["msg"] = "人脸接口调用失败！"
    return_result["result_data"] = response
else:
    return_result["code"] = 500
    return_result["msg"] = "百度接口调用失败！"
    return_result["result_data"] = response

#实时视频接口： @__app.route('/stream/<action>')
#image： 摄像头实时传递过来的图像
#param_data： 必须为 None
else:
    return_result["result_image"] = self.image_to_base64(image)

return return_result


#单元测试，如果处理类中引用了文件，则在单元测试中要修改文件路径
if __name__ == '__main__':
    #创建图像处理对象
    img_object = BaiduFaceRecognition()

    #读取测试图像
    img = cv.imread("./test.jpg")
    #将图像编码成数据流
    img = img_object.imencode(img)

    #设置参数
    addUser_data = {"APP_ID": "12345678", "API_KEY": "12345678",
                    "SECRET_KEY": "12345678", "type": "0", "userId": "lilianjie"}
    searchUser_data = {"APP_ID": "12345678", "API_KEY": "12345678",
                       "SECRET_KEY": "12345678", "type": "1"}
    img_object.font_path = "./../font/wqy-microhei.ttc"

    #调用接口进行人脸注册
    result = img_object.inference(img, addUser_data)
    #调用接口进行人脸识别
    if result["code"] == 200:
        result = img_object.inference(img, searchUser_data)
        try:
            frame = img_object.base64_to_image(result["result_image"])
            print(result["result_data"])

            #图像显示
            cv.imshow('frame', frame)
            while True:
                key = cv.waitKey(1)
                if key == ord('q'):
                    break
            cv.destroyAllWindows()

        except AttributeError:
            print("识别结果图像为空！请重新识别！")
        else:
            print("注册失败！")
```

## 4.1.2 开发步骤与验证

### 4.1.2.1 项目部署

- 1) 硬件部署

- (1) AiCam平台的部署。

- ① 准备AiCam平台，并正确连接Wi-Fi天线、摄像头。

- ② 启动AiCam平台，启动Ubuntu操作系统，连接局域网内的Wi-Fi网络，记录AiCam平台的IP地址，如192.168.100.200。

- (2) 项目硬件设备部署。通过以下两种方式可以部署边缘应用所需要的硬件设备

- 方式1：利用虚拟仿真进行硬件原型的搭建，Sensor-A、Sensor-C、Sensor-D、Sensor-EH传感器设备的项目。

- 方式2：利用AiCam平台的硬件来部署项目硬件设备，并与AiCam平台构建一个传感网络。

- 2) 工程部署

- (1) 运行MobaXterm工具，通过SSH登录到边缘计算网关。

- (2) 在SSH终端执行以下命令，创建项目工程目录。

```bash
$ mkdir -p ~/aiedge-exp
```

- (3) 通过SSH将本项目开发工程代码和aicam工程包（DISK-AILab/02-软件资料/02-综合应用/aicam.zip）上传到~/aiedge-exp目录下，并采用unzip命令进行解压缩。

```bash
$ unzip edge_door.zip
$ unzip aicam.zip -d edge_door
```

- (4) 修改工程配置文件static/edge_door/js/config.js内的智云账号、百度账号、硬件地址、边缘服务地址等信息，示例如下：

```javascript
user: {
    id: '12345678',    //智云账号
    key: '12345678',    //智云密钥
    addr: 'ws://api.zhiyun360.com:28080', //智云服务地址
    edge_addr: 'http://192.168.100.200:4001', //边缘服务地址
    baidu_id: '12345678',    //百度应用 ID
    baidu_apikey: '12345678',    //百度应用 APIKEY
    baidu_secretkey: '12345678',    //百度应用 SECRETKEY
},
//定义本地存储参数（MAC 地址）
macList: {
    mac_602: '01:12:4B:00:27:22:AC:4E',    //Sensor-B 控制类传感器的地址
},
```

- (5) 通过SSH将修改后的文件上传到边缘计算网关。

- 3) 工程运行

- (1) 在SSH终端输入以下命令，运行项目工程。

```bash
$ cd ~/aiedge-exp/edge_door
$ chmod 755 start_aicam.sh
$ conda activate py36_tfl14_torch15_cpu_cv345    //Ubuntu 20.04 操作系统下需要切换环境
$ ./start_aicam.sh
```

- (2) 在客户端或者边缘计算网关端打开Chrome浏览器，输入项目页面地址http://192.168.100.200:4001/static/edge_door/index.html，即可查看项目内容。

### 4.1.2.2 人脸开闸机系统的验证

本项目通过AiCam平台的门锁控制来模拟人脸开闸机的应用场景。

- 1）使用AiCam平台门锁控制的人脸开关门锁

AiCam平台的门锁控制实现了人脸的注册和识别功能。

- （1）运行AiCam平台的门锁控制功能，在其界面中选择“人脸开关门锁”，单击“人脸注册”可弹出对话框，将当前视频截图作为需要注册的人脸对象，按照要求填写用户姓名（英文），单击“注册”按钮将发送一次注册请求并返回注册结果。人脸注册如图4.6所示。
