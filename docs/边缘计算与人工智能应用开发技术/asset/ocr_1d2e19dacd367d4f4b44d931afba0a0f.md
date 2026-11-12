(3) 添加算法。智能家居系统用到了手势识别、人体检测、百度语音识别等算法，需要在aicam工程添加手势识别算法文件 algorithm/handpose_detection/handpose_detection.py、人体检测算法文件algorithm/person_detection/person_detection.py、语音识别算法文件 algorithm/baidu_speech_recognition/baidu_speech_recognition.py。

(4) 添加应用。在aicam工程添加算法项目前端应用 static/edge_smarthome。

# 3) 算法交互

(1) 手势识别算法。智能家居系统采用EventSource接口获取处理后的视频流，通过实时推理接口调用手势识别算法来识别视频流中的手势，返回base64编码的结果图像和结果数据。前端应用处理示例如下：

```
/****************************************************************************
 * 名称： gestureRecognition
 * 功能： 识别视频流中的手势，对识别到的手势次数进行累计，当累计到 4 次时向相应的家居
```