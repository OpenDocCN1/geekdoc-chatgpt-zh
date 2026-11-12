```
}
}
} else if(mac == SensorB) {
    //判断是否是 Sensor-B 节点的数据
    if(dat[0] == '{' && dat[dat.length - 1] == '}') {    //判断字符串首尾是否为{}
        dat = dat.substr(1, dat.length - 2);    //截取 {} 内的字符串
        var its = dat.split(',');    //以 '，' 来分割字符串
        for (var x in its) {    //循环遍历
            var t = its[x].split('=');    //以 '=' 来分割字符串
            if (t.length != 2) continue;    //满足条件时结束当前循环
            if (t[0] == "D1") {    //判断参数 D1
                var LightStatus = parseInt(t[1]);    //根据 D1 的值来进行开关的切换
                if ((LightStatus & 0x30) == 0x30) {
                    $("#btn_1_img").attr('src','images/an-on.png')
                } else if ((LightStatus & 0x30) == 0) {
                    $("#btn_1_img").attr('src','images/an-off.png')
                }
            }
        }
    }
});
/**********************************************************************************************/
* 处理按键事件
/**********************************************************************************************/
$("#btn_1_img").click(function() {
    if($("#btn_1_img").attr('src') == "images/an-on.png") {
        rtc.sendMessage(SensorB, "{CD1=48,D1=?}");   //发送关闭灯光命令
    } else {
        rtc.sendMessage(SensorB, "{OD1=48,D1=?}");   //发送打开灯光命令
    }
})
)
```

(2) 通过Chrome浏览器打开实时连接示例程序RTConnectDemo-Web\\index.html，查看实时数据信息及灯的开关操作。实时连接示例如图2.41所示。