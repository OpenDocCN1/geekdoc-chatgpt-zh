4. 撰写推荐理由或淘汰理由

5. 生成候选人排序清单

这五步分别映射到 OpenClaw 的哪些能力：

1. 上传 → 文件读取工具
2. 提取 → 文档解析 + AI 理解
3. 打分 → 按规则推理
4. 生成理由 → 文本生成
5. 输出 → 格式化输出

##### 第三步：准备核心文件
AGENTS.md 用来定义身份与规则：

> 你是一名专业的 HR 简历筛选助手。

职责范围：

- 迅速浏览简历并抽取要点
- 依岗位要求判断候选人适配程度
- 输出明确的推荐或淘汰说明

工作准则：

- 公正客观，不掺杂个人偏好
- 同时兼顾硬性指标（学历、从业年限）和软性要素（项目经历、技能契合）
- 信息缺失时要如实标注“缺少××信息”

禁止事项：

- 不回应无关提问（例如“今天天气如何”）
- 不给出职业发展建议（此项由候选人自行决定）
- 不对候选人的外貌、性别、年龄等做评价

预装 Skills：
搭建一个“简历解析”技能，教会 OpenClaw 如何从简历中抽取信息、如何评分、如何呈现结果。

模板文件：
准备若干高频岗位的需求模板，例如“Java 工程师要求”“产品经理要求”“运营专员要求”等。

##### 第四步：测试和优化
邀请数名真实使用者试用：

- 提供真实简历让其跑通流程
- 观察输出质量
- 收集反馈：哪些地方顺手、哪些卡顿、哪些看不懂

常见问题与对策：

- 抽取信息不准 → 调优 Skill 的抽取逻辑
- 评分不合理 → 调整评分规则
- 输出格式混乱 → 固定输出模板
- 用户缺乏使用感知 → 增加引导提示

反复迭代，直至 80% 的测试者反馈“还算可用”。

##### 第五步：打包和分发
面向公司内部使用：

- 部署在内网服务器
- 编写简要使用手册
- 开展半小时左右的培训

面向客户使用：

- 提供安装包或云端访问链接
- 交付详细文档与视频教程
- 提供技术支持，至少在初期保持响应

若考虑商业化：

- 设计计费方式（按人头、按用量、按年订阅）
- 准备销售资料（功能介绍、案例展示）
- 关注法律风险（数据隐私、服务协议）

#### 6.10.4 商业化的三大要点

##### 注意一：成本必须算清
OpenClaw 调用大模型会产生费用。若把工具提供给用户，这笔费用由谁承担？主要有三种做法：

1. 用户自备 API Key：成本由用户承担，配置相对繁琐
2. 统一采购额度：由你购买模型配额，再按使用量向用户收费，需测算利润空间
3. 免费版加付费版：免费版采用通用模型并限制用量，付费版使用更强模型且不限量

##### 注意二：数据隐私必须保障
简历、合同、客户信息等通过你的系统流转，安全责任在你。

基本要求：

- 不将用户数据用于其他用途
- 不向第三方泄露用户数据
- 定期清理不再需要的历史数据
- 云端部署需符合相关数据保护法规

##### 注意三：技术支持要提前准备
产品化后出现问题，用户会找你而非 OpenClaw 官方。

建议准备：

- 常见问题的解答文档
- 故障排查的标准流程
- 联系渠道（邮箱、微信、电话）
- 前期耐心解答各类咨询

### 小结
到此为止，全书结束。若你坚持到这里，你已不再是 OpenClaw 的普通使用者，而是真正的“养虾人”。

本章做了什么：学会让 OpenClaw 保持轻盈（精简 MEMORY.md、清理日志、关闭闲置插件），让它记忆更牢（手动编辑 MEMORY.md、定期整理 Daily Logs），让多只“龙虾”协同（多 Agent 架构、消息路由、子 Agent 后台任务），为其设立安全边界（工具白名单、沙箱隔离、安全审计），快速定位故障（status 检查、日志追踪、doctor 诊断），灵活混用云端与本地模型（Fallback 链、Ollama 本地部署），控制 API 开销（压缩上下文、设置预算上限、定期清理），备份与迁移数据（完整归档、备份校验、跨设备迁移），并探索把 OpenClaw 封装成自有工具的可能性（配置包、云端部署、定制界面、功能锁定）。

