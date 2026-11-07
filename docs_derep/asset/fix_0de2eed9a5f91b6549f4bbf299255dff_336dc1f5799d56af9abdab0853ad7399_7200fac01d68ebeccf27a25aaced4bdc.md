从简历处理到结果呈现，整个流程可以拆成五个环节：

4. 撰写推荐或淘汰的依据
5. 给出候选人排序结果

这五步在 OpenClaw 里的对应能力是：

1. 上传 → 文件读取工具
2. 提取 → 文档解析结合 AI 理解
3. 打分 → 依据规则进行推理
4. 生成理由 → 文本生成
5. 输出 → 格式化输出

##### 第三步：准备核心文件
AGENTS.md 用来定义角色与行为边界：

> 你是一名专注于 HR 简历初筛的 AI 助手。

职责范围：
- 快速浏览简历，抓取关键要素
- 参照岗位要求判断匹配程度
- 给出清晰明确的推荐或不推荐说明

工作原则：
- 保持中立客观，避免个人倾向
- 同时考量硬性条件如学历、从业年限，和软性条件如项目经历、技能契合度
- 当信息不足时，需明确提示“缺少××信息”

不介入的事项：
- 不回应与筛选无关的提问，如日常闲聊
- 不提供职业规划建议
- 不对候选人的长相、性别、年龄等作任何评价

预装 Skills：
搭建一个“简历解析”技能，演示如何从简历中抽取信息、如何评分、如何呈现结论。

模板文件：
准备若干常见岗位的需求模板，例如 Java 工程师要求、产品经理要求、运营专员要求等。

##### 第四步：测试和优化
找真实用户实操：

- 提供真实简历跑通全流程
- 观察输出质量
- 收集反馈：好用之处、卡点、看不懂的地方

常见问题与改进方向：

- 抽取信息不准确 → 优化 Skill 的提取策略
- 评分结果不合理 → 调整评分规则
- 输出格式混乱 → 统一输出模板
- 用户无从下手 → 增加引导提示

反复迭代，直到 80% 的测试用户认为可用。

##### 第五步：打包和分发
内部使用：

- 部署到内网服务器
- 撰写简要使用手册
- 安排约半小时的培训

对外客户：

- 提供安装包或云端访问链接
- 提供详细文档与视频教程
- 提供技术支持，初期需快速响应

商业化考量：

- 设计计费模式，按人头、用量或年费订阅
- 准备销售资料，功能介绍与案例展示
- 关注法律风险，数据隐私与服务协议

#### 6.10.4 商业化的三个注意事项

##### 注意一：成本要算清楚
调用 OpenClaw 的大模型会产生费用，成本分摊有三种做法：

1. 用户自行配置 API Key：成本由用户承担，但配置门槛高
2. 统一采购额度：自行购买模型额度后按量转售，需核算利润空间
3. 免费版加付费版：免费版使用基础模型并限量，付费版使用强力模型不限量

##### 注意二：数据隐私要保护
简历、合同、客户信息等进入系统后，安全责任需明确。

基本要求：
- 不将用户数据用于其他用途
- 不向第三方泄露用户数据
- 定期清理不再需要的数据
- 云端部署需符合相关数据保护法规

##### 注意三：技术支持要准备好
产品化后，用户遇到问题会找你而非官方。

需提前准备：
- 常见问题解答文档
- 故障排查流程
- 联系方式，邮箱、微信、电话
- 初期有足够的耐心解答各类咨询

### 小结
本书到此结束。若你坚持下来，你已从 OpenClaw 的使用者成长为真正的“养虾人”。

本章要点：让 OpenClaw 更轻盈，精简 MEMORY.md、清理日志、关闭闲置插件；让记忆更持久，手动编辑 MEMORY.md、定期整理 Daily Logs；实现多 Agent 协作，构建多 Agent 架构、消息路由、子 Agent 后台任务；建立安全边界，工具白名单、沙箱隔离、安全审计；快速排障，status 检查、日志追踪、doctor 诊断；混合使用云端与本地模型，Fallback 链、Ollama 本地部署；控制 API 开销，压缩上下文、设置预算上限、定期清理；备份与迁移，完整归档、校验备份、跨设备迁移；并尝试将 OpenClaw 包装成自己的工具，配置包、云端部署、定制界面、功能锁定。

