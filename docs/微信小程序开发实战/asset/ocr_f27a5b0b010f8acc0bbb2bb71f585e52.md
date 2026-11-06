# 4.1.5 button 组件

button 是小程序组件库提供的一个封装完善的按钮组件，其实，在小程序开发中，并非只有 button 组件才能作为用户交互的按钮，自定义的 view 组件都可以通过绑定用户交互事件的方式接收用户的操作。除提供最基础的交互功能外，button 组件还封装了许多微信官方提供的服务接口，如进行用户登录、进行当前页面的分享等。

在测试工程中新建一个 button 页面，在 button.wxml 文件中编写如下代码：

```
<!--pages/button/button.wxml-->
<button style='margin:30rpx;' 
type='warn'
size='default'
plain='{{true}}'
loading='{{true}}'
open-type='share'>
警告按钮
</button>
```

运行代码，效果如图 4-7 所示。

![](bbox=[0.38, 0.46, 0.57, 0.67])

图 4-7 button 组件的运行效果

button 组件的 size 属性用来设置组件的尺寸，可选值有 default 和 mini，default 为默认尺寸的按钮组件，mini 为小尺寸按钮。type 属性设置按钮的风格，风格会影响按钮的渲染颜色：primary 风格的按钮会被渲染为绿色，default 风格的按钮会被渲染为白色，warn 风格的按钮会被渲染为红色。

plain 属性用来设置按钮的背景色是否透明。除了这些属性，button 组件还可配置其他属性，如表 4-4 所示。