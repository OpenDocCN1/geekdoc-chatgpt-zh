
- face_det.bin/face_det.param、人脸识别模型文件
- face_rec.bin/face_rec.param。
- (3) 添加算法。在aicam工程中添加深度学习人脸识别算法 algorithm/face_recognition/face_recognition.py、百度人脸识别算法 algorithm/baidu_face_recognition/baidu_face_recognition.py。
- (4) 添加应用。在aicam工程中添加前端应用 static/edge_door。

## 3) 硬件通信设计

前端应用中的硬件控制部分通过智云ZCloud API连接到硬件系统，前端应用处理示例如下：

```
getConnect()
//连接智云服务
function getConnect(){
    //建立连接服务的函数
    rtc = new WSNRConnect(config.user.id, config.user.key)
    rtc.setServerAddr(config.user.addr);
    rtc.connect();
    rtc.onConnect = () => {
    //连接成功回调函数
    online = true
    setTimeout(() => {
    if(online){
      cocoMessage.success('数据服务连接成功！ 查询数据中...')
      //发起数据查询
      rtc.sendMessage(config.macList.mac_602,config.sensor.mac_602.query);
      }
}, 200);
}
rtc.onConnectLost = () => {    //数据服务掉线回调函数
  online = false
  cocoMessage.error('数据服务连接失败!请检查网络或 IDKEY...')
};
rtc.onmessageArrive = (mac, dat) => {     //消息处理回调函数
  if (dat[0] == '{' && dat[dat.length - 1] == '}') {
    //截取后台返回的 JSON 对象（去掉{}符号）后，以 "," 分割为数组
    let its = dat.slice(1,-1).split(',')
    for (let i = 0; i < its.length; i++) {    //循环遍历数组的每一个值
      let t = its[i].split("==");     //将每个值以 "==" 分割为数组，
      if (t.length != 2) continue;
      //mac_602 控制类传感器
      if (mac == config.macList.mac_602) {
        console.log('门锁开关: ',t);
        if(t[0] == 'D1'){    //开关控制
          if (t[1] & 64) {
            //判断接收到的命令是否跟上次的命令一样
            //通过当前显示的图标可以得到上次命令的结果
            if($("#icon").attr('src').indexOf('icon-off') > -1){
              $("#icon").attr('src','./img/icon-on.gif')
              setTimeout(() => {
                rtc.sendMessage(mac,config.sensor.mac_602.doorClose);
              }, 5000);
            }
          } else {
            //判断接收到的命令是否跟上次的命令一样
            //通过当前显示的图标可以得到上次命令的结果
            if($("#icon").attr('src').indexOf('icon-on') > -1){
              $("#icon").attr('src','./img/icon-off.gif')
            }
          }
        }
      }
    }
  }
}
```

## 4）人脸识别算法的交互

前端应用的算法采用RESTful接口获取处理后的视频流，返回base64编码的结果图像和结果数据。当检测到注册的人脸时将会执行打开闸机命令。访问URL地址的格式如下（IP地址为边缘计算网关的地址）：

```
http://192.168.100.200:4001/stream/[algorithm_name]?camera_id=0
```

前端应用处理示例如下：

```
let linkData = [
    '/stream/index?camera_id=0',
    '/stream/face_recognition?camera_id=0'
]
//请求图像流资源
let imgData = new EventSource(linkData[1])
//对图像流返回的数据进行处理
imgData.onmessage = function (res) {
    let {result_image} = JSON.parse(res.data)
    $('#img_box>img').attr('src', `data:image/jpeg;base64,${result_image}`)

    if (interactionThrottle) {
        interactionThrottle = false
        let {result_data} = JSON.parse(res.data)
        let html = `<div>${new Date().toLocaleTimeString()}————${JSON.stringify(result_data)}
</div>`
        $('#text-list').prepend(html);
        console.log(result_data);
        //只有成功连接智云服务（online），且不处于节流（throttle）状态，才能进行判断
        if (result_data.obj_num > 0 && throttle && online) {
            throttle = false
            //识别到人脸且 name 不为 unknown，表示已注册用户，发送开启闸门命令
            if(result_data.obj_list[0].name != 'unknown'){
                cocoMessage.success(`用户：${result_data.obj_list[0].name},!门锁开启中，请稍等...`)
                rtc.sendMessage(config.macList.mac_602, config.sensor.mac_602.doorOpen);
            }else{
                //识别到人脸且 name 为 unknown，表示未注册用户，
                //弹窗提示并截图当前图像显示到右侧图像列表
                cocoMessage.error('非法用户！请进行注册...')
                $('#result1').click()
            }
            setTimeout(() => {
                throttle = true
            }, 10000);
        }
        setTimeout(() => {
            interactionThrottle = true
        }, 1000);
    }
}
```

## 5) 百度人脸识别算法交互

前端应用截取图像后，通过Ajax接口将图像以及包含百度账号信息的数据传递给算法进行人脸识别。百度人脸识别算法的交互参数如表4.3所示。

## 表4.3 百度人脸识别算法的交互参数

