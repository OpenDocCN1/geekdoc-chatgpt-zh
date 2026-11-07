- [TOOLS.md](TOOLS.md)：工具使用指引
- [HEARTBEAT.md](HEARTBEAT.md)：心跳自检清单
- [BOOT.md](BOOT.md)：重启执行清单
- [BOOTSTRAP.md](BOOTSTRAP.md)：首次启动初始化文件
- memory/：每日记忆日志，按需调用不占用主上下文 Token
- [MEMORY.md](MEMORY.md)：长期沉淀记忆，仅私聊场景加载

工作区并非严格沙箱，相对路径默认指向工作区，绝对路径可直接触达整机，开启沙箱模式后权限会被收紧。

### 14.4 核心代码逻辑拆解

#### 系统提示的组装方式

OpenClaw 每次运行都会为任务定制系统提示，不依赖通用默认模板，而是按固定顺序拼接：工具列表 → 安全规则 → 技能 → 自更新 → 工作区 → 文档 → 注入文件 → 沙箱 → 时区 → 回复标签 → 心跳 → 运行时信息 → 推理级别。

#### 三种提示模式

- full 即完整版，默认使用
- minimal 用于子代理，结构精简只留核心要素
- none 仅保留基础身份行

技能是按需注入，匹配不到相关技能时直接略过，保持提示干净。时间信息只保留时区以维持缓存稳定，具体时间由模型通过工具自行获取。

#### Plugin 系统架构

插件采用四层防护设计，代码全程延迟执行不预跑：

## OpenClaw 开源实践

1. 清单发现：读取配置与清单，锁定可用的插件候选
2. 启用校验：依据元数据判定启用、禁用或阻断
3. 运行加载：在进程内加载并向中央注册表登记能力
4. 表面消费：全局读取注册表，暴露工具、通道、命令等接口

#### 插件分类

- 单一能力：只登记一种能力
- 混合能力：登记多种能力
- 仅 Hook：只登记事件脚本
- 非能力：登记工具、命令或服务

#### 六大能力注册接口

文本生成、语音合成、媒体理解、图像生成、网页搜索、消息通道。

#### Hooks 机制

Hook 是在网关内部触发的轻量脚本，内置四项核心 Hook：会话记忆、额外启动文件、命令日志、开机执行。

#### Hook 加载优先级

内置 > 插件 > 全局管理 > 工作区，需手动开启工作区 Hook。

每个 Hook 包含元数据文档与执行处理器，支持类似 npm 包的安装方式。

#### 安全模型与信任边界

OpenClaw 采用个人助手安全模型设计，并非面向对抗的多租户隔离方案，多敌对用户需拆分独立 Gateway/宿主机。

#### 核心信任边界

- 网关认证：校验 API 访问身份
- sessionKey：仅用于路由，不是授权凭证
- 执行审批：提供操作护栏，非强隔离
- 沙箱+主机隔离：最强安全边界

建议定期运行 `openclaw security audit`，排查认证泄露、权限异常、文件系统与执行策略等风险。

### 14.5 源码阅读技巧与重点

#### 先从官方文档索引入手

读源码前先通读核心文档，构建完整概念模型：架构、Agent 循环、协议、会话、队列、定时任务、提示词、流式、插件、安全。

#### 协议优先，从 TypeBox Schema 切入

协议类型早于实现代码，先看 `src/gateway/protocol/schema.ts`，理清类型后再追踪处理器与执行逻辑。

#### 顺着数据流追踪

入站消息标准路径：平台事件 → Channel 适配器 → 标准化元数据 → 消息队列 → Agent 运行 → 工具调用 → 流式分块 → 通道出站 → 平台 API。

#### 区分 Plugin API 新旧演进

- 旧版：`before_agent_start`，兼容但已废弃
- 新版：六大注册方法 + `before_model_resolve`/`before_prompt_build`

#### 安全模块阅读要点

- 高危工具清单：拦截危险路径
- 外部内容隔离：防范提示注入
- 安全审计：命令映射到实现规则
- DM 策略：多用户隔离的核心逻辑

#### 会话维护机制

通过 `session.maintenance` 配置过期清理、数量上限、文件轮转、磁盘配额，并可预览清理效果，在存储与性能间平衡。

#### 诊断命令辅助阅读

- `security audit`：进入安全规则
- `plugins inspect`：查看插件类型与能力
- `doctor`：配置与健康检查
- `cron list`：校验调度状态
- `/context list`：查看上下文消耗
- `sessions cleanup`：预览清理效果

