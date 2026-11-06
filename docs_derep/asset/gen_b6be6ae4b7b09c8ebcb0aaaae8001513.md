软件连接器擅长从不同的 App 和软件中提炼出高频的待办动作，比如在天眼查里就能批量抓取企业的基础档案、联系方式、司法风险、工商登记、法律诉讼、变更记录等信息，如图 12-6 所示。

[PRE0]

把 GPTs 落地到这些预设好的执行任务上，就能为 GPTs 生成 Action，让它直接查询某公司的工商信息，或直接通过企业微信发送消息。

这类连接工具有很多，国外主流的有 Zapier、Make，国内常用的有集简云、腾讯轻联、数环通。

后文会分别以 Zapier 和集简云为例，讲解如何通过 Action 让 GPTs 直接操作 Gmail 邮件并保存到草稿箱，以及如何调用天眼查查询工商信息。

## 12.2 Action 应用场景示例

Action 的落地方向大致可以归纳为以下几类。

1. 数据查询和读取

核心逻辑是让 GPTs 通过 Action 接入数据库或 API 获取实时数据，特别适合需要即时反馈的场景，比如查天气、查股价、查机票酒店价格等。

把 GPTs 打造成个人助理时，这一类应用能让它收集、分析各类信息并给出建议。

示例：扮演个人助理角色

把 “Google Calendar Assistant（Google 日历助手）” 挂到 GPTs 之后，GPTs 就能充当个人助理。询问 “Google Calendar Assistant 我今天的日程有哪些” 时，它会先请求访问日历权限，读取日程内容，如图 12-7 所示。

[PRE1]

图 12-7 “Google Calendar Assistant” 连接 Action 后会询问用户是否授权查询日历

授权确认后，“Google Calendar Assistant” 会经由 Action 读取 Google 日历事项，并在聊天窗口里把全天安排反馈给用户，如图 12-8 所示。

[PRE2]

图 12-8 “Google Calendar Assistant” 查询 Google 日历事项并且回复用户

2. 数据搜索

ChatGPT 自身的知识边界是有限的，接入外部知识库后就能获取更广的信息。

这类场景适合把 GPTs 接到外部数据库，发挥 AI 大数据助手的作用。

示例：从论文知识库中查询信息

Consensus 向 GPTs 开放平台上约 200 万份期刊论文，让使用者通过 Consensus GPTs 检索各类论文资料，如图 12-9 所示。

[PRE3]

图 12-9 Consensus 通过 Action 将 200 万份期刊论文数据与 GPTs 共享

打开 Consensus GPTs 后提问某个领域的论文，后者会调用 Action 在论文库中检索，并把标题、摘要、链接返回给用户，如图 12-10 所示。

[PRE4]

图 12-10 Consensus GPTs 查询论文数据库中的资料并回复用户

3. 数据写入

除了查询和搜索，GPTs 还能通过 Action 改写或写入第三方 App 的数据，比如直接代用户给同事发 Slack 消息，无需登录客户端，如图 12-11 所示。

[PRE5]

图 12-11 GPTs 连接 Action 后可以直接代替用户给同事发送 Slack 消息

## 12.3 Action 配置必备要素

新建一个 GPTs，进入 “Configure” 选项卡，拉到页面最底部点 “Create new action”，就能看到配置时需要准备的元素，如图 12-12 所示。

[PRE6]

图 12-12 单击 “Create new action” 按钮

点开后进入 “Add actions” 界面，如图 12-13 所示。

[PRE7]

图 12-13 “Add actions” 界面

第一个要素：Authentication（认证）

指 GPTs 发起 Action 请求后，访问第三方软件或 App 时是否需要账号认证，如图 12-14 所示。

[PRE8]

目前常见三种方式，如图 12-15 所示。

“None” 表示 GPTs 可直接访问 API，无需密码或密钥，通常用于公开数据，比如天气、新闻头条。

“API Key” 相当于一把访问密码，只有持有者才能调取对应数据，多用于需要管控权限的场景。

“OAuth” 是一种更安全灵活的授权方式，相当于给 GPTs 一张临时许可，代表用户安全访问第三方服务，如社交账号或个人资料，而不暴露登录信息，常用于需要用户授权的邮箱、日历等服务，确保只有获许可的应用才能操作数据。

[PRE9]

第二个要素：Schema（结构）

Schema 定义了 API 的结构和调用写法。比如做一个天气查询助手 GPTs，就需要定义 Schema 来描述如何向天气 API 发送查询请求，如图 12-16 所示。

[PRE10]

通过 Schema，GPTs 才知道如何正确获取并把结果返回到聊天界面。Schema 通常由开发者手写，使用软件连接器搭建 Action 时，则可以直接 “Import from URL（从 URL 导入）”，填入连接器厂商提供的现成 Schema，省去重复编写。

