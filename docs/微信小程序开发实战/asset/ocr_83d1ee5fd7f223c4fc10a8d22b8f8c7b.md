# 表 2-6 常用背景设置相关属性

| 属性名 | 意义 | 值 |
|---|---|---|
| background-color | 设置背景色 | 颜色值 |
| background-image | 设置背景图片 | 图片地址 URL |
| background-repeat | 设置图片背景的重复方式 | repeat: 背景图像将在垂直方向和水平方向重复
repeat-x: 背景图像将在水平方向重复
repeat-y: 背景图像将在垂直方向重复
no-repeat: 背景图像将仅显示一次 |
| background-position | 设置如何定位图片 | 水平和垂直方向分别有 top、left、right、bottom、center 5 个关键字可用，在设置时，可以组合使用，也可以使 用百分比或像素的方式自定义图片显示位置 |
| background-attachment | 设置背景是否随页面滚动 | scroll: 背景随页面滚动
fixed: 背景不随页面滚动 |

# 2.3.4 WXSS 文本相关属性

文本相关属性用来对元素中的文字进行设置，其可以对文字的字体、字号、颜色、对齐方式、缩进等属性进行配置。文本相关属性如表 2-7 所示。

# 表 2-7 文本相关属性

| 属性名 | 意义 | 值 |
|---|---|---|
| text-indent | 设置文本缩进，块级元素有效 | 尺寸数值 |
| text-align | 设置文本的对齐方式 | left: 左对齐
right: 右对齐
center: 居中对齐
justify: 两端对齐 |
| word-spacing | 设置词组之间的间距 | 尺寸数值 |
| letter-spacing | 设置字符之间的间距 | 尺寸数值 |
| text-transform | 进行大小写设置 | none: 不改变原文
capitalize: 每个单词首字母大写
uppercase: 所有字符转换为大写
lowercase: 所有字符转换为小写 |
| text-decoration | 进行文本修饰 | none: 不进行修饰
underline: 添加下画线
overline: 添加上画线
line-through: 添加删除线 |
| white-space | 处理空白字符 | none: 不处理
nowrap: 不换行 |
| color | 设置文本颜色 | 颜色值 |
| font-family | 设置文本字体 | 字体名 |