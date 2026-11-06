```
nodes:array
});
console.log(node);
}
}
```

上述代码将 custom-list 与 custom-list-item 建立父子关系，当关系建立完成后，在 ready 方法中可以通过组件的 getRelationNodes 方法获取所关联的组件，getRelationNodes 方法会返回一个数组，将关联的组件有序地提供给我们。

新建一个名为 custom-list-page 的页面，先在 custom-list-page.json 文件中引入要使用的自定义组件，具体如下：

```
{
  "usingComponents": {
    "custom-list": "/components/custom-list/custom-list",
    "custom-list-item": "/components/custom-list/custom-list-item"
  }
}
```

在 custom-list-page.wxml 文件中编写如下测试代码：

```
<!--pages/custom-list-page/custom-list-page.wxml-->
<custom-list>
<custom-list-item></custom-list-item>
<custom-list-item></custom-list-item>
<custom-list-item></custom-list-item>
<custom-list-item></custom-list-item>
<text>不是 ITEM 组件</text>
</custom-list>
```

运行上述代码，可以看到界面渲染出的列表效果。为组件建立关系，也是组件间通信的一种高效方式。

# 6.4.2 使用数据监听器

数据监听器并非一个陌生的概念，在介绍外部数据时，曾提及在定义数据时可以设置其 observer 监听函数，当这个数据发生改变时，监听函数就会被调用。observer 是另一种进行数据监听的方法，其更加通用、更加灵活。

以之前编写的 custom-button 组件为例，修改其 custom-button.js 文件，具体如下：

```
//components/custom-button/custom-button.js
Component({
  /**
   *组件的属性列表
   */
  properties: {
    buttonTitle:{
      type:String,
      value:"按钮",
```