```
"success",{button: false,timer: 2000});
}
}
setTimeout(() => {
gestureThrottle = true
}, 3000);
}
} else {
count[gesture] = 1
}
}
setTimeout(() => {
resultThrottle = true
}, 500);
}
}
}
```

（2）语音识别算法。智能家居系统通过Ajax接口将音频数据传递给百度语音识别接口进行语音识别，百度语音识别接口是通过单次推理接口调用的。百度语音识别接口的参数请参考表4.11。前端应用处理示例如下：