```
```javascript
rtc.sendMessage(config.macList.mac_601,config.sensor.mac_601.query);
    }
}, 200);
}
rtc.onConnectLost = () => {            //数据服务掉线回调函数
    online = false
    cocoMessage.error('数据服务连接失败!请检查网络或 IDKEY...')
};

rtc.onmessageArrive = (mac, dat) => {         //消息处理回调函数
    if(dat[0] == '{' && dat[dat.length - 1] == '}') {
    //截取后台返回的 JSON 对象 （去掉{}符号） 后，以 “，”分割为数组
    let its = dat.slice(1,-1).split(',')
    for (let i = 0; i < its.length; i++) {      //循环遍历数组的每一个个值
    let t = its[i].split('=');                  //将每个值以 “=” 分割为数组
    if (t.length != 2) continue;
    //mac_601 采集类传感器
    if (mac == config.macList.mac_601) {
    if (t[0] == 'A0') {           //温度
    console.log('温度： ' ,t);
    $('.item span:eq(0)').text(t[1])
    temperature = t[1]
    }
    if (t[0] == 'A1') {   //湿度
    console.log('湿度： ' ,t);
    $('.item span:eq(1)').text(t[1])
    humidity = t[1]
    setTimeout(() => {
    if(audioAuthority & temperature) {
    audioAuthority = false
    let html = `<div class="msg"><div>${new
    Date().toLocaleTimeString()}——
    当前温度${temperature}℃，
    湿度${humidity}%</div></div>`'
    $('#message_box').append(html)
    $('#message_box').scrollTop($('#message_box')[0].scrollHeight);
    voiceSynthesis(`当前温度${temperature}度，
    湿度百分之${humidity}`)
    setTimeout(() => {
    audioAuthority = true
    }, 5000);
    }
    }, 500);
    }
    }
    }
    }
}
```
```