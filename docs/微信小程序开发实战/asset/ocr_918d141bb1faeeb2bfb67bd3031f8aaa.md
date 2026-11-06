# 微信小程序开发实战

```
<text wx:if="{{false}}" style='color:{{textColor}}'>{{textData + "! 一起来学习吧"}}</text>
```

上面的示例代码将 wx:if 属性对应的值设置为 false，刷新模拟器可以看到文本标签将不会被渲染，在通常情况下，wx:if 属性绑定的数据是一个条件表达式，示例代码如下：

```
<text wx:if="{{textData.length>0}}" style='color:{{textColor}}'> {{textData + "! 一起来学习吧"}}</text>
```

wx:if 属性也可以和 wx:elif、wx:else 结合使用，从而实现更复杂的条件渲染逻辑，示例代码如下：

```
<text wx:if="{{textData.length>3}}" style='color:{{textColor}}'> {{textData + "! 一起来学习吧"}}</text>
<text wx:elif="{{textData.length>0}}" style='color:{{textColor}}'> HelloWorld</text>
<text wx:else>无数据</text>
```

上面的代码会判定 textData 数据的长度，大于 3 个字符则在页面上显示 textData 数据拼接上 “! 一起来学习吧”，如果不大于 3 个字符但大于 0 个字符，则在页面显示 textData 的值，否则在页面显示 “无数据”。

如果需要同时对多个标签进行条件渲染，则可以使用 block 标签进行包裹，然后控制 block 标签的渲染条件即可，示例代码如下：

```
<block wx:if="{{textData.length>0}}">
<text style='color:{{textColor}}'>{{textData + "! 一起来学习吧"}}</text>
<text>END</text>
</block>
```

在学习 JavaScript 的语法时，我们了解到，分支和循环是程序流程控制的两大逻辑。对于 WXML 的渲染也是这样，WXML 也支持循环渲染标签，这是小程序简单列表的基本开发方式，使用 wx:for 实现对数组的遍历，并循环渲染标签内部内容，首先在页面数据源中定义一个数据，示例代码如下：

```
data: {
    textColor:'#ff0000',
    textData:'Hi,小程序',
    listData:["JavaScript","Python","C++","Ruby"],
}
```

修改 index.wxml 文件，示例代码如下：

```
<!--index.wxml-->
<view class="container">
    <text wx:for="{{listData}}">
        {{index}}:{{item}}
    </text>
</view>
```

wx:for 属性会对数据进行遍历，并且自动生成两个临时变量：index 和 item。index 为当前遍历数据元素的下标值，从 0 开始；item 为当前遍历出的数据元素。运行上面的代码，