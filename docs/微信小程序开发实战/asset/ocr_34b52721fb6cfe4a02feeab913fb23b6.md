# 表 4-4  button 组件可配置属性列举

| 属性名 | 意义 | 值 |
|---|---|---|
| disabled | 设置按钮是否禁用 | 布尔值 |
| loading | 设置按钮的标题前是否显示加载动画 | 布尔值 |
| form-type | 设置当按钮在 form 表单中时的提交类型 | 可选值：submit，进行表单提交；reset，进行表单重置 |
| hover-class | 设置按钮被按下时的 class 类 | 字符串 |
| hover-stop-propagation | 设置按钮是否阻止父节点显示按下状态 | 布尔值 |
| hover-start-time | 设置按钮按住多久后显示按下状态 | 数值，单位为 ms |
| hover-stay-time | 设置手指松开后按钮保持多久的按下状态 | 数值，单位为 ms |

表 4-4 列举了 button 组件的基础属性，button 组件更多是用来作为特殊微信服务的人口，如上面的实例代码，当单击页面上的按钮时会弹出分享弹窗，button 组件可以通过设置 open-type 属性使用微信开发功能，open-type 支持的功能如表 4-5 所示。

# 表 4-5  open-type 支持的功能

| open-type 值 | 意义 |
|---|---|
| contact | 打开客服会话 |
| share | 触发用户分享 |
| getUserInfo | 获取用户信息 |
| getPhoneNumber | 获取用户手机号 |
| launchApp | 打开 App |
| openSetting | 打开授权设置页 |
| feedback | 打开意见反馈页面 |

调用微信开放功能，通常需要配置 button 组件的属性一起使用，button 组件中与微信开放功能相关的属性如表 4-6 所示。

# 表 4-6  button 组件中与微信开放功能相关的属性

| 属性名 | 意义 | 值 |
|---|---|---|
| lang | 设置 getUserInfo 功能获取到的用户信息的语言类型 | 可选：zh_CN，简体中文；zh_TW，繁体中文；en，英文 |
| bindgetuserinfo | 设置 getUserInfo 功能获取用户信息后的回调函数，用户信息会通过回调函数传递给开发者 | 函数 |
| session-from | 设置 contact 功能的会话来源 | 字符串 |
| send-message-title | 设置 contact 功能的消息标题 | 字符串 |
| send-message-path | 设置 contact 功能的消息单击跳转路径 | 字符串 |
| send-message-img | 设置 contact 功能的消息图片 | 字符串 |
| show-message-card | 设置 contact 功能是否显示会话内的消息卡片发送提示 | 布尔值 |