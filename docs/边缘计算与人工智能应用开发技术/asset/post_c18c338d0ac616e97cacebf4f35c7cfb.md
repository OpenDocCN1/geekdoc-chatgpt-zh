
```javascript
if(result.result_data.indexOf('关闭空调') > -1){
  swal('识别到关闭空调！空调关闭中...', '',
    "success",{button: false,timer: 2000});
  rtc.sendMessage(this.config.macList.mac_602,'{CD1=64,D1=?}');
}
if(result.result_data.indexOf('打开加湿器') > -1){
  swal('识别到打开加湿器！加湿器开启中...', '',
    "success",{button: false,timer: 2000});
  rtc.sendMessage(this.config.macList.mac_602,'{OD1=128,D1=?}');
}
if(result.result_data.indexOf('关闭加湿器') > -1){
  swal('识别到关闭加湿器！加湿器关闭中...', '',
    "success",{button: false,timer: 2000});
  rtc.sendMessage(this.config.macList.mac_602,'{CD1=128,D1=?}');
}
}else{
  swal('控制命令错误！', '',"error",{button: false,timer: 2000});
},
error: function(error){
  console.log(error);
  swal('语音识别失败！', '',"error",{button: false,timer: 2000});
}
}});
```

- (3) 人体检测算法。当光栅传感器报警时，智能家居系统通过EventSource接口获取处理后的视频流，通过实时推理接口调用人体检测算法来识别视频流中的人体，返回base64编码的结果图像和结果数据。前端应用处理示例如下：

```javascript
/*****************************************************************************
* 名称： getVisitor
* 功能： 当光栅传感器报警后调用人体检测算法进行检测，并拍照保存，每隔 15 s 拍照一次
******************************************************************************/
getVisitor(){
    imgData && imgData.close()
    let resultThrottle = true       //设置节流结果
    //请求视频资源
    imgData = new EventSource(this.config.user.edge_addr + this.linkData[3])
    console.log(this.config.user.edge_addr + this.linkData[3]);
    //对视频资源返回的数据进行处理
    imgData.onmessage = res => {
        let {result_image} = JSON.parse(res.data)
        this.homeVideoSrc = `data:image/jpeg;base64,${result_image}`
        let {result_data} = JSON.parse(res.data)
        //对识别到的人体进行判断，每隔 15 s 拍照一次
        if(result_data.obj_num > 0 && resultThrottle){
            console.log(result_data);
            resultThrottle = false
            this.visitorRecord.unshift({   //将检测到的人体图像添加到记录列表中
                image: this.homeVideoSrc,
                time: new Date().toLocaleTimeString()
            })
            cocoMessage.error('检测到人体，请前往监控记录页面查看！')
            setTimeout(() => {
                resultThrottle = true
            }, 15000);
        }
    }
}
```

### 4) 人体检测算法接口设计

```python
##############################################################################
#文件： person_detection.py
#说明： 人体检测
##############################################################################
from PIL import Image,ImageDraw,ImageFont
import numpy as np
import cv2 as cv
import os
import json
import base64
c_dir = os.path.split(os.path.realpath(__file__))[0]

class PersonDetection(object):
    def __init__(self, model_path="models/person_detection"):
        self.model_path = model_path
        self.person_model = PersonDet()
        self.person_model.init(self.model_path)

    def image_to_base64(self, img):
        image = cv.imencode('.jpg', img, [cv.IMWRITE_JPEG_QUALITY, 60])[1]
        image_encode = base64.b64encode(image).decode()
        return image_encode

    def base64_to_image(self, b64):
        img = base64.b64decode(b64.encode('utf-8'))
        img = np.asarray(bytearray(img), dtype="uint8")
        img = cv.imdecode(img, cv.IMREAD_COLOR)
        return img

    def draw_pos(self, img, objs):
        img_rgb = cv.cvtColor(img, cv.COLOR_BGR2RGB)
        pilimg = Image.fromarray(img_rgb)
        #创建 ImageDraw 绘图类
        draw = ImageDraw.Draw(pilimg)
#设置字体
font_size = 20
font_path = c_dir+"/.././font/wqy-microhei.ttc"
font_hei = ImageFont.truetype(font_path, font_size, encoding="utf-8")

for obj in objs:
    loc = obj["location"]
    draw.rectangle((loc["left"], loc["top"], loc["left"]+loc["width"],
                     loc["top"]+loc["height"]), outline='green',width=2)
    msg = " %.2f"%obj["score"]
    draw.text((loc["left"], loc["top"]-font_size*1), msg, (0, 255, 0), font=font_hei)
    result = cv.cvtColor(np.array(pilimg), cv.COLOR_RGB2BGR)
    return result

def inference(self, image, param_data):
    #code： 识别成功返回 200
    #msg： 相关提示信息
    #origin_image： 原始图像
    #result_image： 处理之后的图像
    #result_data： 结果数据
    return_result = {'code': 200, 'msg': None, 'origin_image':
                     None, 'result_image': None, 'result_data': None}

#实时视频接口： @_app.route('/stream/<action>')
#image： 摄像头实时传递过来的图像
#param_data： 必须为 None
result = self.person_model.detect(image)
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

#单元测试，如果处理类中引用了文件，则在单元测试中要修改文件路径
if __name__=='__main__':
    from persondet import PersonDet
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
img_object=PersonDetection(c_dir+'/.../.../models/person_detection')
#调用图像处理函数对图像进行加工处理
result=img_object.inference(img,None)
frame = img_object.base64_to_image(result["result_image"])
#图像显示
cv.imshow('frame',frame)
key=cv.waitKey(1)
if key==ord('q'):
    break
cap.release()
cv.destroyAllWindows()
else:
    from .persondet import PersonDet
```