#### 推荐阅读顺序

1. 架构层：协议 → 网关服务 → 会话管理
2. 执行流：Agent 运行 → 提示词 → 工具 → 流式
3. 扩展层：定时任务 → 插件 → Hook
4. 安全层：危险工具 → 内容隔离 → DM 策略 → 审计规则

## 第15章

## Agent 与 Skills 体系

## OpenClaw 开源实战

如果你用过各种 AI 聊天工具，可能会遇到这种体验：问完问题得到答案，对话就结束，下次打开又不记得了。这不是模型变笨，而是这些工具的边界本就如此——它们是应答机，不是真正的助手。

OpenClaw 想突破这条线。它不只是收消息回消息，而是让 Agent 真正“住”在你的系统里：记得昨天的上下文，能在你休息时静默办事，通过 WhatsApp、Telegram、Discord 找到你，需要时调用工具、操作文件、执行脚本。

要实现这一点，OpenClaw 在底层构建了一套精密架构。理解这套架构是用好 OpenClaw 的前提。文章将从 Agent Runtime 开始，依次拆解工作空间机制、执行循环、多 Agent 路由、记忆系统、系统提示词，以及 Skills 扩展体系与沙盒安全边界，重点解释设计的“为什么”。

### 15.1 核心架构与运行时

#### Agent Runtime：嵌入式运行时的设计思路

在整体架构中，Agent Runtime 是核心层，负责模型调用、工具执行、流式输出和会话状态管理。它基于 pi-agent-core 构建，OpenClaw 在其上叠加了会话管理、消息发现、工具接线与通道投递层。

官方表述很清晰：“Session management, discovery, tool wiring, and channel delivery are OpenClaw-owned layers on top of that core”。这体现了推理核心与平台能力的严格解耦。

解耦的好处是切换模型不影响会话历史、路由规则和通道配置。今天用 Anthropic 的 Claude，明天换 OpenAI 的 GPT 或自托管 Ollama，也不会动到上层逻辑。反之，通道能力或工具策略更新也不需要改动推理核心。

#### 模型引用格式

配置中引用模型采用 provider/model 形式，例如 anthropic/claude-opus-4-6。若模型 ID 本身包含斜杠，如 OpenRouter 风格，需在前加 provider 前缀，例如 openrouter/moonshotai/kimi-k2，避免解析歧义。若省略 provider 前缀，仅在模型 ID 不含斜杠时会被视作别名或默认提供商处理。

#### 工具体系与工具策略

Agent 运行时内置核心工具：read、write、edit、exec 以及若干系统辅助工具。这些工具默认可用，但最终能否调用由工具策略层决定。TOOLS.md 不控制可用性，它只是使用说明书，说明如何使用、注意哪些惯例；真正控制可用性的是配置文件。

工具可用性受四层约束：全局 tools.allow/tools.deny 列表、单 Agent 级 agents.list[].tools 配置、沙盒模式下 sandbox.tools 策略、授权发件人专属 tools.elevated 逃生舱，允许特定 exec 调用绕过沙盒在宿主机运行。四层由粗到细构成权限体系。

apply_patch 是可选工具，默认关闭，需在 tools.exec.applyPatch 中显式开启。它通过统一 diff 格式修改文件，比直接 exec 更安全，只能做精确增删改，适合代码编辑场景。

#### 单 Agent 模式与多 Agent 模式

默认是单 Agent 模式，agentId 为 main，所有通道输入默认路由至它。会话 key 格式为 agent:main:<mainKey>，mainKey 默认也是 main。

在 openclaw.json 中配置 agents.list 并添加多个 Agent 后，系统切换为多 Agent 模式。每个 Agent 拥有独立三件套：workspace 工作空间根目录、agentDir 状态目录 ~/.openclaw/agents/<agentId>/agent 用于存放认证 profile 与单 Agent 配置、sessions 存储 ~/.openclaw/agents/<agentId>/sessions 用于聊天历史与路由状态。三者严格隔离，不共享。

#### 会话转录的存储格式

每段对话完整记录以 JSONL 格式存于 ~/.openclaw/agents/<agentId>/sessions/<SessionId>.jsonl。JSONL 即逐行 JSON，便于追加与逐行读取。

