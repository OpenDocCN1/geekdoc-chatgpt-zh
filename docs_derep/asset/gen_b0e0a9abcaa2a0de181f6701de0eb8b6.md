主流混合专家模型在四块RTX 4090上就能实现顺畅推理，换成4到8位量化后，推理需求甚至压缩到两张卡就能搞定。与此同时，DeepSeek团队推出的V2大模型，基于MoE架构打造，总参数体量2360亿，但单次前向仅激活21亿参数，综合表现已与GPT-4处于同一水平线。V2的经济性相当突出，百万输入Token定价约1元，输出约2元；配合提示词缓存机制，命中时输入成本还能拉低到0.1元每百万Token。部署层面，它适配单机8卡80G H800环境。这次进展重塑了中外开源生态，也让中小企业多场景落地变得可行。2025年1月前后，DeepSeek V3与推理专用的R1接连面世，配套放出了1.5B到70B规模的蒸馏小模型家族，全系开源，并且采用强化学习新范式做训练，显著压低了落地门槛，点燃了国内AI应用的爆发潮。沐曦、海光、华为等本土算力与云服务厂商迅速跟进，尤其是华为的加入加速了全产业链的国产化布局，为大中型企业和机构提供了可信的本地化部署路径。

更关键的是，本地个人电脑跑大模型不再是概念，智能体可以直接在家用机上私有化部署，这为AI的个性化定制与数据安全打开了新空间。

接下来，我们将对全球范围内的智能体平台做一次系统盘点，试图绘制一张行业的索引地图。新手可以借此建立全局认知，专业研究者则能借此洞察版图走向，挖掘研究切口，也能看到更多国际化选项。

提醒一下：这个赛道迭代极快，平台诞生与消亡都很常见，用的时候务必结合最新实际情况甄别。

#### 2.1 常见的智能体工具

##### 2.1.1 编码工具

编码类智能体平台数量众多，这里仅挑选一批具有代表性的产品进行展示，如图2-1所示。

[PRE0]

图2-1 编码智能体平台

部分编码智能体平台的简介如表2-1所示。

表2-1 部分编码智能体平台的简介