### 5) 语音识别算法接口设计

```python
########################################
#文件： baidu_speech_recognition.py
#说明： 语音识别
########################################
import os
import wave
import numpy as np
from aip import AipSpeech
import ffmpeg
import tempfile

class BaiduSpeechRecognition(object):
    def __init__(self):
        pass

def __check_wav_file(self,filePath):
    #读取 wav 文件
    wave_file = wave.open(filePath, 'r')
    #获取文件的帧率和通道
    frame_rate = wave_file.getframerate()
    channels = wave_file.getnchannels()
    wave_file.close()
    if frame_rate == 16000 and channels == 1:
        return True
    #feature_path=filePath
    else:
        return False

def inference(self, wave_data, param_data):
#code: 识别成功返回 200
#msg: 相关提示信息
#origin_image: 原始图像
#result_image: 处理之后的图像
#result_data: 结果数据
return_result = {'code': 200, 'msg': None, 'origin_image': None,
                         'result_image': None, 'result_data': None}

#应用请求接口: @__app.route('/file/<action>', methods=["POST"])
#wave_data: 应用传递过来的数据（根据实际应用可能为图像、音频、视频、文本）：
#语音数据，格式为 wav，帧率为 16000
#param_data: 应用传递过来的参数，不能为空
if param_data != None:
    fd, path = tempfile.mkstemp()
    try:
        with os.fdopen(fd, 'wb') as tmp:
            tmp.write(wave_data)
        if not self.__check_wav_file(path):
            fd2, path2 = tempfile.mkstemp()
            ffmpeg.input(path).output(path2, ar=16000).run()
            os.remove(path)
            path = path2
        f = open(path, "rb")
        f.seek(4096)
        pcm_data = f.read()
        f.close()
    finally:
        os.remove(path)

#调用百度语音识别接口，通过以下用户密钥连接百度服务器
#APP_ID: 百度应用 ID
#API_KEY: 百度 API_KEY
#SECRET_KEY: 百度用户密钥
client = AipSpeech(param_data['APP_ID'], param_data['API_KEY'], param_data['SECRET_KEY'])
#语音文件的格式为 pcm，帧率为 16000，dev_pid 为普通话（纯中文识别）
response = client.asr(pcm_data,'pcm', 16000, {'dev_pid': 1537,})

#处理服务器返回结果
if response['err_msg']=='success.':
    return_result["code"] = 200
    return_result["msg"] = "语音识别成功！ "
    return_result["result_data"] = response["result"][0]
else:
    return_result["code"] = 500
    return_result["msg"] = response["err_msg"]
return return_result

#单元测试，如果处理类中引用了文件，则在单元测试中要修改文件路径
if __name__ == '__main__':
    #创建音频处理对象
    test = BaiduSpeechRecognition()
    param_data = {"APP_ID":"12345678", "API_KEY":"12345678", "SECRET_KEY":"12345678"}
    with open("./test.wav", "rb") as f:
        wdat = f.read()
    result = test.inference(wdat, param_data)
    print(result["result_data"])
```

### 6) 手势识别算法接口设计

```python
#####################################################################################################
#文件 : handpose_detection.py
#说明 : 手势识别
#####################################################################################################
from PIL import Image,ImageDraw,ImageFont
import numpy as np
import cv2 as cv
import os
import json
import base64
c_dir = os.path.split(os.path.realpath(__file__))[0]

class HandposeDetection(object):
    def __init__(self, model_path="models/handpose_detection"):
        self.model_path = model_path
        self.handpose_model = HandDetector()
        self.handpose_model.init(self.model_path)

    def image_to_base64(self, img):
        image = cv.imencode('.jpg', img, [cv.IMWRITE_JPEG_QUALITY, 60])[1]
        image_encode = base64.b64encode(image).decode()
        return image_encode

    def base64_to_image(self, b64):
        img = base64.b64decode(b64.encode('utf-8'))
        img = np.asarray(bytearray(img), dtype="uint8")
        img = cv.imdecode(img, cv.IMREAD_COLOR)
        return img

    def draw_pos(self, img, objs):
        img_rgb = cv.cvtColor(img, cv.COLOR_BGR2RGB)
        pilimg = Image.fromarray(img_rgb)
        #创建 ImageDraw 绘图类
        draw = ImageDraw.Draw(pilimg)
        #设置字体
        font_size = 20
        font_path = c_dir+"/../../font/wqy-microhei.ttc"
        font_hei = ImageFont.truetype(font_path, font_size, encoding="utf-8")

    for obj in objs:
        loc = obj["location"]
```
