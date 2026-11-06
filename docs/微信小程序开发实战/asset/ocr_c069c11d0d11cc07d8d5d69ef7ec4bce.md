# 5.1 导航组件

学习导航组件之前，应先明确一个新的概念：导航。导航用来管理应用页面之间的跳转逻辑。在小程序开发中，页面的跳转通常有两种方式：一种是使用导航组件创建导航视图控件，当用户与组件进行交互时触发页面的跳转；另一种方式是使用 JavaScript 调用系统提供的方法，直接进行页面的跳转。

# 5.1.1 navigator 导航组件

本章继续使用第 4 章创建的测试工程，新建一个 navigator 页面，在其中编写如下测试代码：

```
<!--pages/navigator/navigator.wxml-->
<navigator url='/pages/switch/switch'>
跳转到 switch 界面
</navigator>
```

上述代码在页面上创建了一个导航组件，当用户单击导航组件时，将跳转到另外一个测试界面。其中，url 属性需要设置为要跳转的界面路径。

navigator 组件的常用属性如表 5-1 所示。

表 5-1 navigator 组件的常用属性

| 属性名 | 意义 | 值 |
|---|---|---|
| target | 设置跳转目标 | 可选值：self，打开当前小程序中的页面；miniProgram，跳转到其他小程序中的页面 |
| url | 设置跳转到当前小程序中的页面路径 | 字符串 |
| open-type | 设置跳转方式，后面会介绍 | 字符串 |
| delta | 设置回退层数 | 当 open-type 属性设置为 navigateBack 时，这个属性设置要返回的层数 |
| app-id | 设置打开其他小程序的小程序 id | 字符串，当 target 设置为 miniProgram 时有效 |
| path | 设置打开其他小程序的页面路径 | 字符串 |
| extra-data | 设置打开其他小程序传递的参数 | 对象 |
| version | 设置打开其他小程序的小程序版本 | 可选值：develop，开发版；trial：体验版；release，正式版 |
| bindsuccess | 绑定跳转其他小程序成功后的回调函数 | 函数 |