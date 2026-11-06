| 属性名 | 意义 | 值 |
|---|---|---|
| animation-delay | 设置动画延时多久开始执行 | 与 animation-duration 属性一致 |
| animation-direction | 设置动画播放的方向 | normal: 始终正向播放
alternate: 轮流逆向播放 |
| animation-fill-mode | 设置动画执行完成后元素的状态 | 可选值：forwards，保持结束的状态；backwards，保持开始的状态；both，根据动画播放正逆保持 |

在默认情况下，动画执行完成后，执行动画的元素会还原到原始状态，如果需要元素保持动画执行完成后的状态，设置 animation-fill-mode 属性即可，上面属性的应用实例代码如下：

```
.move {
  animation-iteration-count:3;
  animation-name: move-animation;
  animation-duration: 3s;
  animation-timing-function: ease-in-out;
  animation-delay: 1;
  animation-direction: alternate;
  animation-fill-mode: both;
}
```

# 8.4.2 组件的形态变换与动画

在对组件的样式进行配置时，可以对组件的形态转换属性 transform 进行设置，使用 transform 属性可以对组件进行平移、旋转、缩放、翻转等形态的变换，并且配合关键帧，组件形态的变换也可以以动画的方式展现。

常用的形态变换方法有平移、旋转、缩放和翻转 4 种，如表 8-11 所示。

# 表 8-11 transform 相关属性列举

| 方法名 | 意义 | 参数 |
|---|---|---|
| translate | 进行平移变换 | 第 1 个参数：沿 x 轴平移多少单位
第 2 个参数：沿 y 轴平移多少单位 |
| rotate | 进行旋转变换 | 顺时针旋转角度 |
| scale | 进行缩放变换 | 第 1 个参数：宽度缩放比例
第 2 个参数：高度缩放比例 |
| skew | 进行翻转变换 | 第 1 个参数：按 x 轴旋转角度
第 2 个参数：按 y 轴旋转角度 |
| rotate3d | 进行三维旋转 | 第 1 个参数：x 轴旋转分量
第 2 个参数：y 轴旋转分量
第 3 个参数：z 轴旋转分量
第 4 个参数：旋转角度 |