OpenClaw 技能全景与落地配置

Notion 相关的技能在 SKILL.md 里详细说明了如何拼装带 Notion-Version 请求头的 curl 调用，并演示了对复杂 JSON 的解析与写入操作，用来驱动 Notion 的内容块，是学习 API 对接的一个很典型的参考实现。

3．Obsidian
该技能由 @steipete 维护，面向喜欢用本地 Markdown 做知识库的用户。通过 obsidian-cli 与本地的 Obsidian Vault 通信，支持读取、创建与修改 Markdown 笔记。例如在默认 Vault 中新建一份会议纪要，或检索知识库里与某个主题相关的全部笔记，都能完成。与 Notion 方案不同的是，此技能完全离线运行，无需网络连接，对隐私要求较高的场景更友好。

4．Trello
面向看板式项目协作的团队，借助 Trello REST API 让 OpenClaw 管理看板 Board、列表 List 与卡片 Card。可在“产品迭代”看板里的“待开发”列表下创建任务卡，或把已完成的卡片搬到“已完成”列表。该实现把 curl 与 jq 结合起来，展示了如何优雅地解析外部 API 返回的复杂 JSON 并抽取出 Agent 可用的关键信息。

5.2.2 开发与运维类
对开发者而言，OpenClaw 配合这一类技能可成为全能的结对编程助手，覆盖代码管理、网页自动化、API 集成等环节。

1．GitHub
是开发者使用频率最高的技能之一，深度整合官方 gh CLI，让 OpenClaw 承担代码审查与 CI/CD 助手角色。可以查询某仓库特定 PR 的 CI 运行状态，列出当前仓库所有 Open 状态的 Issue 并按创建时间排序，或梳理某个 PR 的代码改动。SKILL.md 明确划分了适用场景，例如查看状态、创建 Issue，以及不适用场景，例如本地 git 提交、复杂的代码审查应交由专业工具。该种清晰的边界定义是高质量技能编写的典范。

2．Agent Browser
由 @TheSethRose 开发维护。基于 Rust 的轻量无头浏览器自动化 CLI，为 OpenClaw 提供网页导航、元素点击、文本输入与截图能力。在网页自动化测试、数据采集、复杂交互等场景中不可或缺。例如让 OpenClaw 打开 Hacker News 首页，抓取前十篇文章的标题与链接并导出为 CSV。

3．API Gateway
由 @byungkyu 开发，提供统一入口连接 Slack、Airtable、HubSpot 等 100 多种第三方 API，并内置托管式 OAuth 认证，大幅降低调用复杂度。对于需要在单一工作流中串联多款 SaaS 服务的集成需求，能显著节省开发成本。

4．Coding-Agent
一种特殊的元技能，允许 OpenClaw 在后台 Spawning 并把任务委托给专门的编码 Agent，如 Codex、Claude Code 或 Aider。当需要在某个项目目录里启动编码 Agent 重构用户认证模块时即派上用场。技术上展示了 OpenClaw 的后台进程管理能力：通过 Bash 工具的 pty:true 分配伪终端与 background:true 后台运行参数，可管理长时间运行的交互式 CLI，并借助 process 工具轮询状态、读取日志。

5.2.3 智能体增强类
这类技能聚焦提升 OpenClaw 自身的智能水平、自我反思与主动性，是推动 Agent 能力进化的核心工具。

1．Self-Improving-Agent
ClawHub 上下载量很高的经典技能，由 @pskoett 开发。能够记录 Agent 运行时的学习经验、错误信息以及用户的纠正指令，实现持续自我优化。命令执行失败或用户指出问题时会自动记录并调整后续行为模式，安装后使用体验会随时间逐步贴合个人习惯。

2．Proactive Agent
由 @halthelobster 开发，把 AI Agent 从被动跟随指令转变为主动协作伙伴。引入 WAL 预写日志协议、工作缓冲区与自主 Cron 任务等机制，使 Agent 具备自主规划与执行能力。例如每日开机时主动生成邮件摘要与日程提醒。

3．Skill Vetter
由 @spclaudehome 开发，是一款以安全审视为核心的技能审查工具。安装 ClawHub 或 GitHub 上的新技能前，可用它检查潜在风险、权限范围与可疑代码模式。它会分析技能中的脚本文件，标注可能存在风险的操作，如删除文件、访问敏感目录、发起网络请求等，帮助提前做出审慎决策。

5.2.4 内容处理与创作类
这类技能擅长处理文本、音频、图像与文档文件，能明显提升内容创作与信息处理的效率。

1．Summarize
内容处理领域的热门工具，面向信息过载场景。可对网页、PDF、图片、音频乃至 YouTube 视频进行多模态总结，例如提炼长篇技术博客的核心观点，或抽取视频字幕并生成摘要。技术上体现了多模态输入的处理思路：调用大模型完成文本摘要，同时接入 Firecrawl 处理有反爬机制的站点、Apify 获取 YouTube 字幕作为后备方案，提升鲁棒性。支持 OpenAI、Anthropic、Google 等多家模型后端，可选用 GPT、Claude、Gemini 等系列。

2．Nano Banana Pro
图像生成与编辑的核心工具，基于 Gemini 3 Pro Image 模型，支持文生图与图生图，最高输出 4K 分辨率。可生成赛博朋克风格的未来城市夜景，或对已有图片进行风格转换。脚本在生成后会输出一行特殊的 MEDIA: 标记，OpenClaw Gateway 捕获后自动将图片作为附件发送到 Telegram、Discord 等支持的聊天渠道，实现生成到分发的无缝衔接。

3．Nano PDF
面向频繁处理 PDF 的办公场景，支持用自然语言编辑 PDF，包括合并多份文件、拆分页、提取指定页面、添加水印等，节省大量手工操作时间。

