# 2.2.1 认识 WXML

一个完整的 WXML 标签由开始标签和结束标签组成。在开始标签和结束标签之间可以编写标签的内容，也可以嵌套其他标签，以之前创建的 HelloWorld 小程序为例，其中 index.wxml 文件中的代码如下：

```
<!--index.wxml-->
<view class="container">
  <text style="color:{{textColor}}">HelloWorld</text>
  <button bindtap='changeColor'>变化颜色</button>
</view>
```

下面逐步解析上面的 5 行代码：<!--内容-->是 WXML 注释的写法，其中的内容并不会对程序产生任何影响，其作用只是帮助开发者阅读代码。index.wxml 文件定义了 3 个组件，最外层使用 view 组件作为容器，并将其 class 属性设置为 container，container 实际上是 WXSS 样式表中定义的一个全局的容器样式。

在 view 容器内部，我们又嵌套了一个 text 组件和一个 button 组件。text 组件用来显示文本，其中，style 属性可以设置文本的颜色、字体、字号等属性；button 组件用来渲染可以进行用户交互的按钮，其中，bindtap 属性用来绑定按钮单击方法。

每种标签都可以拥有其独特的属性，属性的设置一般都是通过 key="value" 的方式在一组标签的开始标签中定义的。

# 2.2.2 将数据绑定到 WXML 界面中

小程序采用了响应式编程的设计思路，页面的展示是由数据进行驱动的，也就是说，如果数据产生变化，页面也会直接根据数据重新渲染。WXML 提供了数据绑定的功能。

以 HelloWorld 程序为例，其中，文本标签显示的文本 “HelloWorld” 是写在 text 组件内部的，如果需要动态改变这个文本的值，那么需要将其定义在 index.js 文件中，并绑定到 index.wxml 的 text 组件中，先修改 index.js 文件中页面函数中 data 属性的值，示例代码如下：

```
data: {
  textColor:'#ff0000',
  textData:'Hi,小程序'
}
```

页面所需要的数据通常定义在 data 属性中，这个属性的更改可以直接触发页面的重绘。修改 index.wxml 文件中的代码，示例代码如下：

```
<!--index.wxml-->
<view class="container">
  <text style='color:{{textColor}}'>{{textData}}</text>
  <button bindtap='changeColor'>变化颜色</button>
</view>
```