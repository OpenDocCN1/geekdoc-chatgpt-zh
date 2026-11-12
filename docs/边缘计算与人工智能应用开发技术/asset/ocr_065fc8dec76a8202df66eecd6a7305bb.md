```
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

# 6）人脸识别算法接口设计

人脸识别算法接口设计的代码如下：