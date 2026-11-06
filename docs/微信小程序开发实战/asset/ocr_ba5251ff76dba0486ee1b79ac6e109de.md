# 微信小程序开发实战

```
data: {
  //进行节点数据的定义
  nodes:[
    {
      //定义节点的类型
      type:"node",
      //定义节点的名称
      name:"h1",
      //定义节点的属性
      attrs:{
        style:"color: red;"
      },
      children: [{
        type: 'text',
        text: 'HelloWorld!'
      }]
    },
    {
      type:"text",
      text:"Hi"
    }
  ],
}
```

运行代码，效果如图 4-5 所示。

![](bbox=[0.45, 0.51, 0.62, 0.71])

图 4-5 rich-text 组件的运行效果

如上述代码所示，rich-text 组件通过一组节点进行定义，节点由对象描述，节点对象可配置的属性如表 4-1 所示。

表 4-1 节点对象可配置的属性

| 属性 | 意义 | 值 |
|---|---|---|
| name | 节点名称 | HTML 标签名 |
| attrs | 节点属性 | 设置 HTML 标签的属性、对象类型 |
| children | 子节点列表 | 子节点数组 |
| type | 设置节点的类型 | 可选值: node, text. 其中, node 为元素节点, text 为文本节点 |