# 微信小程序开发实战

@keyframes 是一个关键字，用来定义关键帧动画，其后跟着的字符串为定义的关键帧动画的名称，上面定义了两个关键帧动画，分别命名为 color-animation 和 move-animation。在关键帧的定义中，使用百分比的方式确定关键节点，以 color-animation 关键帧为例，其中定义了当动画开始时背景色为红色，当动画执行到 25%时，背景色完全渐变为黄色，当动画执行到 50%时，背景色完全渐变为蓝色，当动画执行完成时背景色最终渐变为绿色。

创建了关键帧之后，我们还需要将其绑定到指定的选择器上，如上述代码所示，将 color-animation 关键帧绑定到 color 类选择器上，将 move-animation 关键帧绑定到 move 类选择器上，当某个组件需要执行动画时，只需要将其 class 属性设置为绑定了关键帧动画的类即可。

下面在 css-animation.js 文件中实现按钮的绑定函数，具体如下：

```
//pages/css-animation/css-animation.js
Page({
  data:{
    ori:'block',
  },
  move:function(){
    this.setData({
      ori:'move block'
    });
  },
  color:function(){
    this.setData({
      ori: 'color block'
    });
  }
})
```

运行代码，单击相应按钮可以看到色块在页面上的动画效果。

需要注意的是，上面代码对选择器绑定关键帧时，设置的是 animation 属性，这个属性是一个复合的属性，可以拆成多个属性分别进行设置，如表 8-10 所示。

表 8-10 Animation 对象可配置属性

| 属性名 | 意义 | 值 |
|---|---|---|
| animation-name | 设置要绑定的关键帧名称，即 @keyframes 所定义的关键帧名字 | 关键帧名称 |
| animation-duration | 设置动画时长 | 时长，以 s 结尾表明单位为 s，以 ms 结尾表明单位为 ms |
| animation-timing-function | 设置动画执行的时间速度函数 | 可选值：linear，线性，即匀速执行；ease，渐进，逐渐加速逐渐减速；ease-in，渐入，即先慢后快；ease-out，渐出，即先快后慢；ease-in-out，渐入渐出 |