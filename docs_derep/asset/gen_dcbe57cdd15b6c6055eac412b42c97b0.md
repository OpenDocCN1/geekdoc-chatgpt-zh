| unlink | 移除文件 | { filePath:待删除的文件路径 success:查询成功时的回调，文件或目录存在即判定成功 fail:查询失败时的回调，文件不存在会触发 complete:查询结束时的回调 } |

| unzip | 解压压缩包 | { zipFilePath:压缩包路径 targetPath:解压后存放路径 success:查询成功时的回调，文件或目录存在即判定成功 fail:查询失败时的回调，文件不存在会触发 complete:查询结束时的回调 } |

| writeFile | 写入文件内容 | { filePath:目标文件路径 data:写入内容 encoding:编码格式 success:查询成功时的回调，文件或目录存在即判定成功 fail:查询失败时的回调，文件不存在会触发 complete:查询结束时的回调 } |

文件写入时可指定的 encoding 取值见表 7-21。

## 表 7-21 文件编码支持列表

| encoding 可选值 |

|---|

| ascii |

| base64 |

| binary |

| hex |

| utf16le |

| utf8 |

| latin1 |

提醒一点，虽然小程序框架提供了不少文件操作能力，但存储切忌过度占用，小程序的精髓在于轻量，功能设计要始终围绕小巧、快速来展开。

## 第 8 章

## 界面交互与动效

本章聚焦小程序里常见的交互与动画实现。小程序内置了丰富的交互能力，比如系统弹窗、导航栏样式调整、页面下拉刷新与上拉加载等。动画同样关键，它能让页面过渡更顺滑、体验更舒服。开发时既可以用 WXSS 来定义动画，也能借助 Animation 对象来编排。

## 8.1 系统弹窗

第6章在讲自定义组件时，我们做过一个页面弹窗的自定义实现。实际上框架自带了一套系统弹窗，样式和交互都已封装好，开箱即用。

小程序的弹窗大致可分为消息提示、确认对话框、加载等待提示、底部抽屉选项四类，下面逐一说明用法。

## 8.1.1 消息提示

调用 wx.showToast 就能弹出轻量提示，通常用于告知结果，可自定义标题和图标。传入的配置对象可选参数见表 8-1。

## 表 8-1 wx.showToast 可配置参数

| 属性 | 说明 | 类型 |

|---|---|---|

| title | 提示文案 | 字符串 |

| icon | 系统内置图标 | success 成功图标；loading 加载图标；none 隐藏图标 |

| image | 自定义图标本地路径 | 字符串 |

| duration | 展示时长 | 毫秒数 |

| mask | 是否加半透明遮罩拦截手势 | 布尔值 |

| success | 调用成功后的回调 | 函数 |

| fail | 调用失败后的回调 | 函数 |

| complete | 调用结束后的回调 | 函数 |

想要提前收起提示，可以调用 wx.hideToast。在测试项目里新建一个名叫 modal 的页面，在 modal.wxml 中加入如下测试代码：

[PRE0]

toast 方法的实现如下：

[PRE1]

运行后效果如图 8-1。

[PRE2]

图 8-1 消息提示展示样式

## 8.1.2 确认对话框

wx.showModal 能弹出带按钮的确认框，除了展示信息，还能收集用户选择。配置对象可选项见表 8-2。

## 表 8-2 wx.showModal 可配置参数

| 属性 | 说明 | 类型 |

|---|---|---|

| title | 标题文案 | 字符串 |

| content | 内容文案 | 字符串 |

| showCancel | 是否展示取消按钮 | 布尔值 |

| cancelText | 取消按钮文字 | 字符串 |

| cancelColor | 取消按钮文字颜色 | 颜色值 |

| confirmText | 确认按钮文字 | 字符串 |

| confirmColor | 确认按钮文字颜色 | 颜色值 |

| success | 用户操作后的回调，返回按钮状态 | 函数 |

| fail | 调用失败回调 | 函数 |

| complete | 调用完成回调 | 函数 |

示例代码如下：

[PRE3]

[PRE4]

运行效果如图 8-2。

[PRE5]

图 8-2 确认对话框展示样式

用户点任一按钮后对话框会自动关闭。

## 8.1.3 等待提示

使用过程中常有等待场景，如网络请求、文件下载等。wx.showLoading 能在界面上显示友好的等待层，参数说明见表 8-3。

表 8-3 wx.showLoading 可配置参数

| 属性 | 说明 | 类型 |

|---|---|---|

| title | 提示文案 | 字符串 |

| mask | 是否显示遮罩拦截手势 | 布尔值 |

| success | 调用成功回调 | 函数 |

| fail | 调用失败回调 | 函数 |

