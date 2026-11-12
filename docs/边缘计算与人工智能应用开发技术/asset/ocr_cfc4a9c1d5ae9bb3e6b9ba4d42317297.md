(2) 添加模型。在aicam工程添加模型文件
models/plate_recognition、车牌检测模型文件
det3.bin/det3.param、车牌对齐模型文件lffd.bin/lffd.param、颜色识别模型文件lpc.bin/lpc.param、车牌识别模型文件
lpr.bin/lpr.param。

(3) 添加算法。在aicam工程添加基于深度学习的车牌识别算法
algorithm/plate_recognition/plate_recognition.py。

(4) 添加应用。在aicam工程添加算法项目前端应用
static/edge_plate。

# 3) 硬件通信设计

前端应用中的硬件控制部分通过智云ZCloud API连接到硬件系统，前端应用处理示例如下：

```
getConnect()
//智云服务连接
function getConnect(){    //建立连接服务的函数
    rtc = new WSNRTCConnect(config.user.id, config.user.key)
    rtc.setServerAddr(config.user.addr);
    rtc.connect();
    rtc.onConnect = () => {       //连接成功回调函数
        online = true
        setTimeout(() => {
            if(online){
                cocoMessage.success('数据服务连接成功！ 查询数据中...')
                rtc.sendMessage(config.macList.mac_604, {'V3=1,V3=?}');  //发起数据查询
            } }, 200);
    }
    rtc.onConnectLost = () => {   //数据服务掉线回调函数
        online = false
        cocoMessage.error('数据服务连接失败!请检查网络或 IDKEY...')
    };
}
```

# 4) 算法交互