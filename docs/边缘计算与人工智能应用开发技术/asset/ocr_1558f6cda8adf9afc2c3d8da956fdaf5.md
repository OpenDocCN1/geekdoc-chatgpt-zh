```
console.log(result_data.obj_list[0].name);
throttle = false
rtc.sendMessage(config.macList.mac_602, config.sensor.mac_602.fanClose)
cocoMessage.success('识别到手势 1, 关闭风扇！')
setTimeout(() => {
throttle = true
}, 5000);
}
if (result_data.obj_list[0].name === 'five' && result_data.obj_list[0].score > 0.95
&& $("#icon").attr('src').indexOf("icon-off") > -1) {
console.log(result_data.obj_list[0].name);
throttle = false
rtc.sendMessage(config.macList.mac_602, config.sensor.mac_602.fanOpen)
cocoMessage.success('识别到手势 5, 开启风扇！')
setTimeout(() => {
throttle = true
}, 5000);
}
}
setTimeout(() => {
interactionThrottle = true
}, 1000);
}
}
```

# 5）手势开关风扇算法接口设计