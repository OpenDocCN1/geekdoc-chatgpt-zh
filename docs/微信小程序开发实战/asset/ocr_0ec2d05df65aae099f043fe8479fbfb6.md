# 12.2.3 悬浮按钮组件的封装

扫码录入图书功能可以采用悬浮按钮的方式进行呈现。由于很多页面会使用悬浮按钮，所以可以将其封装为组件。

在工程的根目录下新建一个命名为 components 的文件夹，在其中新建一个命名为 float-button 的组件，并在 float-button.wxml 文件中编写如下代码：

```
<!--components/float-button.wxml-->
<view class='float-button' hover-class='hover' bindtap='click'>
<text class='content'><slot></slot></text>
</view>
```

对应的样式表如下：

```
/*components/float-button.wxss*/
.float-button {
  background-color: #444444;
  z-index: 9999;
  position: fixed;
  width: 120rpx;
  height: 120rpx;
  border-radius: 60rpx;
  text-align: center;
  padding: 0;
  right: 60rpx;
  bottom: 60rpx;
  box-shadow: 3px 3px 5px 1px #aaaaaa;
}
.content {
  padding: 0;
  margin: 0;
  color: white;
  font-size: 30px;
  text-align: center;
  line-height: 120rpx;
  font-weight: bold;
}
.hover {
  background-color: #000000;
}
```

当用户点击悬浮按钮后，需要将事件传递到原页面进行处理，在 float-button.js 文件中实现如下方法：

```
//components/float-button.js
Component({
  click: function() {
    this.triggerEvent("tap");
  }
})
```