| complete | 调用完成回调 | 函数 |

代码示例：

[PRE6]

运行效果如图 8-3。

[PRE7]

图 8-3 等待提示展示样式

注意该提示不会自动消失，需要手动调用 wx.hideLoading 结束展示。

## 8.1.4 底部抽屉选项

抽屉弹窗用于提供一组可选操作，从页面底部上滑出现，交互后自动收起。可通过 wx.showActionSheet 触发，配置项见表 8-4。

表 8-4 wx.showActionSheet 可配置参数

| 属性 | 说明 | 类型 |

|---|---|---|

| itemList | 选项数组，最多6项 | 字符串数组 |

| itemColor | 选项文字颜色 | 颜色值 |

| success | 用户选择后的回调，返回 tapIndex | 函数 |

| fail | 调用失败回调 | 函数 |

| complete | 调用完成回调 | 函数 |

示例代码：

[PRE8]

运行效果如图 8-4。

[PRE9]

图 8-4 抽屉弹窗展示样式

## 8.2 导航栏与标签栏操作

导航栏位于页面顶部，展示标题与返回控件；标签栏位于底部，用于切换平级页面。两者都可通过接口动态调整。

### 8.2.1 导航栏接口

框架提供多项导航栏控制能力，可修改标题、颜色、加载态等，具体方法见表 8-5。

表 8-5 导航栏相关方法

| 方法 | 说明 | 参数 |

|---|---|---|

| wx.showNavigationBarLoading | 展示导航栏加载图标 | { success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.hideNavigationBarLoading | 隐藏导航栏加载图标 | { success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.setNavigationBarTitle | 修改导航栏标题 | { title:标题文本 success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.setNavigationBarColor | 配置导航栏配色 | { frontColor:前景色仅支持 "#000000" 或 "#ffffff" backgroundColor:背景色 animation:动画对象 success:成功回调 fail:失败回调 complete:完成回调 } |

设置背景色时可附带动画，动画对象属性见表 8-6。

表 8-6 背景色切换动画属性

| 属性 | 说明 | 取值 |

|---|---|---|

| duration | 动画时长 | 毫秒数 |

| timingFunc | 时间曲线 | linear 线性；easeIn 渐入；easeOut 渐出；easeInOut 渐入渐出 |

下面演示加载图标的显隐。新建 navigation-bar 页面，在 navigation-bar.wxml 中写入：

[PRE10]

对应逻辑在 navigation-bar.js 中实现 hide 与 show：

[PRE11]

运行效果如图 8-5。

[PRE12]

图 8-5 导航栏配置效果

### 8.2.2 标签栏配置

标签栏的全局结构需在 app.json 中先行声明：

[PRE13]

每个标签对应一个页面，点击即可切换。运行时也可动态控制标签栏或单个标签，具体接口见表 8-7。

表 8-7 标签栏操作相关方法

| 方法 | 说明 | 参数 |

|---|---|---|

| wx.showTabBar | 显示标签栏 | { animation:是否带动画布尔值 success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.hideTabBar | 隐藏标签栏 | { animation:是否带动画布尔值 success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.showTabBarRedDot | 为标签添加红点 | { index:标签下标 success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.hideTabBarRedDot | 移除标签红点 | { index:标签下标 success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.setTabBarBadge | 设置标签徽章 | { text:徽章文字 index:标签下标 success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.removeTabBarBadge | 移除标签徽章 | { index:标签下标 success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.setTabBarItem | 修改单个标签 | { index:标签下标 text:文字 iconPath:图标路径 selectedIconPath:选中态图标路径 success:成功回调 fail:失败回调 complete:完成回调 } |

| wx.setTabBarStyle | 调整标签栏样式 | { color:文字颜色 selectedColor:选中文字颜色 backgroundColor:背景色 borderStyle:边框风格 black/white success:成功回调 fail:失败回调 complete:完成回调 } |

## 8.3 下拉刷新与上拉加载

列表类页面常用分页加载和刷新机制，小程序对此支持很友好。

### 8.3.1 启用下拉刷新

开启下拉刷新只需在页面 JSON 中配置。若在 app.json 中开启，则全局生效；也可单页配置。

新建 pull-refresh 页面，在 pull-refresh.json 中写入：

[PRE14]

当 enablePullDownRefresh 为 true 时，下拉会触发刷新动画，默认关闭。可进一步美化刷新组件样式，相关方法见表 8-8。

表 8-8 刷新组件样式配置

| 方法 | 说明 | 参数 |

|---|---|---|

| wx.setBackgroundTextStyle | 设置下拉刷新文字样式 | { textStyle:文字风格 dark 或 light success:成功回调 fail:失败回调 complete:完成回调 } |