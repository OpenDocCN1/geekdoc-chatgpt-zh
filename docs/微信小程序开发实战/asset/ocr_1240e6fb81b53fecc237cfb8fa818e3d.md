当用户与应用程序进行交互时，通常会产生界面的变化。例如，用户点击某个按钮会影响按钮的背景色，用户的手指滑动操作会使页面元素的位置发生变化，等等。一般情况下，页面的变化都是瞬时完成的，突然的变化往往会使用户产生突兀的感觉，如果配合使用动画，则会使页面变化过渡自然，用户体验也会更加舒适。

# 8.4.1 定义关键帧

通过 WXSS 定义动画的一种重要方式是定义关键帧。关键帧动画是指在元素变化过程中可以定义几个关键节点，如背景色变化的动画，可以定义几个关键节点的背景色，之后将定义好的关键帧绑定到选择器，将选择器添加到组件上即可触发动画。

在测试工程中新建一个命名为 css-animation 的页面，在 css-animation.wxml 文件中编写如下测试代码：

```
<!--pages/css-animation/css-animation.wxml-->
<view class="{{ori}}"></view>
<button bindtap='move'>移动动画</button>
<button bindtap='color'>背景色动画</button>
```

上面的代码创建了两个功能按钮，这两个功能按钮用于演示元素的位置移动动画和背景色动画，并且都将采用关键帧的方式进行定义。上面的 view 组件是用来演示动画的色块。

在 css-animation.wxss 文件中编写如下代码：

```
/*pages/css-animation/css-animation.wxss*/
.block {
  width: 100rpx;
  height:100rpx;
  background-color: red;
}
.color {
  animation: color-animation 3s;
}
.move {
  animation: move-animation 3s;
}
@keyframes color-animation {
  0% {background: red;}
  25% {background: yellow;}
  50% {background: blue;}
  100% {background: green;}
}
@keyframes move-animation {
  0% {margin-left: 0rpx;}
  100% {margin-left: 200rpx;}
}
```