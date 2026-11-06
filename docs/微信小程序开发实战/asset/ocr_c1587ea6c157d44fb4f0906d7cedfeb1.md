app.wxss 用来定义一些全局的样式表，样式表的定义与 CSS 语法基本一致，后面会详细介绍。

除上面比较重要的几个文件外，模板工程中还生成了一个名为 pages 的文件夹，该文件夹中又包含 index 和 logs 两个文件夹。pages 文件夹用来存放页面文件，其中每个文件夹都是一个页面，如 index 文件夹存放的是示例程序的主页面相关代码，在微信小程序中，每个页面都由 JS、JSON、WXML 和 WXSS 这 4 种类型的文件共同定义。关于页面的相关内容，后面章节会具体介绍。

# 1.3.2 修改示例程序

下面将模板生成的示例程序修改为 HelloWorld 程序，先修改 index.wxml 文件，具体如下：

```
<view class="container">
  <text style='color:{{textColor}}'>HelloWorld</text>
  <button bindtap='changeColor'>变化颜色</button>
</view>
```

WXML 文件主要编写页面的骨架，上述代码在页面中定义了一个文本组件和一个按钮组件，并绑定了一个单击按钮的回调方法，修改 index.js 文件，具体如下：

```
Page({
  data: {
    textColor:'#ff0000',
  },
  //事件处理函数
  changeColor:function(){
    this.setData({
      textColor:this.randomColor(),
    });
  },
  randomColor: function() {
    var rand = Math.floor(Math.random() * 0xFFFFFF).toString(16);
    if (rand.length == 6) {
      return '#'+rand;
    } else {
      return getRandomColor();
    }
  },
  onLoad: function () {
  },
})
```

如上述代码所示，data 中定义了文本的颜色，默认为红色，当用户单击按钮时，会随机切换文本的颜色，小程序页面是由数据进行驱动的，当有数据改变时，开发者只需要调