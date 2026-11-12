```
if(result.code==200) {
    let img = 'data:image/jpeg;base64, ' + result.origin_image;
    let html = `<div class="img-li">
    <div class="img-box">
    <img src="$ {img}" alt="" data-toggle="modal" data-target="#myModal">
    </div>
    <div class="time">原始图像<span></span><span>$ {new
    Date().toLocaleString()}</span></div>
    </div>`;
    $(".list-box").prepend(html);

    let img1 = 'data:image/jpeg;base64, ' + result.result_image;
    let html1 = `<div class="img-li">
    <div class="img-box">
    <img src="$ {img1}" alt="" data-toggle="modal" data-target="#myModal">
    </div>
    <div class="time">识别结果<span></span><span>$ {new
    Date().toLocaleString()}</span></div>
    </div>`;
    $(".list-box").prepend(html1);
    //将识别到的人脸文本信息渲染到页面上
    let text = result.result_data.result.face_list[0].user_list[0].user_id
    let html2 = `<div>$ {new Date().toLocaleTimeString()} ——
    识别结果： $ {text}</div>`;
    $("#text-list").prepend(html2);
    //返回成功的识别结果则开启闸门
    if(result.result_image && online){
    cocoMessage.success(`用户： $ {text}!,门锁开启中、请稍等...`)
    rtc.sendMessage(config.macList.mac_602, config.sensor.mac_602.doorOpen);
    }
} else if(result.code==404){
    cocoMessage.error("用户识别失败，请重新尝试...")
    let img = 'data:image/jpeg;base64, ' + result.origin_image;
    let html = `<div class="img-li">
    <div class="img-box">
    <img src="$ {img}" alt="" data-toggle="modal" data-target="#myModal">
    </div>
    <div class="time">原始图像<span></span><span>$ {new
    Date().toLocaleString()}</span></div>
    </div>`;
    $(".list-box").prepend(html);
}else{
    cocoMessage.error("用户识别失败，请重新尝试...")
}
//请求图像流资源
imgData.close()
imgData = new EventSource(linkData[0])
//对图像流返回的数据进行处理
```