| 序号 | 工具名称 | 介绍 |
| :--- | :--- | :--- |
| 1 | Vanna.AI | Vanna.AI 是基于Python的开源检索增强生成方案，主攻SQL生成任务。训练好RAG后可自动撰写SQL语句，直连数据库提取可视化洞察 |
| 2 | ReactAgent | ReactAgent 属于实验性质的智能体，依托GPT-4进行React组件的生成与组合，兼容本地设计系统与原子化设计规范 |
| 3 | WorkGPT | WorkGPT 是类似AutoGPT或LangChain风格的智能体框架，能按指令和API自动完成任务，例如网络调研、网站抓取等 |
| 4 | AutoPR | AutoPR 是一款代码治理型智能体，实时总结代码、追踪待办、记录API调用历史，并允许通过YAML配置自定义工作流 |
| 5 | DemoGPT | DemoGPT 基于LangChain构建，可把静态提示转化为动态产品，并用来搭建交互式的Streamlit应用 |
| 6 | Aide | Aide 依托VS Code运行，能执行跨文件编辑、代码搜索、调试与重构等IDE级操作 |
| 7 | Smol Developer | Smol Developer 是入门级开发智能体，根据产品规格自动初始化代码仓库，适合作为应用创建的辅助伙伴 |
| 8 | bloop. | bloop. 支持自然语言检索代码、自动生成补丁，并结合代码上下文撰写新功能，以提升开发效率 |
| 9 | Automata | Automata 志在打造完全自主编程的AI系统，其研究基础是代码记忆理论，并探索通用人工智能方向 |
| 10 | Continue | Continue 是面向VS Code与JetBrains的开源Copilot类助手，兼容任意大语言模型进行编码 |
| 11 | GPT Migrate | GPT Migrate 是一款智能迁移工具，可便捷地把代码库搬迁到其他框架或编程语言 |
| 12 | GPT Engineer | GPT Engineer 由GPT-4驱动的智能体，能依据提示词一次性生成完整代码库，并可个性化配置AI助手 |
| 13 | CodeFuse | CodeFuse 基于多智能体框架的开发助手，支持接入DevOps工具链及RAG等能力 |
| 14 | Stackwise | Stackwise 是一套开源AI应用集合，允许开发者用代码和提示词快速搭建应用 |
| 15 | Sourcegraph Cody AI | Sourcegraph Cody AI 是一款免费的核心AI编码助手，借助代码图谱理解仓库，提升编写效率 |
| 16 | Cody | Cody 是交互式代码库问答AI，提供文件监控、知识库构建与对话式问答等功能 |
| 17 | Sweep AI | Sweep AI 扮演代码库的初级开发员，可处理缺陷修复、代码改进与Pull Request |
| 18 | GPT Pilot | GPT Pilot 是能编码调试并交互的AI开发伙伴，模拟真实开发流程并与用户协同工作 |
| 19 | English Compiler | English Compiler 是一款概念验证型AI编译器，可把英文Markdown规范转译为可运行代码 |
| 20 | Tusk | Tusk 扮演AI前端工程师，能自动创建PR并推送测试代码 |
| 21 | BitBuilder | BitBuilder 是实习生级别的代码生成工具，可自动撰写测试并处理代码变更 |
| 22 | GitWit | GitWit 是一款在线AI建站工具，适合不同经验层级的开发者快速搭建Web应用 |
| 23 | v0 by Vercel | v0 by Vercel 是一个生成式UI系统，基于Shadcn UI和Tailwind CSS，支持动态组件生成 |
| 24 | Airplane Autopilot | Airplane Autopilot 是AI编码助手，专为加速内部工具的开发而设计 |
| 25 | Factory | Factory 是一名编码机器人，可承担软件从端到端的构建工作 |
| 26 | phind | phind 兼具AI搜索引擎与结对编程功能 |
| 27 | Dosu | Dosu 是GitHub专属AI助手，覆盖文档管理、Issue响应与代码解析 |
| 28 | Hex Magic | Hex Magic 是AI数据工具，可理解代码、解析数据库结构并给出智能建议 |
| 29 | grit | grit 借助机器学习与静态分析，自动执行代码迁移、依赖升级并生成PR |
| 30 | codium | codium 是多功能AI助手，提供测试生成、代码优化与解释等能力 |
| 31 | GitLab Duo | GitLab Duo 是AI驱动的DevSecOps平台，涵盖规划、风险处理与CI/CD管理 |
| 32 | Deepnote | Deepnote 是AI增强的数据处理平台，兼容Python、SQL、R等语言 |
| 33 | MakeDraft | MakeDraft 是一款AI工作流与应用构建平台，能自动产出前端代码 |
| 34 | Copilot X | Copilot X 是GitHub的AI开发助手，提供代码补全、PR处理等功能 |
| 35 | Input | Input 是AI协作编码助手，支持邀请团队成员共同参与开发 |
| 36 | Devin | Devin 由Cognition公司打造，能自动化解决程序员的各类问题与需求 |
| 37 | SECOND | SECOND 是自动化迁移升级工具，可把Angular迁移至React、Redux迁移至React Context，并支持JS到TS等语言迁移，同时可完成Next.js、MUI、Python等大规模代码库的版本升级 |
| 38 | mutable.ai | mutable.ai 用于加速软件开发，提供智能补全、一键生成高质量代码及自动化测试生成，提升效率 |
| 39 | Butternut AI | Butternut AI 能在20秒内生成可立即上线的完整网站，无需编码，界面友好并内置SEO，支持图片上传、个性化定制、即时预览与30%提速 |
| 40 | Cursor | Cursor 是一款AI驱动的代码编辑器，界面类似VS Code，专为AI协作编码设计以加速开发 |
| 41 | Codegen | Codegen 基于GPT-4驱动，提供自动问题解决、测试编写与代码优化，适用于编码调试和迁移场景 |
| 42 | Duckie AI | Duckie AI 是与工程师协作的智能体，覆盖设计到实现的端到端开发，支持交互式目标定义、工程设计与代码生成 |
| 43 | DevGPT | DevGPT 模拟AI虚拟开发团队，按需创建定制微服务，团队包含虚拟产品经理、开发者和DevOps，贯穿概念到部署全流程 |
| 44 | Windsurf | Windsurf 是AI编程助手，提供智能补全、多模型集成与深度上下文感知 |
| 45 | Claude Code | Claude Code 由Anthropic开发的本地终端编程智能体，无需IDE即可读写文件、执行命令、生成代码，支持20万Token上下文处理大型代码库，具备智能理解与跨文件重构能力，插件可接入DeepSeek、K2、GLM-4.5等模型 |
| 46 | Gemini CLI | Gemini CLI 是谷歌开源的终端命令行编程智能体，可自主编程，插件支持接入DeepSeek、K2、GLM-4.5模型 |