从第一章的“安装、配置、开始用”，到二至五章的实战场景，再到本章的深度优化——你已走完从“用虾”到“养虾”的完整路径。如今的 OpenClaw，已不再是装好却不知如何使用的工具，而是一个被深度训练、受控、安全、可长期维护的 AI 生产力系统。

## 最后的话

### 从“用虾”到“养虾”再到“造虾”
OpenClaw 的价值不在于它多强大，而在于它有多“属于你”。

它开源、免费、可定制，数据在你手中，规则由你制定。你投入多少心力去训练它，它就回报多少。这和养宠物类似——起初要立规矩、设围栏、喂养数据；养熟后，它就成为离不开的助手。

从安装 OpenClaw 那一刻起，“养虾之旅”就已开启。旅程可能有坎坷——报错、账单超支、数据丢失——但掌握本章技巧后，这些坑都能避开或迅速填补。

**技术会过时，原则不会。** OpenClaw 的具体配置会变，但“最小权限”“定期备份”“成本可控”“数据隔离”等原则始终有效。掌握原则，就能应对变化。

**效率的尽头是克制。** OpenClaw 能做的事很多，但并非所有事都值得让它做。会用工具的人清楚何时该用、何时不该用、何时该停止。

**工具是手段，不是目的。** OpenClaw 帮你省时提效，但省下的时间用来做什么、提升的效率创造什么价值，这些问题只有你能回答。

**会用工具的人很多，会养工具的人不多。** 你能看到这里，说明你已不满足于“用虾”，而是想“养好虾”，甚至“造自己的虾”。

继续走下去吧。OpenClaw 的世界很大，本书只是起点。

感谢阅读，祝你“养虾”顺利！

## 附录 A

### ClawHub Skills 完整手册
本附录依据 ClawHub 的真实下载数据整理。ClawHub 是 OpenClaw 的公共技能注册中心。截至 2026 年 3 月 21 日，注册技能已超 31K。如需最新清单，请访问 clawhub.ai，或执行 clawhub list --popular 查看。

> 注意事项
安全提醒：2026 年初 ClawHub 出现过 “ClawHavoc” 安全事件，随后清理了 341 个恶意技能。目前 ClawHub 已与 VirusTotal 合作，对所有技能进行自动扫描。建议遵循“100/3 原则”：优先选择下载量超 100 次、上线超 3 个月的技能，安装前在 clawhub.ai 查看 VirusTotal 扫描报告。

### A.1 下载榜 TOP 10
表 A-1 ClawHub 下载量前十（2026 年 3 月）

| 排名 | 技能名称 | 安装命令 | 一句话说明 |
| :--- | :--- | :--- | :--- |
| 1 | Capability Evolver | clawhub install capability-evolver | AI 自进化引擎，让“龙虾”自主提升能力，下载量远超第二名 |
| 2 | Wacli | clawhub install wacli | CLI 多功能瑞士军刀，开发与系统任务皆可胜任 |
| 3 | ByteRover | clawhub install byterover | 多用途任务处理器兼具实用性与开发能力 |
| 4 | Self-Improving Agent | clawhub install self-improving-agent | 自我学习框架，社区评分目前最高 |

## 附录 A ClawHub Skills 完整手册

### A.1 核心技能

| 排名 | 技能名称 | 安装命令 | 一句话说明 |
| :--- | :--- | :--- | :--- |
| 5 | ATXP | clawhub install atxp | 高级系统级工具，用于扩展核心底层能力 |
| 6 | Google Workspace (No Cloud Console) | clawhub install google-workspace-mcp | 覆盖 Gmail、日历、云盘、文档、表格，无需谷歌云控制台，仅需 OAuth 登录，集成复杂度极低 |
| 7 | Agent Browser | clawhub install agent-browser | 浏览器自动化，支持填表、抓取数据、截图 |
| 8 | Summarize | clawhub install summarize | 对长文档、文章、对话进行智能摘要 |
| 9 | GitHub CLI | clawhub install github-cli | 完整支持 Issue/PR/CI 管理 |
| 10 | Sonoscli | clawhub install sonoscli | 控制 Sonos 音响系统，适合智能办公与家居场景 |

### A.2 生产力工具