4．Sag
集成了 ElevenLabs 高质量文本转语音 API，提供类似 macOS say 命令的简洁体验。可指定不同声音角色如 Roger、Sarah 等朗读文本。演示了如何通过环境变量 ELEVENLABS_API_KEY 安全注入 API 密钥并在本地播放音频，是外部 API 集成的典型学习案例。

通过以上技能可见 OpenClaw 的能力边界高度可扩展，只要有对应的命令行工具或 API，就能通过编写技能转化为 AI Agent 的得力助手。

5.3 技能的安装与使用
了解 ClawHub 概念与技能生态后，可动手将技能装到本地 OpenClaw 并在对话中高效使用。

5.3.1 安装 ClawHub CLI
与 ClawHub 交互首先需要安装命令行工具。ClawHub CLI 基于 Node.js，需先安装 Node.js v22 或更高版本及 npm。执行全局安装：

# 使用 npm 安装
npm install -g clawhub

安装完成后用 clawhub -V 验证，若正确输出版本号即表示就绪。

5.3.2 通过 ClawHub CLI 命令安装与更新
以 tavily-search-skill 为例，该技能集成 Tavily API，支持实时配额管理与付费模式切换，可执行网络搜索并返回内容片段、相关性评分与元数据。

安装命令非常简洁：
Bash
clawhub install tavily-search-skill

其中 tavily-search-skill 为技能 Slug，通常可在 _meta.json 中找到。默认会安装到当前工作目录 ./skills，若已配置 OpenClaw 工作空间，则自动安装到 ~/.openclaw/workspace/skills，确保 OpenClaw 可识别。

指定版本安装：
Bash
clawhub install tavily-search-skill --version 1.0.2

[PRE0]
图 5-5 tavily-search-skill 指定版本的安装（获取 Slug 后执行安装命令）

版本迭代时可智能更新。ClawHub 会计算本地文件哈希并与远程比对后自动升级，并提供批量更新：

# 更新所有已安装技能
clawhub update --all

# 更新特定技能
clawhub update tavily-search

若本地文件被自定义修改，普通 update 会跳过以免覆盖，可用 --force 强制执行。目前 CLI 无专用卸载命令，需直接删除对应文件夹：
rm -rf ~/.openclaw/workspace/skills/tavily-search
下次运行时 OpenClaw 会自动将其移出加载列表。

5.3.3 技能的加载机制与优先级
OpenClaw 从三处加载技能并按优先级覆盖。

• 优先级最高：当前 OpenClaw Workspace 对应的技能，位于 <workspace>/skills 目录，也是 clawhub install 的默认路径，适合存放自定义技能。
• 优先级中等：托管/本地技能，位于 ~/.openclaw/skills，通常用于多 Agent 共享的本地技能。
• 优先级最低：内置技能，随安装包发布的官方核心技能。

若三处存在同名技能，高优先级版本会覆盖低优先级版本，可在不改动核心代码的情况下替换内置技能。此外可在配置文件 ~/.openclaw/openclaw.json 中通过 skills.load.extraDirs 增加额外扫描目录，优先级最低，满足企业级部署需求。

OpenClaw 默认开启热重载，持续监听技能目录变化，默认防抖 250 毫秒。安装新技能或修改 SKILL.md 后会在极短时间内自动刷新快照，无需重启 Gateway，对开发调试十分便利。

5.3.4 在对话中调用已安装的技能
提供两种主要调用方式：自然语言触发与 Slash Command 强制调用。

自然语言触发最符合直觉。每个技能的 SKILL.md 中的 description 字段既面向用户，也用于指导大语言模型理解用途与触发场景。例如安装 weather 技能后输入“今天北京天气怎么样”，Agent Loop 会匹配技能并自动调用工具返回结果。

Slash Command 强制调用适用于明确指定技能的场景。若技能配置 user-invocable: true，默认开启，OpenClaw 会自动生成斜杠命令，格式：
/skill <技能名称> [参数]
例如强制用 summarize 总结网页：
/skill summarize https://example.com/article
该方式绕过意图识别，直接传递指令，执行更确定，调试时尤为有用。

5.4 环境变量配置详解
许多调用外部 API 的技能需配置环境变量如 API Key 与访问令牌。OpenClaw 提供多种配置方式，理解优先级与生效时机至关重要。

5.4.1 系统级环境变量
最基础的方式，在 Shell 配置文件如 ~/.bashrc、~/.zshrc 或 ~/.profile 中导出变量：
# 在 ~/.bashrc 或 ~/.zshrc 中添加
export GEMINI_API_KEY="your_gemini_api_key_here"
export GITHUB_TOKEN="your_github_token_here"

作用范围全局，其他程序也可读取。修改后需执行 source ~/.bashrc 或打开新终端使当前会话生效，已运行的 OpenClaw Gateway 因启动时已读取环境变量，需重启后才能识别新变量。该方式适合全系统通用的密钥，但安全性相对较低。

5.4.2 .env 文件配置
OpenClaw 支持在工作空间根目录通常是 ~/.openclaw/ 下读取 .env 文件，将敏感信息集中管理，避免污染系统环境变量。

在 ~/.openclaw/.env 中按键值对配置：
GEMINI_API_KEY=your_gemini_api_key_here
OPENAI_API_KEY=your_openai_api_key_here
ELEVENLABS_API_KEY=your_elevenlabs_api_key_here

作用范围仅限当前 OpenClaw 实例。OpenClaw 启动时会加载该文件，运行期间修改需重启后生效。需注意 .env 包含敏感密钥，切勿提交到 Git，建议在 .gitignore 中排除。