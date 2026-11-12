```
count[gesture] += 1
if (count[gesture] == 4 && gesture != 'undefined') {
    count = {}
    gestureThrottle = false
    let index
    if (gesture == 'one') index = 1
    if (gesture == 'two') index = 2
    if (gesture == 'three') index = 3
    //if (gesture == 'four') index = 4
    //if (gesture == 'five') index = 5
    console.log(gesture, index);
    if (index) {
        if (this.data602[index - 1].value == '已关闭') {
            //发送开关命令
            rtc.sendMessage(this.config.macList.mac_602,
                `{OD1=${this.data602[index - 1].val},D1=?}`);
            swal(`识别到手势${index},打开${this.data602[index - 1].name}! `,
                "", "success", { button: false, timer: 2000 });
        } else {
            //发送开关命令
            rtc.sendMessage(this.config.macList.mac_602,
                `{CD1=${this.data602[index - 1].val},D1=?}`);
            swal(`识别到手势${index},关闭${this.data602[index - 1].name}! `,
                "", "success", { button: false, timer: 2000 });
        }
    }
    setTimeout(() => {
        gestureThrottle = true
    }, 3000);
} else {
    count[gesture] = 1
}
}
setTimeout(() => {
    resultThrottle = true
}, 500);
},
},
```

手势识别算法的参数如表5.3所示。

# 表5.3 手势识别算法的参数