Session ID 由 OpenClaw 分配并保持稳定，不依赖底层 LLM 的会话 ID。即使更换模型，同一会话的历史仍能正确读取恢复，因为身份由 OpenClaw 管理。

#### 引导文件：Agent 的出生仪式

新会话启动时，Agent 并非空启动。OpenClaw 会将工作区内的特殊文件注入上下文，即引导文件机制，让 Agent 知道身份、规则与服务对象。

标准引导文件包括：

- AGENTS.md：操作规程与行为优先级，每次会话加载
- SOUL.md：人格设定、边界与语气，每次会话加载
- USER.md：用户画像与称呼偏好，每次会话加载
- IDENTITY.md：名称、气质与 Emoji 标识，在 bootstrap 初始化时创建更新
- TOOLS.md：本地工具使用指引，不控制可用性
- HEARTBEAT.md：可选心跳清单，建议简短控制 Token
- BOOTSTRAP.md：一次性首次运行文件，新工作区创建后建议删除
- MEMORY.md：精编长期记忆，仅主私聊加载，群组等共享上下文不加载以防泄漏

空文件会被静默跳过，超大文件会被截断并标注。单文件上限由 agents.defaults.bootstrapMaxChars 控制，默认 20000；总注入上限由 agents.defaults.bootstrapTotalMaxChars 控制，默认 150000。

子 Agent 会话由 sessions_spawn 创建时，仅注入 AGENTS.md 与 TOOLS.md，其余引导文件全部过滤。这是刻意设计，子 Agent 为短暂任务型，无需完整人格与私人记忆，精简可降 Token 消耗。

### 15.2 工作空间与执行循环

#### Agent Workspace：把文件当记忆用

工作空间是 Agent 核心工作目录，也是文件操作与上下文注入的唯一根。官方定义：“Keep it private and treat it as memory”。它既是工作台，也是唯一持久化状态区。

需区分 ~/.openclaw/ 系统目录与工作区。系统目录存放配置、凭据、会话记录；工作区默认 ~/.openclaw/workspace，用于存放生成的笔记与知识。

注意安全点：工作区是默认 cwd，但不是硬隔离沙箱。绝对路径仍可访问主机其他位置，真正的文件系统隔离需显式启用沙盒。

#### 默认位置与自定义

默认路径为 `~/.openclaw/workspace`。若设置 OPENCLAW_PROFILE 且非 default，路径变为 `~/.openclaw/workspace-<profile>`，支持多套隔离配置。

可在 `~/.openclaw/openclaw.json` 通过 `agent.workspace` 自定义路径，例如 `~/Projects/my-agent-workspace`。`openclaw setup` 可初始化工作区并生成引导模板，已有文件不受影响。

#### 工作空间目录结构

- `skills/` 可选，工作区级 Skills 目录，优先级最高可覆盖托管技能
- `memory/` 推荐，每日记忆日志，按 `YYYY-MM-DD.md` 存储，通过 memory_search 与 memory_get 按需读取
- `canvas/` 可选，Canvas UI 文件目录，如 `canvas/index.html`
- `BOOT.md` 可选，Gateway 重启时执行清单，需启用 internal hooks，建议简短

不适合放入工作区的内容：`~/.openclaw/openclaw.json` 主配置含 API Key、`~/.openclaw/credentials/` 各类 OAuth Token、`~/.openclaw/agents/<agentId>/sessions/` 会话转录、`~/.openclaw/skills/` 托管 Skills。这些不应混入工作区，更不应提交 Git。

#### Git 版本控制与备份

官方建议将工作区作为私有 Git 仓库备份，便于迁移恢复与误删回滚。有 Git 时初始化会自建仓库。

推荐 .gitignore 规则：.DS_Store、.env、**/*.key、**/*.pem、**/secrets*。即使私有仓库也避免明文存放凭据，建议使用 SecretRef 对象 { source, provider, id } 从环境变量或密钥管理器读取。

#### Agent Loop：一次完整对话的内部旅程

Agent Loop 描述用户消息如何变成行动与回复的全过程，涵盖接入到持久化的全部步骤，是动态运行核心。

官方定义：“单次完整运行全流程：消息接入 → 上下文组装 → 模型推理 → 工具执行 → 流式回复 → 状态持久化”。一次 loop 在会话内串行执行，推理、工具调用、流式输出期间会触发生命周期与流式事件，Gateway 订阅后转发至通道实现实时流式。

#### 两个触发 Loop 的 RPC 命令