表 4-8 radio 组件的常用属性

| 属性名 | 意义 | 值 |
|---|---|---|
| value | 设置当前选项的值 | 字符串 |
| checked | 设置当前选项是否选中 | 布尔值 |
| disabled | 设置当前选项是否可用 | 布尔值 |
| color | 设置选中颜色 | 颜色值 |

# 4.2.3 input 组件

input 组件是一个原生组件，用来接收用户使用键盘输入的内容，示例代码如下：

```
<!--pages/input/input.wxml-->
<input style='background-color:#fafafa;color:gray;height:45px;' placeholder='请输入内容'></input>
```

运行代码，效果如图 4-10 所示。

![](bbox=[0.38, 0.38, 0.58, 0.6])

图 4-10 input 组件的运行效果

上述示例代码创建了一个最基本 的 input 输入框组件，用户单击输入框后会唤起键盘，操作键盘进行输入框文本的键入，开发者可以绑定多种回调方法接收用户的交互事件。input 组件的常用属性如表 4-9 所示。

表 4-9 input 组件的常用属性

| 属性名 | 意义 | 值 |
|---|---|---|
| value | 设置输入框中的内容 | 字符串 |
| type | 设置输入框的类型，不同的类型会匹配不同的键盘 | 可选值如下： text，文本输入类型； number，数值输入类型； idcard，身份证输入类型； digit，小数输入类型 |
| password | 设置输入框是否为密码模式 | 布尔值 |
| placeholder | 设置输入框无文字时的默认提示文本 | 字符串 |
| placeholder-style | 设置提示文本的风格 | 字符串 |
| placeholder-class | 设置提示文本的类 class 属性 | 字符串 |
| disabled | 设置输入框是否禁用 | 布尔值 |