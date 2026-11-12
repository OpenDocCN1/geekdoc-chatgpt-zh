# 4) 算法交互

前端应用通过Ajax接口将需要进行语音合成的文本传递给百度语音合成接口，百度语音合成接口的参数如表4.14所示。

# 表4.14 百度语音合成接口的参数

| 参数 | 示例 |
|---|---|
| url | "/file/baidu_speech_synthesis" |
| method | "POST" |
| processData | false |
| contentType | false |
| dataType | 'json' |
| data | let config = configData
let text = $(this).parent('.item').find('textarea').val()
let blob = new Blob([text],{
 type:'text/plain'
 });
//语音合成播报
let formData = new FormData();
formData.append('file_name',blob,'text.txt');
formData.append('param data', JSON.stringify({"APP_ID":config.user.baidu_id,
"API_KEY":config.user.baidu_apikey,
"SECRET_KEY":config.user.baidu_secretkey})); |
| success | function(res){} 内容：
return_result = {'code': 200, 'msg': None, 'origin_image': None, 'result_image': None, 'result_data': None}
示例：
code/msg: 200 表示语音合成成功，500 表示语音合成失败。
result_data: 返回 base64 编码的音频文件 |

前端应用示例如下：