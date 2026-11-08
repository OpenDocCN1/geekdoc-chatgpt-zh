多轮任务里，Agent 如何自行判断走向？

在一次需要多环节协作的流程中，容易冒出这些疑问：
本地库什么时候该启动查阅？首段相对简单。
如何确认本地检索已收尾，可以推进下一步？
要接入哪家外部检索服务，比如谷歌？
外部检索返回的结果是否正是想要的？
拿到外部数据后怎样评估可信度再决定后续动作？

面对这种环环相扣的情境，LangChain 的 Agent 靠什么机制完成自规划、自判断、自执行？

> > 小雪插嘴：当然是 ReAct 框架！
> > 咖哥：对。LangChain 里有多种 Agent 形态，比如自问自答配合搜索、结构化对话以及 ReAct 框架，其中最具代表性的就是 ReAct。

把 ReAct 完整封装后，LangChain 能赋予大模型很强的自主性。引入 ReAct 以后，模型不再只是凭借内部知识闲聊，而是升级为会调用外部能力的智能体。

LangChain 的 Agent 通常由一个大模型加上一份精心设计的提示构成。提示里会设定 Agent 的角色人设、任务背景上下文，以及像 ReAct 这样的推理策略。

## 6.3 LangChain 中的工具和工具包

ReAct 会引导模型为任务生成推理轨迹和行动序列，使 Agent 能系统化地进行动态推理，创建、维护并调整执行计划，同时支持与外部环境比如谷歌搜索、维基百科交互，把额外信息并入推理。这个与外部环境交互的过程，本质上就是我们多次见到的调用并执行工具的动作。本地知识库或搜索引擎并不是模型内部自带的知识，我们称之为“外部工具”（见图 6.5）。

[PRE0]

图6.5 大模型可以通过外部工具获取额外信息

LangChain 将能力拆成工具和工具包两类。

工具是 Agent 可调用的函数。设计时需要关注两点：一是为 Agent 配齐合适的工具；二是把工具作用描述得清晰易懂。工具不匹配，任务难以完成；描述不清，Agent 就无从下手。除了官方自带的工具集，用户也可以自行定义。

目前 LangChain 支持的工具及其说明如表 6.2 所示。

表6.2 LangChain支持的工具及其说明

| 工具 | 说明 |

|---|---|

| Apify | 用于网络抓取和自动化的工具 |

| ArXiv API Tool | 用于访问学术文献的工具 |

| AWS Lambda API | 提供云函数或无服务器计算的工具 |

| Shell Tool | 允许机器学习模型直接与系统 shell 交互的工具 |

| Bing Search | 提供 Bing 搜索引擎查询结果的工具 |

| Brave Search | 提供 Brave 搜索引擎查询结果的工具 |

| ChatGPT Plugins | 插件工具，用于扩展 ChatGPT 的功能 |

| DataForSEO API Wrapper | 从各种搜索引擎获取搜索结果的工具 |

| DuckDuckGo Search | 提供 DuckDuckGo 搜索引擎查询结果的工具 |

| File System Tools | 提供与本地文件系统交互的工具集 |

| Golden Query | 提供基于知识图谱的自然语言查询服务的工具 |

| Google Places | 提供 Google 地点信息和查询结果的工具 |

| Google Search | 提供 Google 搜索引擎查询结果的工具 |

| Google Serper API | 用于网络搜索的工具，需要注册并获取 API 密钥 |## 6.4 通过create_react_agent创建鲜花定价Agent

| 工具 | 说明 |

|---|---|

| Gradio Tools | 提供与 Gradio 应用交互功能的工具 |

| GraphQL tool | 提供 GraphQL 查询结果的工具 |

| huggingface_tools | 提供与 Hugging Face 库交互功能的工具 |

| Human as a tool | 描述如何将人类作为一个执行具体任务的工具 |

| IFTTT WebHooks | 提供与 IFTTT WebHooks 交互功能的工具 |

| Lemon AI NLP Workflow Automation | 用于自动化 NLP 工作流的工具 |

| Metaphor Search | 专为机器学习模型设计的搜索引擎工具 |

| OpenWeatherMap API | 提供查询天气信息结果的工具 |

| PubMed Tool | 提供 PubMed 医学文献查询结果的工具 |

| Requests | 提供网络信息获取功能的工具 |

| SceneXplain | 提供图像描述服务的工具 |

| Search Tools | 提供各种搜索工具的工具集 |

| SearxNG Search API | 提供自托管 SearxNG 搜索 API 查询服务的工具 |

| SerpApi | 提供网络搜索功能的工具 |

| Twilio | 提供短信息或其他发送消息功能的工具 |

| Wikipedia | 提供查询维基百科结果的工具 |

| Wolfram Alpha | 提供查询 Wolfram Alpha 的知识引擎功能的工具 |