从第一章的安装配置，到二至五章的实战，再到本章的深度优化，你已走完从“用虾”到“养虾”的完整路径。如今的 OpenClaw 是一个经过深度训练、安全可控、可长期维护的 AI 生产力系统。

## 最后的话

### 从“用虾”到“养虾”再到“造虾”
OpenClaw 的价值在于它有多“属于你”。

开源、免费、可定制，数据掌握在自己手上，规则由自己制定。你投入多少心力去训练，它就回报多少。就像养宠物，前期要立规矩、设围栏、喂数据；养熟后就成为不可或缺的助手。

从安装那一刻起，“养虾之旅”就开始了。这段旅程可能会遇到报错、账单超支、数据丢失，但掌握本章技巧后，这些问题都能规避或快速修复。

**技术会过时，原则不会。** 具体配置会变化，但最小权限、定期备份、成本可控、数据隔离这些原则长期有效。

**效率的尽头是克制。** OpenClaw 能做的事很多，但并非所有都值得让它做。真正会用工具的人知道何时使用、何时不用、何时停止。

**工具是手段，不是目的。** 它帮助你节省时间、提升效率，但省下的时间用来做什么、效率提升创造什么价值，只有你自己能回答。

会用工具的人很多，会养工具的人不多。你看到这里，说明你已不满足于“用虾”，而是想“养好虾”，甚至“造自己的虾”。

继续走下去吧。OpenClaw 的世界很大，这本书只是起点。

感谢阅读，祝你“养虾”顺利！

## 附录 A

### ClawHub Skills 完整手册
本附录基于 ClawHub 真实下载数据整理。ClawHub 是 OpenClaw 的公共技能注册中心。截至 2026 年 3 月 21 日，注册技能达 31K+。最新列表可访问 clawhub.ai，或执行 clawhub list --popular 查看。

> 注意事项
安全提示：2026 年初 ClawHub 出现过 “ClawHavoc” 事件，随后清除 341 个恶意技能。目前 ClawHub 已与 VirusTotal 合作，对所有技能进行自动扫描。建议遵循 “100/3 原则”：优先选择下载量超过 100 次、上线超过 3 个月的技能，安装前在 clawhub.ai 查看 VirusTotal 扫描报告。

### A.1 下载榜 TOP 10
表 A-1 ClawHub 下载量前十名，2026 年 3 月数据

| 排名 | 技能名称 | 安装命令 | 一句话说明 |
| :--- | :--- | :--- | :--- |
| 1 | Capability Evolver | clawhub install capability-evolver | 自进化引擎，让 Agent 自主提升能力，下载量远超第二名 |
| 2 | Wacli | clawhub install wacli | CLI 多功能瑞士军刀，覆盖开发与系统任务 |
| 3 | ByteRover | clawhub install byterover | 多用途任务处理器，兼顾实用与开发 |
| 4 | Self-Improving Agent | clawhub install self-improving-agent | 自我学习框架，社区评分领先 |

## 附录 A ClawHub Skills 完整手册

### A.1 核心技能

| 排名 | 技能名称 | 安装命令 | 一句话说明 |
| :--- | :--- | :--- | :--- |
| 5 | ATXP | clawhub install atxp | 系统级扩展工具，增强底层能力 |
| 6 | Google Workspace (No Cloud Console) | clawhub install google-workspace-mcp | 集成 Gmail、日历、云盘、文档、表格，无需控制台，仅需 OAuth 登录 |
| 7 | Agent Browser | clawhub install agent-browser | 浏览器自动化，填表、抓数据、截图 |
| 8 | Summarize | clawhub install summarize | 长文档、文章、对话的智能摘要 |
| 9 | GitHub CLI | clawhub install github-cli | Issue/PR/CI 管理全覆盖 |
| 10 | Sonoscli | clawhub install sonoscli | Sonos 音响控制，适合办公与家居场景 |

### A.2 生产力工具

