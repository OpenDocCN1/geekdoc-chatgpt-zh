# 4.1.2 text 组件

text 组件的主要作用是展示文本。在测试工程中新建一个 text 页面，在 text.wxml 文件中编写如下代码：

```
<!--pages/text/text.wxml-->
<text style="color:red;font-size:80rpx;">文本<text style="color:blue;font-size:100rpx;">文本内文本</text></text>
```

上述代码创建了一段简单的文本视图，效果如图 4-4 所示。

![](bbox=[0.38, 0.3, 0.57, 0.51])

图 4-4 text 组件的运行效果

text 组件中只能够嵌套 text 组件，通过 text 组件嵌套的方式可以在一段文本中设置不同风格的多段文本块。

默认文本组件只能够进行文本的显示，不可以进行交互，但可以添加 selectable 属性使其支持文本选中，选中文本后可以进行复制、查询等操作。

# 4.1.3 rich-text 组件

前面提及，text 组件中只能嵌套 text 组件，虽然可以通过 text 组件的 style 属性定制文本的展示样式，但是其功能依然有限，如无法在文本中插入图片、超链接等。小程序组件库中还提供了一个专门用来创建富文本的组件：rich-text 组件。

rich-text 组件允许在文本中插入常用的 HTML 节点，包括图片、段落、超链接等。rich-text 组件的使用也非常简单，可以通过定义一组 HTML 节点创建富文本组件。

在测试工程中新建一个 rich-text 页面，在 rich-text.wxml 文件中编写如下代码：

```
<!--pages/rich-text/rich-text.wxml-->
<rich-text nodes="{{nodes}}"></rich-text>
```

上面的代码设置了 rich-text 组件的 nodes 属性,这个属性对应的数据定义在 rich-text.js 文件中：