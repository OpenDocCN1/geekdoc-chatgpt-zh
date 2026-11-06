| 属性名 | 意义 | 值 |
|---|---|---|
| bindtimeupdate | 设置播放进度改变时的回调函数 | 函数 |
| bindfullscreenchange | 设置全屏模式改变时的回调函数 | 函数 |
| bindwaiting | 设置视频缓冲时的回调函数 | 函数 |
| bindererror | 设置播放异常时的回调函数 | 函数 |
| bindprogress | 设置加载进度变化时的回调函数 | 函数 |

video 组件支持大部分主流的视频格式，与 audio 组件一样，开发者也可以通过 VideoContext 上下文对象控制 video 组件的行为，VideoContext 对象可调用的方法如表 5-9 所示。

# 表 5-9 VideoContext 对象可调用的方法

| 方法名 | 参数 | 意义 |
|---|---|---|
| play | 无 | 进行视频播放 |
| pause | 无 | 暂停视频播放 |
| stop | 无 | 停止视频播放 |
| seek | 数值 | 设置播放位置 |
| sendDanmu | 弹幕对象：
{
text:内容
color:颜色
} | 立即发送一条弹幕 |
| playbackRate | 数值：0.5、0.8、1.9、1.25、1.5 | 设置播放倍速 |
| requestFullScreen | 视频方向：0，正常竖向；90，逆时针旋转 90°；-90，顺时针旋转 90° | 进入全屏播放 |
| exitFullScreen | 无 | 退出全屏播放 |
| showStatusBar | 无 | 显示状态栏，只在 iOS 全屏模式下有效 |
| hideStatusBar | 无 | 隐藏状态栏，只在 iOS 全屏模式下有效 |

# 5.2.4 camera 组件

camera 组件用来调用设备的相机功能。使用 camera，可以非常方便地实现拍照、拍视频、扫码等定制化的多媒体相关功能。新建立一个命名为 camera 的测试页面，编写如下测试代码：

```
<!--pages/camera/camera.wxml-->
<!--定义外层容器-->
<view style='width:100%;height:400rpx;text-align:center'>
<!--定义相机组件-->
<camera style='width:100%;height:80%'></camera>
</view>
```