```
let its = dat.slice(1,-1).split(';')
for (let i = 0; i < its.length; i++) { //循环遍历数组的每一个值
    let t = its[i].split("="); //将每个值以 "=" 分割为数组
    if (t.length != 2) continue;
    //mac_603 安防类
    if (mac == config.macList.mac_603) {
    if(t[0] == 'A5'){ //光栅传感器的命令
        console.log('光栅状态：' ,t);
        //此处调用 AiCam 接口进行人体识别
    }
    }
}
}
}
}
}
```

# 4) 算法交互

前端应用的算法采用RESTful接口获取处理后的视频流，返回base64编码的结果图像和结果数据。访问URL地址的格式如下（IP地址为边缘计算网关的地址）：

```
http://192.168.100.200:4001/stream/[algorithm_name]?camera_id=0
```

前端应用处理示例如下：