| 技能 | 安装命令 | 功能 |
| :--- | :--- | :--- |
| Google Workspace (No Cloud Console) | clawhub install google-workspace-mcp | Gmail、日历、云盘、文档、表格集成，免控制台 OAuth 即可使用 |
| Summarize | clawhub install summarize | 生成长文档、网页、对话的摘要 |
| Obsidian | clawhub install obsidian | Markdown 笔记库，支持 wikilink 双向链接 |
| Notion | clawhub install notion | 数据库与页面同步，可作长期记忆存储 |
| Todoist | clawhub install todoist | 任务管理，支持标签、优先级、周期规则 |
| Slack | clawhub install slack | 消息收发、频道管理、文件上传 |
| Humanize AI text | clawhub install humanize-ai-text | 去除 AI 痕迹，让写作更自然 |
| AgentMail | clawhub install agentmail | IMAP/SMTP 邮件管理、摘要、自动回复 |
| Calendar | clawhub install calendar | 日历管理与排程，支持创建活动、跨平台同步 |

### A.3 开发工具

| 技能 | 安装命令 | 功能 |
| --- | --- | --- |
| GitHub | clawhub install github | Issue/PR/工作流管理，完整支持 gh CLI |
| Wacli | clawhub install wacli | 多功能 CLI，覆盖系统与开发任务 |
| ByteRover | clawhub install byterover | 多用途任务处理，实用与开发兼顾 |
| Agent Browser | clawhub install agent-browser | 浏览器自动化：填表、抓数据、截图 |
| Playwright | clawhub install playwright | 无头浏览器，表单填写、数据提取、截图 |
| Code Reviewer | clawhub install code-reviewer | Diff 分析、代码审查、Commit 消息生成 |
| SQL Toolkit | clawhub install sql-toolkit | PostgreSQL/MySQL/SQLite 只读查询 |
| Azure DevOps | clawhub install azure-devops | 项目、仓库、看板、流水线对话式管理 |
| Auto-Updater | clawhub install auto-updater | 每日自动更新 OpenClaw 及已装技能，通过 Cron 运行并推送变更摘要 |

### A.4 AI 自进化与 Agent 增强

| 技能 | 安装命令 | 功能 |
| --- | --- | --- |
| Capability Evolver | clawhub install capability-evolver | 下载量第一的自主进化引擎，可持续提升 Agent 能力 |
| Self-Improving Agent | clawhub install self-improving-agent | 从交互中学习并优化响应 |
| Proactive Agent | clawhub install proactive-agent | 主动式 Agent 框架，可自主发起任务 |
| Find Skills | clawhub install find-skills | 对话中搜索技能，元技能 |
| ATXP | clawhub install atxp | 高级系统扩展，强化底层能力 |
| Arc Agent Lifecycle | clawhub install arc-agent-lifecycle | 管理自主 Agent 的完整生命周期 |

### A.5 搜索与信息获取

| 技能 | 安装命令 | 功能 |
| --- | --- | --- |
| Tavily Search | clawhub install openclaw-tavily-search | Tavily AI 搜索引擎，结果质量高 |
| Weather | clawhub install weather | 天气、交通、航班实时数据 |
| News Aggregator Skill | clawhub install news-aggregator-skill | 聚合 GitHub 趋势、36kr、Hacker News 等实时内容并分析 |
| ArXiv Search | clawhub install arxiv-search-collector | 学术论文检索，支持按语言筛选 |
| Web Clipper | clawhub install web-clipper | 一键保存网页到 Notion/飞书文档 |

### A.6 运维与基础设施

| 技能 | 安装命令 | 功能 |
| --- | --- | --- |
| AWS Infra | clawhub install aws-infra | EC2、S3、Lambda 对话式管理 |
| Azure DevOps | clawhub install azure-devops | 项目、仓库、看板、流水线管理 |
| Arc Security Audit | clawhub install arc-security-audit | 对 Agent 技能栈进行安全审计 |

### A.7 智能家居与多媒体

| 技能 | 安装命令 | 功能 |
| :--- | :--- | :--- |
| Sonoscli | clawhub install sonoscli | Sonos 音响控制：播放、分组合并、音量调节 |
| Home Assistant | clawhub install home-assistant | 智能家居设备控制，完全本地运行 |
| ElevenLabs Agents | clawhub install elevenlabs-agents | 接入 ElevenLabs，实现电话与语音交互 |
| Openai Whisper | clawhub install openai-whisper | 语音转文字，支持中文，可离线使用 |

## 附录 B 官方配置文档示例（JSON）