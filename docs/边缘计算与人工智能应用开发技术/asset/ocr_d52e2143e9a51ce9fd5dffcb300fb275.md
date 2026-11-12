| 参数 | 示例 |
| :--- | :--- |
| url | "/file/baidu_face_recognition?camera_id=0" |
| method | "POST" |
| processData | false |
| contentType | false |
| dataType | 'json' |
| data | let img = $(".camera>img").attr('src')
let blob = dataURLtoBlob(img)
var formData = new FormData();
formData.append('file_name',blob,'image.png');
//type=1 表示人脸识别
formData.append('param_data', JSON.stringify({"APP_ID":config.user.baidu_id, "API_KEY":config.user.baidu_apikey, "SECRET_KEY":config.user.baidu_secretkey,"type":1})); |

# 续表

| 参数 | 示例 |
| :--- | :--- |
| success | function(res){}内容：
return_result = {'code': 200, 'msg': None, 'origin_image': None, 'result_image': None, 'result_data': None}
示例：
code/msg: 200 表示人脸注册成功，404 表示没有检测到人脸，500 表示人脸接口调用失败。
origin_image/result_image: 原始图像和结果图像。
result_data: 返回的人脸信息 |

打开AiCamTools，在“地址”栏中输入“http: //192.168.100.200:4000”，在“算法”栏中输入“baidu_face_recognition”，在“摄像头”栏中输入“camera_id=0”，在“接口”栏中选择“file”，单击“连接”按钮即可显示原始视频流图像。

在“数据”栏中选择“图像”，单击“截图”按钮即可将当前视频截图作为需要识别的对象，在“参数”栏中输入
“{{"APP_ID": "***", "API_KEY": "***", "SECRET_KEY": "***", "userId": "***", "type": 1}}”（***因不同的用户或应用而不同），单击“执行”按钮即可调用百度的人脸识别算法和单次推理接口进行人脸识别，并在右侧边窗中显示原始图像和结果图像，在下方显示返回的结果数据，如图2.50所示。