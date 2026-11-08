多步任务里，Agent 到底怎么自己做决定？

在做一个需要多轮操作的任务时，我们通常会先问自己几个关键点：
本地知识库什么时候该上场查？第一步相对好上手。
怎么判断本地检索已经结束，可以进入下一步？
要调用哪家外部搜索引擎，比如 Google？
外部搜索回来的内容是不是我们想要的？
拿到外部信息后，如何验证可信度并决定下一步怎么走？

面对这种复杂且需要多环节协作的场景，LangChain 的 Agent 有什么机制可以让它自己规划、自己判断、自己执行呢？

> > 小雪插嘴：这还用问，肯定是 ReAct 框架啊！
> > 咖哥：没错。LangChain 提供了多种 Agent 实现方式，比如自问自答配搜索、结构化聊天以及 ReAct 框架，其中最具代表性的就是 ReAct。

把 ReAct 框架完整封装起来，LangChain 就能让大模型具备很强的自主能力。应用 ReAct 之后，模型不再只是靠内部知识聊天，而是变成了一个会用工具的智能体。

LangChain 里的 Agent 本质上由大模型和一个精心设计的提示组成。提示里会定义 Agent 的人设、任务背景信息，以及像 ReAct 这样的推理策略。

## 6.3 LangChain 工具与工具包体系

ReAct 会引导模型为任务生成推理链和行动轨迹，让 Agent 能够有序地进行动态推理，不断创建、维护和调整执行计划，同时还能和外部环境交互，比如 Google 搜索、维基百科，把额外信息并入推理。这一交互过程，其实就是我们反复看到的调用工具并执行的过程。本地知识库或搜索引擎并不是模型内部的知识，我们称之为“外部工具”（见图 6.5）。

[PRE0]

图6.5 大模型可以通过外部工具获取额外信息

LangChain 把能力拆成工具和工具包两类。

工具是 Agent 可以直接调用的函数。设计时要注意两点：一是给 Agent 配备合适的工具，二是用清晰的方式描述工具的作用。工具不对，任务就完不成；描述不清，Agent 就不会用。除了官方提供的工具集，用户也可以自行定义。

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

| Google Serper API | 用于网络搜索的工具，需要注册并获取 API 密钥 |## 6.4 用 create_react_agent 搭一个鲜花定价 Agent

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

工具包是一组围绕同一目标关联起来的工具集合。每个工具包包含多个工具。例如 LangChain 的 Office365 工具包就涵盖 Outlook 连接、邮件列表读取、邮件发送等功能。选择也很多。

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

工具和工具包的列表会随着版本更新不断扩充，这也让 LangChain 的能力边界在持续扩大。下面是一段为 Agent 分配工具包中工具的示例代码。

[PRE1]

关于这些工具和工具包的详细用法，参考 LangChain 官方文档即可。我们接下来会用一个 ReAct Agent 调用搜索和数学工具的例子来演示。

本节的目标是利用 LangChain 的 create_react_agent 来构建一个 ReAct Agent。任务很具体：先查出当前玫瑰花的市场价，再在此基础上算出加价 5% 后的售价。

准备工作可以参照 2.5.2 节，比如安装 OpenAI、LangChain，在 SerpApi 注册账号获取 SERPAPI_API_KEY 并安装对应包。本节会为模型提供 Google 搜索能力。

另外还需要安装 numexpr 数学包，这是 llm-math 工具的依赖。

[PRE2]

先配置 OpenAI 和 SerpApi 的 API 密钥。

[PRE3]

然后加载用来驱动 Agent 的大模型。

[PRE4]

接着加载要用的工具。这里给模型配了两个工具：SerpApi，用来通过 Google 搜索引擎查玫瑰花的当前市场价格；llm-math，用来做售价计算。

[PRE5]

随后设计提示模板来落地 ReAct 认知框架。

[PRE6]

咖哥：这个提示模板看起来很熟悉吧？
小雪：对，跟 LangChain Hub 上 hwchase17 提供的 ReAct 模板很像。
咖哥：是的，这次我们自己写的 ReAct 提示基于同一套认知框架，只是可以加入个性化要求，比如强制用中文回答、规定答案格式或限制字数在 100 字以内。

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

接下来调用 create_react_agent 函数创建 ReAct Agent，传入模型、工具和提示词。

[PRE7]

下一步组装 AgentExecutor 来执行。

[PRE8]

现在让 Agent 回答问题：“目前市场上玫瑰花的一般进货价格是多少？如果加价 5%，应该如何定价？”

[PRE9]

Agent 成功遵循了 ReAct 框架，它输出的思考与行动轨迹如图 6.6 所示。

[PRE10]

[PRE11]

小雪：效果不错，就是这次运行输出全是英文，我有点不习惯。
咖哥：那你觉得要怎么改？
小雪：咖哥，只要把 Prompt 改一下就行了。