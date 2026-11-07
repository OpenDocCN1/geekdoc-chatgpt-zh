[PRE0]

碰到“权限不足”的提示时，别急着猜原因，先执行一次 openclaw sandbox explain 看看当前会话实际生效的权限长什么样。很多情况下，你以为某个工具是开启状态，其实已经被沙箱策略覆盖掉了。

#### 1.11.3 第三层：多智能体（multi-agent）场景下的独立权限

若同时配置了多个 agent，例如一个专做代码、一个处理邮件，每个 agent 都可以拥有各自独立的权限配置。

官方的多智能体文档明确说明，每个 agent 都能单独设置：

- agents.list [].sandbox
- tools.allow / tools.deny
- agents.list [].tools 覆盖

这就意味着，同样的任务，Agent A 能执行而 Agent B 无法执行。这并非 bug，而是权限分配不同所致。

例如可以这样配置：

[PRE1]

在该配置中，coder 拥有执行命令的权限，assistant 没有；assistant 可以使用浏览器，coder 则不行。这样分工清晰，既灵活又安全。

#### 1.11.4 关于 Browser、Nodes、Canvas 的说明

不少教程把 Browser、Nodes、Canvas 说成三个互相独立的开关，这种说法不够准确。

更准确的理解是：

- Browser 代表一种具体的工具能力，由 Gateway 管理，可被 tools.deny 禁用
- Nodes 代表“伴随设备”能力，节点通过 Gateway WebSocket 接入，暴露 canvas.*、camera.*、device.*、notifications.*、system.* 等命令
- Canvas 其实是 node 能力的一部分，并不是一个独立的配置项

因此，“如何给它开门”并不是简单地打开某个开关，而是要搞清这三层权限机制，定位实际的限制层级。

#### 1.11.5 实用排查步骤

出现“权限不足”报错时，按下面顺序排查：

第一步：检查工具配置。打开 ~/.openclaw/openclaw.json，找到 tools.allow 和 tools.deny，确认报错的工具是否被禁止。

第二步：查看实际生效权限。运行 openclaw sandbox explain，查看当前 session 真正可用哪些工具。经常是配置文件看起来正常，但沙箱策略已经覆盖了配置。

第三步：检查 agent 级别权限。若使用了多个 agent，确认当前任务由哪个 agent 处理，以及该 agent 的权限配置。

第四步：查看日志。运行 openclaw logs --follow，查看详细错误信息。日志里通常会明确写出“tool 'xxx' is denied”或“permission error”。

第五步：临时测试。若不确定是否权限问题，可临时修改配置测试。比如清空 tools.deny，或把可疑工具加入 tools.allow，看是否能正常执行。能执行说明确实是权限限制，不能执行则另有问题。

#### 1.11.6 安全建议：宁可严格，别太松

OpenClaw 的能力很强，包含读写文件、执行命令、操作浏览器、控制设备等。权限放得太宽，万一被恶意 Skill 利用或因配置失误误操作，后果可能很严重。

建议：

- 默认使用 deny 列表，明确禁止高风险工具，例如 bash、exec
- 需要时再临时放开，用完后收回
- 多 agent 场景下，为每个 agent 授予最小必要权限
- 定期复审权限配置，回收不再需要的权限

权限控制看似麻烦，却是保护数据和系统安全的基础。弄懂这三层权限逻辑，就能在发挥 OpenClaw 强大能力的同时把风险控制在合理范围。

### 1.12 多通道连上后消息乱，怎么让它不混淆？

装好 OpenClaw 后，很多人会把飞书、钉钉、企业微信等所有渠道都连上。结果出现问题：在飞书里问工作问题，回答却带出了 Telegram 私聊里的个人信息；或者在不同群里 @ 它，回复内容相互串了。这就是消息混淆。

#### 1.12.1 核心机制：Session 隔离

OpenClaw 解决消息混淆靠的不是模型“记住场景”，而是 Session 隔离机制。官方文档明确指出：

- 私聊 DM：通常进入共享的 main session
- 群组：始终使用独立的 non-main session

这意味着，Telegram 私聊里的对话会和飞书私聊共享同一个 session，都是 main session；而群聊完全独立，各自拥有自己的 session，见表 1-7。

**表 1-7 Session Key 的场景说明**

| 场景 | Session Key | 说明 |
| --- | --- | --- |
| 私聊 | agent:main:main | 所有渠道的私聊通常共享 |
| 飞书群 A | agent:main:feishu-group-xxx | 每个群独立 |
| 飞书群 B | agent:main:feishu-group-yyy | 互不干扰 |
| 钉钉群 | agent:main:dingtalk-group8zzz | 独立 session |

例如在两个不同的飞书群里 @OpenClaw，这两个群使用完全不同的 session，彼此看不到对方的历史。