| 参数 | 说明 |
|---|---|
| url | "/file/baidu_face_recognition?camera_id=0" |
| method | 'POST' |
| processData | false |
| contentType | false |
| dataType | 'json' |
| data | let img = $("camera>img").attr('src')<br>let blob = dataURLtoBlob(img)<br>var formData = new FormData();<br>formData.append('file_name',blob,'image.png');<br>//type=1 表示人脸识别<br>formData.append('param_data', JSON.stringify({"APP_ID":config.user.baidu_id,<br>"API_KEY":config.user.baidu_apikey, "SECRET_KEY":config.user.baidu_secretkey,"type":1})); |
| success | function(res){} 内容；<br>return_result = {'code': 200, 'msg': None, 'origin_image': None, 'result_image': None, 'result_data': None}<br>示例：<br>code/msg: 200 表示人脸注册成功，404 表示没有检测到人脸，500 表示人脸识别接口调用失败。<br>origin_image/result image: 表示原始图像和结果图像。result data: 算法返回的人脸信息 |

前端应用拍照并上传含有待识别的人脸图像，算法进行人脸识别，当识别到注册的人脸后，将会执行开启闸门的命令：

```
$("#result2").click(function () {
    let img = $("#img_box>img").attr('src')
    let blob = dataURLtoBlob(img)
    cocoMessage.info('正在识别中，请稍等...')
    var formData = new FormData();
    formData.append('file_name',blob,'image.png');
    //type=1 表示人脸识别
    formData.append('param_data', JSON.stringify({"APP_ID":config.user.baidu_id,
        "API_KEY":config.user.baidu_apikey,
        "SECRET_KEY":config.user.baidu_secretkey,"type":1}));
    $.ajax({
        url: './file/baidu_face_recognition',
        method: 'POST',
        processData: false,    //必需的
        contentType: false,    //必需的
        dataType: 'json',
        data: formData,
        success: function(result) {
            console.log(result);
        }
    });
}
if(result.code==200) {
    let img = 'data:image/jpeg;base64, ' + result.origin_image;
    let html = `<div class="img-li">
    <div class="img-box">
    <img src="${img}" alt="" data-toggle="modal" data-target="#myModal">
    </div>
    <div class="time">原始图像<span></span><span>${new Date().toLocaleString()}</span></div>
    </div>`;
    $(".list-box").prepend(html);

    let img1 = 'data:image/jpeg;base64, ' + result.result_image;
    let html1 = `<div class="img-li">
    <div class="img-box">
    <img src="${img1}" alt="" data-toggle="modal" data-target="#myModal">
    </div>
    <div class="time">识别结果<span></span><span>${new Date().toLocaleString()}</span></div>
    </div>`;
    $(".list-box").prepend(html1);
    //将识别到的人脸文本信息渲染到页面上
    let text = result.result_data.result.face_list[0].user_list[0].user_id
    let html2 = `<div>${new Date().toLocaleTimeString()} ——
    识别结果： ${text}</div>`;
    $("#text-list").prepend(html2);
    //返回成功的识别结果则开启闸门
    if(result.result_image && online){
    cocoMessage.success(`用户： ${text}!,门锁开启中、请稍等...`)
    rtc.sendMessage(config.macList.mac_602, config.sensor.mac_602.doorOpen);
    }
} else if(result.code==404){
    cocoMessage.error("用户识别失败，请重新尝试...")
    let img = 'data:image/jpeg;base64, ' + result.origin_image;
    let html = `<div class="img-li">
    <div class="img-box">
    <img src="${img}" alt="" data-toggle="modal" data-target="#myModal">
    </div>
    <div class="time">原始图像<span></span><span>${new Date().toLocaleString()}</span></div>
    </div>`;
    $(".list-box").prepend(html);
}else{
    cocoMessage.error("用户识别失败，请重新尝试...")
}
//请求图像流资源
imgData.close()
imgData = new EventSource(linkData[0])
//对图像流返回的数据进行处理
imgData.onmessage = function (res) {
    let {result_image} = JSON.parse(res.data)
    $('#img_box>img').attr('src', 'data:image/jpeg;base64,${result_image}')
}, error: function(error){
    console.log(error);
    cocoMessage.error('接口调用失败，请重新尝试...')
//请求图像流资源
imgData.close()
imgData = new EventSource(linkData[0])
//对图像流返回的数据进行处理
imgData.onmessage = function (res) {
    let {result_image} = JSON.parse(res.data)
    $('#img_box>img').attr('src', 'data:image/jpeg;base64,${result_image}')
}
}
}});
```

## 6）人脸识别算法接口设计

人脸识别算法接口设计的代码如下：

```
###############################################################################
#文件： face_recognition.py
#说明： 人脸识别
###############################################################################
import numpy as np
import cv2 as cv
import os
import json
import base64
import copy
import math
import time
from PIL import Image, ImageDraw, ImageFont

c_dir = os.path.split(os.path.realpath(__file__))[0]

def load_json_file(name):
    jo = {}
    if os.path.exists(name):
        with open(name,"r") as f:jo = json.loads(f.read())
    return jo

def save_json_file(name, jo):
    with open(name,"w") as f:f.write(json.dumps(jo))

def image_to_base64(img):
```
