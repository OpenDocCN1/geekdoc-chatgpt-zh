运行代码，效果如图 4-20 所示。

![](bbox=[0.45, 0.13, 0.64, 0.35])

图 4-20  地区选择器的运行效果

地区选择器的常用属性如表 4-17 所示。

表 4-17  地区选择器的常用属性

| 属性名 | 意义 | 值 |
|---|---|---|
| value | 设置组件的值，数组中需要设置选中的省、市、区字符串 | 数组 |
| custom-item | 为每列的最上面增加一个自定义的选项，如设置为“暂不选择” | 字符串 |
| bindchange | 设置组件值改变回调的函数 | 函数 |
| bindcancel | 设置用户单击“取消”按钮回调的函数 | 函数 |
| disabled | 设置是否禁用组件 | 布尔值 |

# 4.3.6 选择器视图

通过上面的介绍可知，picker 组件是系统封装好的一个弹窗组件。小程序组件库中也提供了直接定制选择器视图的组件：picker-view。

picker-view 是一个直接显示在页面上的选择器视图，其内只能嵌套 picker-view-column 组件，picker-view-column 用来描述选择器视图中的一列，其内可以放置其他任意组件，也可以循环的方式放人一组子组件。示例代码如下：

```
<!--pages/picker-view/picker-view.wxml-->
<picker-view style='height:200rpx;'>
  <picker-view-column>
    <icon type='warn'></icon>
    <view>哈哈</view>
    <view>哈哈哈</view>
  </picker-view-column>
  <picker-view-column>
    <view>你</view>
```