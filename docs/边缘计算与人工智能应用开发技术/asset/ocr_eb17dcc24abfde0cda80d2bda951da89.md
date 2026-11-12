| 参 数 | 示 例 |
|---|---|
| url | "/file/baidu_speech_recognition" |
| method | 'POST' |

# 续表

| 参 数 | 示 例 |
|---|---|
| processData | false |
| contentType | false |
| dataType | 'json' |
| data | let config = configData
let blob = recorder.getWAVBlob();
let formData = new FormData();
formData.set('file_name','blob','audio.wav');
formData.append('param_data', JSON.stringify({ "APP-ID":config.user.baidu_id,
"API_KEY":config.user.baidu_apikey, "SECRET_KEY":config.user.baidu_secretkey})); |
| success | function(res){}内容:
return_result = {'code': 200, 'msg': None, 'origin_image': None, 'result_image': None, 'result_data': None}
示例：
code/msg: 200 表示手势识别成功、500 表示手势识别失败
result_data: 返回手势识别的文本内容 |

（2）百度语音识别接口。辅助驾驶系统通过Ajax接口将音频数据传递给百度语音识别接口进行识别，百度语音识别接口是通过单次推理接口调用的。前端应用处理示例如下：