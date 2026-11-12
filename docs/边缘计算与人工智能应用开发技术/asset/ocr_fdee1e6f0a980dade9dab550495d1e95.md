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
    $('#img_box>img').attr('src', 'data:image/jpeg;base64,${result_image}')

    if (interactionThrottle) {
        interactionThrottle = false
        let {result_data} = JSON.parse(res.data)
        let html = `<div>${new Date().toLocaleTimeString()}————${JSON.stringify(result_data)}
</div>'
        $('#text-list').prepend(html);
        console.log(result_data);
        //只有成功连接智云服务（online），且不处于节流（throttle）状态，才能进行判断
        if (result_data.obj_num > 0 && throttle && online) {
            throttle = false
            //识别到人脸且 name 不为 unknown，表示已注册用户，发送开启闸门命令
            if(result_data.obj_list[0].name != 'unknown'){
                cocoMessage.success('用户：${result_data.obj_list[0].name},!门锁开启中，请稍等...')
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

# 5) 百度人脸识别算法交互