| YouTubeSearchTool | 提供 YouTube 视频搜索功能的工具 |

| Zapier Natural Language Actions API | 用于自然语言处理的 API 工具 |

工具包是一组为达成特定目标而关联的工具集合。每个工具包包含多个工具。例如 LangChain 的 Office365 工具包就涵盖 Outlook 连接、邮件列表读取、发送邮件等一系列能力。当然 LangChain 中还有很多其他工具包可供选择。

目前 LangChain 支持的工具包及其说明如表 6.3 所示。

| 工具包 | 说明 |

|---|---|

| Amadeus Toolkit | 将 LangChain 连接到 Amadeus 旅行信息 API |

| Azure Cognitive Services Toolkit | 与 Azure Cognitive Services API 交互，以实现一些多模态功能 |

| GitHub Toolkit | GitHub 工具包，包含使大模型 Agent 与 GitHub 存储库互动的工具，这些工具是 PyGitHub 库的封装 |

| Gmail Toolkit | Gmail 工具包 |

| Jira | Jira 工具包 |

| MultiOn Toolkit | 将 LangChain 连接到浏览器中的 MultiOn 客户端 |

| Office365 Toolkit | 将 LangChain 连接到 Office365 电子邮件和日历等 |

| PlayWright Browser Toolkit | 通过浏览器导航 Web 并与动态渲染的网站互动 |

表 6.2 与表 6.3 所列的能力会随着版本演进不断增多，这也让 LangChain 的边界持续扩大。下面是一段为 LangChain Agent 分配工具包中工具的示例代码。

[PRE1]

关于上述工具与工具包的详细用法，请参考 LangChain 官方文档。我们接下来会给出一个 ReAct Agent 调用搜索以及数学工具的示例。

本节将借助 LangChain 的 create_react_agent 功能构建一个 ReAct Agent。任务设定为：先查出当前玫瑰花的市场价格，再在此基础上计算加价 5% 后的新价。

准备工作可参照 2.5.2 节，例如安装 OpenAI、LangChain，在 SerpApi 注册账号获取 SERPAPI_API_KEY 并安装对应包。本节会为模型提供 Google 搜索能力。

另外还需要安装 numexpr 数学包，这是 llm-math 工具的依赖。

[PRE2]

设置 OpenAI 网站和 SerpApi 网站提供的 API 密钥。

[PRE3]

随后加载将用于驱动 Agent 的大模型。

[PRE4]

接下来加载待使用的工具。此处为模型配备两个工具——SerpApi，即调用 Google 搜索引擎的工具，用于查找当前玫瑰花的市场价格；llm-math，即通过大模型进行数学计算的工具，用于计算售价。

[PRE5]

随后设计提示模板以落地 ReAct 认知框架。

[PRE6]

咖哥：这份提示模板是不是很眼熟？
小雪：对，曾用过 LangChain Hub 上 hwchase17 提供的 ReAct 提示模板，和这个很像。
咖哥：没错。这次我们自行制作的 ReAct 提示仍基于同一认知框架，只是可以加入个性化要求，例如要求 Agent 用中文作答、限定最终答案格式或把字数控制在 100 字以内等。

ReAct 提示模板包含的部分及其说明如表 6.4 所示。

表 6.4 ReAct 提示模板包含的部分及其说明

| 部分 | 说明 |

| :--- | :--- |

| 介绍 | 尽你所能回答以下问题。如果能力不够，你可以使用以下工具： |

| 工具列表 | {tools} |

| 格式指南 | 使用以下格式： |

| 问题 | 你需要回答的输入问题 |

| 思考 | 你应该始终考虑接下来的操作 |

| 行动 | 需要采取的行动，应该是 [{tool_names}] 中的一个 |

| 行动输入 | 对行动的输入 |

| 观察 | 行动的结果 |

| 循环 | ...（这个思考/行动/行动输入/观察可以重复 N 次） |

| 最终思考 | 我现在知道了最终答案 |

| 最终答案 | 对原始输入问题的最终回答 |

| 开始指令 | 开始！ |

| 实际用例 | 问题：{input}\n 思考：{agent_scratchpad} |

接下来使用 create_react_agent 函数创建 ReAct Agent，并在初始化时指定大模型、工具与提示词。

[PRE7]

下一步是创建 AgentExecutor。

[PRE8]

现在让 Agent 回答刚才的问题“目前市场上玫瑰花的一般进货价格是多少？如果在此基础上加价 5%，应该如何定价？”。

[PRE9]

Agent 严格遵循了 ReAct 框架，它输出的思考与行动轨迹如图 6.6 所示。

[PRE10]

[PRE11]

小雪：挺好的，不过这次运行中 Agent 给出的内容都是英文，我有点不适应。
咖哥：那你觉得该怎么改？
小雪：咖哥，只要修改一下 Prompt 就行了。