#### 1.12.2 MEMORY.md 只进私聊，不进群聊

这是防止消息混淆的关键设计。官方文档明确指出：MEMORY.md 是 curated long-term memory，只在 main/private session 加载，never in group contexts，即绝不在群组上下文中使用。

实际效果：

你在飞书私聊里告诉 OpenClaw：“我的老板姓王，对数据很敏感，汇报时一定要有数据支撑。”

→ 该信息会写入 MEMORY.md

→ 以后飞书私聊时，OpenClaw 会记得这个背景

→ 但在飞书群里 @ 它时，它看不到这条记忆，不会说出“你老板姓王”等内容

该设计既保护隐私，也避免群内回复泄露个人信息。

#### 1.12.3 群聊场景：requireMention 是关键

群聊容易混乱的另一原因是设置不当，OpenClaw 会响应群内所有消息，导致频繁打断、消耗大量 token 甚至回复不该回复的内容。

官方文档反复强调 requireMention 这个设置：

[PRE2]

两种模式的对比见表 1-8。

**表 1-8 群聊模式设置对比**

| 设置 | 行为 | 优点 | 缺点 |
| :--- | :--- | :--- | :--- |
| requireMention: true | 只响应 @Bot 的消息 | 安全、省 token、不打扰 | 需要手动 @ |
| requireMention: false | 响应所有消息 | 无须 @ 即可用 | 消耗巨大、易误触、隐私风险 |

强烈建议群聊保持 requireMention: true。

#### 1.12.4 Telegram 话题和钉钉群的进一步隔离

在钉钉或 Telegram 管理多个项目时，OpenClaw 支持更细粒度的隔离。

Telegram Forum 模式：官方文档明确指出，群 session 按 group ID 隔离，Forum topics 会追加 topic: <threadId> 标识。这意味着每个 topic 都有独立的隔离 workspace、memory、session。

实际场景举例：

团队在 Telegram 有个项目群，用 Forum 功能创建三个话题：

- 话题 1：产品需求讨论
- 话题 2：技术方案评审
- 话题 3：日常闲聊

每个话题拥有独立 session，“产品需求”话题里的讨论不会串到“技术方案”话题，对多项目多话题并行很有用。

#### 1.12.5 跨渠道会不会串

常见疑问：我在飞书私聊和钉钉私聊问同样问题，OpenClaw 会把两边对话混在一起吗？

答案：在单个 agent 的默认路由下，不同渠道的私聊通常都进入 main session，因此会共享上下文。但这不等于自然人账号自动打通。

更准确的理解是：OpenClaw 不知道“飞书的张三”和“钉钉的张三”是同一个人，它只知道这两个账号都通过配对、被允许进入 main session。若想让不同渠道对话完全隔离，需在配置中明确路由到不同 session 或 agent。

#### 1.12.6 实用建议：按场景分流

为避免消息混淆，建议这样规划：

按用途分渠道：

- 工作相关用飞书或钉钉私聊
- 个人问题用 Telegram 或企业微信私聊
- 虽然都进入 main session，但渠道层面已有区分

群聊必开 requireMention：

- 所有群聊务必设置 requireMention: true
- 避免 Bot 响应所有消息造成混乱

利用话题功能：

- 使用钉钉或 Telegram 的多话题功能
- 让每个话题拥有独立 session
- 不同项目互不干扰

多个 agent 方案：

- 若确实需要严格隔离，例如工作与生活完全分开
- 配置两个 agent，通过不同渠道触发
- 工作用 agent-work，生活用 agent-personal

OpenClaw 的 session 隔离机制已很完善，多数“消息混淆”问题源于配置不当。理解 DM 进 main、群组独立 session、MEMORY.md 不进群这三条核心规则，就能基本管好消息。

### 1.13 有哪些指令让“龙虾”更加听话？

和 OpenClaw 对话大部分时间用自然语言即可。但某些操作用自然语言表达不清或需重复解释，这时就需要 Slash Commands。斜杠命令由 Gateway 直接处理，不经过模型理解，响应更快、执行更准。

#### 1.13.1 基础命令：日常最常用的几个

**/status – 查看当前状态**

用法：直接发送 /status。

效果：显示当前 session 状态信息，包括使用的模型、token 用量、上下文窗口使用情况、session 基本信息。是诊断问题时最常用的命令。

**/new – 开始新会话**

用法：直接发送 /new。

效果：清空当前 session 对话历史，开启全新会话。想换话题或觉得上下文过长影响质量时使用。

进阶用法：可带模型参数切换。

- /new deepseek/deepseek-chat – 清空历史并切换到 DeepSeek
- /new zai/glm-5 – 清空历史并切换到智谱 GLM-5

**/usage – 控制 token 用量显示**

基础用法：/usage。

