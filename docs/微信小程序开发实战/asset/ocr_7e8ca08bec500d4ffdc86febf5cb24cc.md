# 4.1.4 progress 组件

progress 组件用来创建进度条视图，其使用非常简单，并且提供了丰富的可定制化的属性，示例代码如下：

```
<!--pages/progress/progress.wxml-->
<progress style='margin:50rpx;' 
percent='50' 
show-info='{{true}}' 
stroke-width='20'>
</progress>
```

运行代码，效果如图 4-6 所示。

![](bbox=[0.43, 0.34, 0.62, 0.57])

图 4-6 progress 组件的运行效果

progress 组件的常用属性如表 4-3 所示。

# 表 4-3 progress 组件的常用属性

| 属性名 | 意义 | 值类型 |
|---|---|---|
| percent | 设置进度条当前进度百分比 | 数值，0～100 |
| show-info | 设置是否在进度条右侧显示进度百分比信息 | 布尔值 |
| border-radius | 设置进度条圆角，此属性在模拟器上可能会出现异常 | 数值 |
| font-size | 设置百分比文字的字体大小 | 数值 |
| stroke-width | 设置进度条宽度 | 数值，默认为 6px |
| activeColor | 设置进度条已完成部分的顏色 | 颜色值 |
| backgroundColor | 设置进度条背景色 | 颜色值 |
| active | 设置进度条在渲染时是否展示从左向右的动画 | 布尔值 |
| bindactiveend | 设置动画播放完成后的回调函数 | 函数 |