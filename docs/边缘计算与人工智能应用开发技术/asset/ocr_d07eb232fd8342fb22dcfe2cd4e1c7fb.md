```
});
    $("#video").click()
}
})

//人脸识别
$("#result").click(function () {
    imgData && imgData.close()
    let img = $("#img").attr('src');           //获取当前视频图像
    let blob = dataURLtoBlob(img);           //转换为 Blob 格式

    var formData = new FormData();
    formData.append('file_name', blob, 'image.png');
    //type=1 表示人脸识别
    formData.append('param_data', JSON.stringify({
        "APP_ID": user.baidu_id,
        "API_KEY": user.baidu_apikey,
        "SECRET_KEY": user.baidu_secretkey,
        "type": 1
    }));
    $.ajax({
        url: user.edge_addr + '/file/baidu_face_recognition',
        method: 'POST',
        processData: false,
        contentType: false,
        dataType: 'json',    //必需的
        data: formData,
        success: function (res) {
            console.log(res);
            let time = new Date().toLocaleTimeString();    //获取当前时间
            //拼接当前时间与注册结果
            let html = '<div>{time}----------${JSON.stringify(res)}</div>'
            $("#info_list").html(html)    //插入页面响应节点
            if (res.code == 200) {
                let img = 'data:image/jpeg;base64,' + res.result_image;
                $("#register").attr('disabled',true)
                $("#img").attr('src', img)    //识别成功后，页面显示识别结果图像
            } else {
                $("#video").click()    //返回实时视频
            }
        },
        error: function (error) {
            console.log(error);
            $("#video").click()    //返回实时视频
        }
    });
});
```