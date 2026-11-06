```
city:[
  ["省份 1","省份 2"],
  ["城市 1","城市 2"],
],
currentCity:[0,1]
```

多列选择器使用二维数组的方式组织数据，其中，每个数组定义选择器中每列的所有选项，currentCity 用来标记每列的选中位置。在 picker.wxml 文件中添加组件标签，示例代码如下：

```
<picker mode="multiSelector" range='{{city}}' value='{{currentCity}}' bindchange='changeCity'>
  地址选择：{{city[0][currentCity[0]]}},{{city[1][currentCity[1]]}}
</picker>
```

运行代码，效果如图 4-17 所示。

![](bbox=[0.35, 0.34, 0.55, 0.54])

图 4-17 多列选择器的运行效果

多列模式下的选择器组件的常用属性如表 4-14 所示。

# 表 4-14 多列模式下的选择器组件的常用属性

| 属性名 | 意义 | 值 |
|---|---|---|
| range | 提供数据源 | 二维数组 |
| range-key | 当提供的数据为对象时，指定对象的某个属性作为选项的值 | 字符串 |
| value | 设置组件的值，数组中的每一项表示这一列选中的选项位置 | 数组 |
| bindchange | 设置组件值改变回调的方法 | 函数 |
| bindcolumnchange | 设置某一列的值改变回调的方法 | 函数 |
| bindcancel | 设置当用户单击“取消”按钮后回调的函数 | 函数 |
| disabled | 设置是否禁用组件 | 布尔值 |