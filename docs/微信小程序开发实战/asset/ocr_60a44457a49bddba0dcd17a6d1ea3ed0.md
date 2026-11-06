```
text:"hahahahahah",
    time:1,
    color:'#ff000'
  }, {
    text: "我是弹幕哦",
    time: 6,
    color: '#00ff00'
  }],
},
```

在 onLoad 方法中编写如下逻辑代码；

```
onLoad: function (options) {
  // 获取视频组件上下文对象
  var video = wx.createVideoContext("video", this);
  // 进行视频播放
  video.play();
  // 对弹幕进行设置
  video.sendDanmu({
    text:"hello",
    color:"#0000ff"
  });
}
```

运行代码，效果如图 5-3 所示。

![](bbox=[0.38, 0.51, 0.56, 0.71])

图 5-3  video 组件的运行效果

与 audio 组件相比，video 组件提供了更多的可配置属性，并且集成了播放弹幕的功能。video 组件的常用属性如表 5-8 所示。

表 5-8  video 组件的常用属性

| 属性名 | 意义 | 值 |
|---|---|---|
| src | 设置视频路径 | 字符串 |
| duration | 指定视频时长 | 数值 |
| controls | 是否显示视频控制组件，如播放按钮、暂停按钮、进度条等 | 布尔值 |