第三个要素：Privacy policy（隐私政策）

Privacy policy 是第三方软件、App 提供 API 时附带的隐私说明，用来告知用户数据交互时的使用和保护方式，如图 12-17 所示。

[PRE11]

整体配置看起来门槛不低，但借助软件连接器工具，即使不懂代码也能扩展 GPTs 的能力。接下来结合案例说明如何利用连接器创建 Action。

## 12.4 Zapier 联动 GPTs：自动化创建 Notion 文档与待办

Zapier 是一款可连接全球超过 7000 个主流应用的软件连接器。通过其 Action 配置，能让 GPTs 连接 Gmail、Outlook 查询最新邮件甚至撰写邮件，也能自动创建印象笔记、Notion 笔记。

本次搭建一个 GPTs，让它联网搜集最新资讯为 YouTube 视频提供灵感，并把结果存到 Notion，同时生成待办提醒。涉及两个核心 Action：一是把内容写入 Notion 文档，二是创建 Microsoft To Do 待办。

在 Zapier 中为 GPTs 配置 Action 主要分三步。

1. 进行 GPTs 的基础设置

设定 GPTs 的身份和任务：YouTube 视频研究与内容创作助手。要求在给出最终结果前严格按步骤执行。第一步联网查询信息，第二步总结信息，第三步把总结和参考链接整理归档，第四步询问用户是否需要保存到 Notion，是否需要生成待办任务，如图 12-18 所示。

[PRE12]

图 12-18 设置 GPTs 的角色和任务

2. 在 Zapier 中设置给 GPTs 使用的 Action

先注册 Zapier 账号，进入官网后点击右上角 “Sign up（注册）” 按钮，如图 12-19 所示。

[PRE13]

注册完成后开始具体配置，如图 12-20 所示。

[PRE14]

本例会用到 Notion 和 Microsoft To Do，因此在 Action 配置界面点 “Add a new action（增加新的动作）”，找到 “Notion：Create Page（Notion：创建页面）” 动作，如图 12-21 所示。

[PRE15]

[PRE16]

图 12-21 选择 “Notion：Create page（Notion：创建页面）”

授权已注册的 Notion 账号，并选择内容保存的目标目录，可以让 AI 自行判断，也可以指定页面，如图 12-22 所示。

[PRE17]

图 12-22 选择 GPTs 应该将内容创建在哪个目录下

关键一步是设置 “Action Name（Action 名称）”。Action Name 用来告诉 GPTs 应该调用哪个 Action，GPTs 会通过名称和对应 URL 进行匹配。本例命名为 “Save info to Notion Page”，如图 12-23 所示。

[PRE18]

图 12-23 命名 GPTs Action

Action Name 下方有 “Require preview before running AI Action（learn more）” 选项，表示把内容发送到 Notion 前是否需要预览。不需要预览可不勾选，需要先看一下再保存则勾选，如图 12-24 所示。

[PRE19]

图 12-24 确认 GPTs 在保存内容前是否需要进行预览

完成后复制该 Action 的 URL 并点击 “Enable action” 按钮，如图 12-25 所示。

[PRE20]

图 12-25 保存 URL 并单击“Enable action”按钮

在 Zapier Action 界面，按钮显示为绿色表示已激活，灰色表示未激活。只有激活状态的 Action 才能被 GPTs 调用，如图 12-26 所示。

[PRE21]

3. 在 GPTs 配置界面完成 Action 的调试

返回第一步搭建的 GPTs，在 “Configure” 选项卡底部点击 “Create new action” 按钮，如图 12-27 所示。

[PRE22]

在 “Add actions” 界面粘贴 Zapier 提供的链接，点击 “Import” 按钮，如图 12-28 所示。

[PRE23]

此时 Zapier 的 “Schema” 会自动填充预设代码，用于让 GPTs 与 Zapier 交互并获取指令，如图 12-29 所示。

[PRE24]

在 “Privacy policy” 对话框填入 Zapier 的隐私政策链接 “https://zapier.com/privacy”，如图 12-30 所示。

[PRE25]

回到 “Configure” 选项卡，在 “Instructions” 对话框里加入 Zapier 推荐的指令描述，如图 12-31 所示。

[PRE26]

图 12-31 在 “Instructions” 对话框中添加 Zapier 推荐的指令描述

这段指令的核心含义是 GPTs 会先检查 Zapier 的 Action 是否已配置完成，若已完成则调用这些 Action 帮用户执行任务。

在 Zapier 推荐的指令下方，补充刚才在 Zapier 中设置的 Action 信息，如图 12-32 所示。