最后，调用表 5-32 所示的方法进行绘制。

# 表 5-32 进行绘制的方法

| 方法名 | 参数 | 意义 |
|---|---|---|
| draw | reserve: 是否逆向绘制
callback: 绘制完成后的回调函数 | 进行图形绘制 |
| drawImage | imageResource: 图像资源
sx: 裁取原图的左上角横坐标
sy: 裁取原图的左上角纵坐标
swidth: 裁取原图的宽度
sheight: 裁取原图的高度
dx: 绘制的左上角横坐标
dy: 绘制的左上角纵坐标
dwidth: 绘制的宽度
dheight: 绘制的高度 | 进行图像绘制 |

示例代码如下：

```
onLoad: function (options) {
//获取画布上下文对象
var context = wx.createCanvasContext("canvas", this);
//设置填充颜色
context.fillStyle = '#ff0000';
//设置填充图形
context.fillRect(0,0,100,100);
//设置绘制文字的字体大小
context.setFontSize(20);
//设置线条风格
context.setStrokeStyle("#00ff00");
//设置要填充绘制的文字
context.fillText("Hello",100,20,200);
//设置要描边绘制的文字
context.strokeText("World",100,100,200);
//定义绘制的图形
context.arc(200,200,100,3.14,0,0);
//设置填充颜色
context.fill();
//进行绘制
context.draw();
}
```

运行代码，效果如图 5-6 所示。