| 技能 | 安装命令 | 功能 |
| :--- | :--- | :--- |
| Google Workspace (No Cloud Console) | clawhub install google-workspace-mcp | 涵盖 Gmail、日历、云盘、文档、表格，免云控制台，OAuth 即可使用 |
| Summarize | clawhub install summarize | 生成长文档、网页、对话的智能摘要 |
| Obsidian | clawhub install obsidian | Markdown 笔记库，支持 wikilink 双向链接 |
| Notion | clawhub install notion | 数据库与页面同步，可作为长期记忆存储 |
| Todoist | clawhub install todoist | 任务管理，支持标签、优先级、周期规则 |
| Slack | clawhub install slack | 消息收发、频道管理、文件上传 |
| Humanize AI text | clawhub install humanize-ai-text | 去除 AI 痕迹，使写作更自然，内容创作者常用 |
| AgentMail | clawhub install agentmail | IMAP/SMTP 邮件管理、摘要、自动回复 |
| Calendar | clawhub install calendar | 日历管理与排程，支持创建活动、管理会议并跨平台同步 |

### A.3 开发工具

| 技能 | 安装命令 | 功能 |
| --- | --- | --- |
| GitHub | clawhub install github | Issue/PR/工作流管理，gh CLI 完整支持 |
| Wacli | clawhub install wacli | 多功能 CLI 工具，覆盖系统与开发任务 |
| ByteRover | clawhub install byterover | 多用途任务处理，兼顾实用与开发 |
| Agent Browser | clawhub install agent-browser | 浏览器自动化：填表、抓数据、截图 |
| Playwright | clawhub install playwright | 无头浏览器，表单填写、数据提取、截图 |
| Code Reviewer | clawhub install code-reviewer | Diff 分析、代码审查、Commit 消息生成 |
| SQL Toolkit | clawhub install sql-toolkit | PostgreSQL/MySQL/SQLite 只读查询 |
| Azure DevOps | clawhub install azure-devops | 项目、仓库、看板、流水线对话式管理 |
| Auto-Updater | clawhub install auto-updater | 每日自动更新 OpenClaw 及所有已装技能，通过 Cron 运行并推送变更摘要 |

### A.4 AI 自进化与 Agent 增强
该分类是 ClawHub 的核心特色，下载集中度高。

| 技能 | 安装命令 | 功能 |
| --- | --- | --- |
| Capability Evolver | clawhub install capability-evolver | 下载量第一的 AI 自主进化引擎，可持续提升 Agent 能力 |
| Self-Improving Agent | clawhub install self-improving-agent | 从交互中学习并优化响应质量 |
| Proactive Agent | clawhub install proactive-agent | 主动式 Agent 框架，可自主发起任务 |
| Find Skills | clawhub install find-skills | 在对话中直接搜索技能，是一款帮助发现与安装 Agent 的元技能 |
| ATXP | clawhub install atxp | 高级系统扩展工具，强化底层能力 |
| Arc Agent Lifecycle | clawhub install arc-agent-lifecycle | 管理自主 Agent 的完整生命周期 |

### A.5 搜索与信息获取

| 技能 | 安装命令 | 功能 |
| --- | --- | --- |
| Tavily Search | clawhub install openclaw-tavily-search | Tavily AI 搜索引擎，搜索结果质量较高 |
| Weather | clawhub install weather | 天气、交通、航班实时数据 |
| News Aggregator Skill | clawhub install news-aggregator-skill | 聚合 GitHub 趋势、36kr、Hacker News 等来源的实时内容并分析过滤 |
| ArXiv Search | clawhub install arxiv-search-collector | 学术论文检索，支持按语言筛选 |
| Web Clipper | clawhub install web-clipper | 一键保存网页到 Notion/飞书文档 |

### A.6 运维与基础设施

| 技能 | 安装命令 | 功能 |
| --- | --- | --- |
| AWS Infra | clawhub install aws-infra | EC2、S3、Lambda 的对话式管理 |
| Azure DevOps | clawhub install azure-devops | 项目、仓库、看板、流水线管理 |
| Arc Security Audit | clawhub install arc-security-audit | 对 Agent 完整技能栈进行安全审计 |

### A.7 智能家居与多媒体

| 技能 | 安装命令 | 功能 |
| :--- | :--- | :--- |
| Sonoscli | clawhub install sonoscli | Sonos 音响控制：播放、分组合并、音量调节 |
| Home Assistant | clawhub install home-assistant | 智能家居设备控制，完全本地运行，数据不出网 |
| ElevenLabs Agents | clawhub install elevenlabs-agents | 接入 ElevenLabs，使“龙虾”可通话、语音交互 |
| Openai Whisper | clawhub install openai-whisper | 语音转文字，支持中文，可离线使用 |

## 附录 B 官方配置文档示例（JSON）