##### 2.1.2 生产力工具

部分生产力智能体平台如图2-2所示。

[PRE1]

图2-2 部分生产力智能体平台

部分生产力智能体平台的简介如表2-2所示。

表2-2 部分生产力智能体平台的简介

| 序号 | 名称 | 介绍 |
| :--- | :--- | :--- |
| 1 | Local GPT | Local GPT 是可在本地运行的GPT智能体，基于LangChain搭建，保障数据隐私，能与本地文档交互，兼容GPU与CPU |
| 2 | Alllice | Alllice 采用智能体调用树执行任务，具备任务规划、动态代理管理与自动构建外部交互模块的能力，实现自我扩展 |
| 3 | Prometh AI | Prometh AI 是个性化智能体，支持问题拆解、流程自动化与语音输入，基于LangChain、向量数据库、Redis构建，使用Flutter/Dart开发，可对接Zapier |
| 4 | Agent4Rec | Agent4Rec 由千个LLM驱动的智能体构成，模拟个性化推荐系统，基于MovieLens-1M初始化，涵盖评分、观看、评价等行为 |
| 5 | Moone | Moone 扮演终身私人助理，协助管理日历、任务与信息，优化个人时间安排 |
| 6 | MultiOn | MultiOn 是控制本地浏览器执行的AI代理，可自主完成订票、发邮件、发推文、管理社交媒体等任务，并支持递归调用 |
| 7 | Lindy | Lindy 是办公助理AI，可自动分类邮件、优先处理重要信息，并支持合同管理、会议纪要、每日简报 |
| 8 | Spell | Spell 是带插件能力的AutoGPT智能体，支持任务自动化，集成Zapier与Wolfram API，可执行网页搜索、文件操作等 |
| 9 | Claros AI Shopper | Claros AI Shopper 是AI购物助手，通过分析用户偏好推荐个性化商品，适用于电商与个人购物 |
| 10 | AGENTSCALE AI | AGENTSCALE AI 专注浏览器AI代理研发，旨在减少重复性工作，提升数据分析与提取效率，节省最高70%工时 |
| 11 | Cykel | Cykel 是一款Copilot风格智能体，可与任意UI、网站、API交互，自动化日常任务 |
| 12 | Floode | Floode 是智能通信助手，管理邮件、社交媒体、日历等，提供自动分类、任务提取与信息汇总 |
| 13 | Otherside Personal Assistant | Otherside Personal Assistant 能自动操控Chrome浏览器，完成订票、订餐、调研、邮件管理、招聘等任务 |
| 14 | Wispy | Wispy 是网页浏览AI助手，可总结撰写解释网页内容，创建测验并优化浏览体验 |
| 15 | Ollie | Ollie 是AI购物助手，根据场景与需求帮助用户挑选合适的礼物 |
| 16 | Cognosys | Cognosys 是基于AutoGPT或BabyAGI的智能体UI，适用于智能体的构建与管理 |
| 17 | Raycast | Raycast 是一款高速可扩展的任务启动器，支持任务执行、计算与公共链接分享 |

##### 2.1.3 通用任务工具

部分通用任务智能体平台如图2-3所示。

[PRE2]

图2-3 部分通用任务智能体平台

部分通用任务智能体平台的简介如表2-3所示。

表2-3 部分通用任务智能体平台的简介