不带参数时循环切换回复底部 usage footer 显示方式，关闭→显示 tokens→显示完整信息。

查看成本：/usage cost。

打印本地成本摘要，了解花费。

**/stop – 停止当前任务**

用法：直接发送 /stop。

效果：若 OpenClaw 正在执行较长任务，可发送此命令中断。

#### 1.13.2 进阶命令：调整行为方式

**/think – 调整推理深度**

用法：/think: high 或 /think high，冒号可选。

可选级别见表 1-9。

**表 1-9 推理深度级别对比**

| 级别 | 推理深度 | token 消耗 | 适用场景 |
| :--- | :--- | :--- | :--- |
| off | 不推理 | 最低 | 简单问答 |
| minimal | 最小化 | 很低 | 日常聊天 |
| low | 轻度 | 低 | 一般任务 |
| medium | 中等 | 中等 | 常规工作 |
| high | 深度 | 高 | 复杂分析 |
| xhigh | 极深 | 很高 | 专业研究 |

实用建议：日常对话用 low 或 medium 足够，复杂任务可临时调到 high。

**/activation – 群聊激活模式**

用法：

- /activation mention – 只响应 @Bot 的消息，推荐
- /activation always – 响应所有消息，不推荐

场景示例：在飞书群里把模式改成 always，结果群聊任何内容它都要插一句，token 暴涨、他人抱怨“太烦”，赶紧改回 mention 模式才安静。

注意：该命令仅在群聊场景有意义，私聊无效。

#### 1.13.3 高级命令：需要谨慎使用的

**/allowlist – 管理白名单**

用法：

- /allowlist add – 添加白名单用户

## 第一章 “龙虾”刚养活，不会动怎么办？

##### /allowlist remove – 移除白名单用户

注意：该命令需在配置中开启 `commands.config = true`，且受渠道 `configWrites` 约束，默认可能禁用。

##### /restart – 重启 Gateway

用法：`/restart`。

效果：重启 Gateway 服务。

警告：

- 默认禁用，需在配置中设置 `commands.restart: true` 才能使用
- 重启会短暂中断所有连接
- 普通用户不建议开启

##### /verbose 和 /reasoning – 调试用

用法：

- `/verbose` – 显示详细日志
- `/reasoning` – 显示推理过程

重要警告：这两个命令在群聊中有风险，可能暴露不想公开的内部推理、工具输出甚至敏感信息。

建议：仅在私聊中使用，群里切勿开启。

#### 1.13.4 其他实用命令

##### /help – 查看帮助

用法：`/help`。

效果：显示可用命令列表及简单说明。

部分常见指令见表 1-10。

表 1-10 命令使用对比表

| 命令 | 使用频率 | 安全级别 | 群聊推荐 | 说明 |
| :--- | :--- | :--- | :--- | :--- |
| /status | 高 | 安全 | √ | 查状态必备 |
| /new | 中 | 安全 | √ | 清空上下文 |
| /usage | 中 | 安全 | √ | 控制成本 |
| /think | 中 | 安全 | √ | 调节推理 |
| /activation | 低 | 安全 | √ | 仅群聊有效 |
| /verbose | 低 | 有风险 | √ | 仅私聊用 |
| /reasoning | 低 | 有风险 | √ | 仅私聊用 |
| /restart | 极低 | 高风险 | √ | 慎用 |

#### 1.13.5 使用建议

**建议一：从基础命令开始**

新手先熟悉 `/status`、`/new`、`/usage` 就够了，用熟后再尝试进阶命令。

**建议二：群里谨慎用命令**

像 `/verbose`、`/reasoning` 这类调试命令只在私聊使用；群里使用可能泄露信息并影响体验。

**建议三：命令要单独发**

大多数命令必须作为单独一条以“/”开头的消息发送。

错误示例：“帮我分析下这个数据 /think high”。命令会失效。

正确做法：先发 `/think high` 等待确认，再发“帮我分析下这个数据”。

**建议四：别把命令当万能钥匙**

Slash Commands 是快捷操作，不是所有问题都要靠命令解决。能用自然语言说清的就直接说，需要精确控制时再用命令。

Slash Commands 让 OpenClaw 更听话，关键是知道何时、如何使用。日常对话可能几天不用一次命令，但在需要精确控制的场景下，一个命令就能省去大量描述。

### 1.14 如何给 OpenClaw 加外挂，Skill 怎么用？

OpenClaw 装好、模型配好后已能完成许多任务。要让它更专业，例如自动查邮件、操作浏览器、连接项目管理工具，就需要安装 Skill。

#### 1.14.1 Skill 和 Plugin 的区别

Skill：为 agent 增加工作流程和能力说明，教会它如何完成某件事。

Plugin：为 OpenClaw 本体增加工具、命令、渠道能力。