| 序号 | 名称 | 介绍 |
| :--- | :--- | :--- |
| 1 | BabyAGI | BabyAGI 是任务管理智能体，基于OpenAI NLP技术，利用GPT-4、LangChain与向量数据库，可自动创建并调整任务优先级 |
| 2 | Multiagent Debate | Multiagent Debate 通过多智能体辩论提升模型事实性与推理能力，不同GPT实例互相生成批评以提高答案准确度 |
| 3 | BeeBot | BeeBot 是早期通用任务处理的研究型智能体项目 |
| 4 | evo.ninja | evo.ninja 是可动态切换角色的智能体，每次迭代会挑选最适合当前任务的角色 |
| 5 | MiniAGI | MiniAGI 是最小化通用智能体，结合强提示、短期记忆与自我批评，可执行股票分析、网络安全测试、艺术创作等 |
| 6 | MultiGPT | MultiGPT 是实验性多智能体系统，由多个expertGPT协作，各自具备短长期记忆并可互通 |
| 7 | Auto gpt | AutoGPT 是GPT-4驱动的完全自治智能体，GitHub星标超15.7万，支持搜索、任务执行、Python运行及插件扩展 |
| 8 | XAgent | XAgent 由面壁智能推出，是一款通用自主智能体，具有安全、自主与双回路机制，可完成编程、浏览器操作、API调用等 |
| 9 | web3 GPT | web3 GPT 是区块链智能体，可编写部署智能合约，让Web2与Web3用户无需专用平台即可交互区块链 |
| 10 | Suspicion Agent | Suspicion Agent 研究GPT在不完全信息博弈中的适用性，探索其推理能力 |
| 11 | Lutra | Lutra 是个人AI工作流平台，可按需生成AI任务流并保障数据安全 |
| 12 | Artisan | Artisan 是AI劳动力智能体，提供销售、邮件、簿记等自动化服务，打造类人数字员工 |
| 13 | GODMODE | GODMODE 受AutoGPT与BabyAGI启发，是UI友好的任务智能体，可执行市场分析、租赁谈判等 |
| 14 | ADEPT | ADEPT 是机器学习研究实验室，致力构建通用智能，实现AI与人类协作并自动操作软件 |
| 15 | Q, ChatGPT for Slack | Q, ChatGPT for Slack 是Slack专属AI助手，支持安全共享与读取URL和文件，适用于总结、评估、头脑风暴等 |
| 16 | Questflow | Questflow 是AI工作者市场，连接中小企业与自主智能体，提供无代码编辑器创建与分发AI劳动力，实现工作流自动化 |
| 17 | manus | manus 被称为全球首款通用AI助手，擅长处理工作与生活任务，让用户安心时仍能妥善完成 |
| 18 | OpenAi Deep Research | OpenAi Deep Research 是OpenAI基于O系列推理模型推出的深度研究型通用多智能体 |
| 19 | Gemini Deep Research | Gemini Deep Research 是谷歌基于Gemini 2.5模型推出的深度研究型通用多智能体 |
| 20 | GenSpark | GenSpark 是多功能AI助手，具备自主思考、规划、行动与工具使用能力，融合顶尖AI搜索、无广告高速浏览器与强力AI代理平台特性 |
| 21 | Lovart | Lovart 是通用Design Agent，可从创意拆解到专业交付完成全流程视觉工作，是设计师的高阶伙伴 |
| 22 | ListenHub | ListenHub 可根据话题、链接或文档一键生成播客 |
| 23 | Medeo | Medeo 无需剪辑，即可把想法一键生成专业视频 |

##### 2.1.4 自主创建工具

部分自主创建智能体平台如图2-4所示。

[PRE3]

图2-4 部分自主创建智能体平台

部分自主创建智能体平台的简介如表2-4所示。

表2-4 部分自主创建智能体平台的简介

| 序号 | 工具名称 | 介绍 |
| :--- | :--- | :--- |
| 1 | Superagent | Superagent 是无需编码即可创建智能体的工具，支持LLM智能体的配置与生产部署，内置内存、向量检索、Webhooks、定时任务等，提供行动型与计划-执行型两种模式 |
| 2 | ChatDev | ChatDev 由面壁智能开发，模拟“斯坦福小镇”架构的AI虚拟软件公司，智能体分工协作如CEO、程序员、测试员等，实现软件开发全流程自动化 |
| 3 | Crewai | Crewai 是基于LangChain的智能体编排框架，支持角色扮演与自主协作，通过模块化设计简化创建与管理，实现复杂任务的高效分工 |
| 4 | BondAI | BondAI 配备CLI与RESTful/WebSocket API的代码解释器，兼容GPT-N、DALL·E、Azure OpenAI，提供强代码执行与数据查询能力，支持Docker部署 |
| 5 | Adala | Adala 是自治数据标注智能体框架，强调可靠、自主学习与可控输出，擅长数据标注处理，适应性强 |
| 6 | AgentPilot | AgentPilot 是桌面端智能体聊天构建与管理工具，集成开源解释器与MemGPT，支持群聊与多智能体协作 |
| 7 | AgentGPT | AgentGPT 是基于浏览器的无代码AutoGPT版本，支持目标设定、思考决策、执行计划与行动反馈，集成OpenAI API与向量数据库 |
| 8 | Agents | Agents 是构建LLM智能体的库，具备长期短期记忆、工具调用、网络导航、多智能体通信、人机交互、符号控制等核心能力 |