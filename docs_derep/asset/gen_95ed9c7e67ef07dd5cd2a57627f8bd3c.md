[PRE0]

想直接和 CSV 文件对话，CSV Agent 就是一个很顺手的入口。它初始化之后会在幕后把已加载的表格读完并建立理解。开发者只需要调用 `agent.run()`，丢进一句自然语言比如“这个表共有几行”，代理就会去检索文件并把行数直接吐出来。整个过程不用写繁琐的查询逻辑，交互非常直观。

[PRE1]

也可以换成更具体的提问，例如统计兄弟姐妹超过 3 人的记录有多少条：

[PRE2]

CSV Agent 的能力不止于单表。 它能同时承接多个 CSV，并在这几个表之间做交互式对比。比如把 `titanic.csv` 和 `titanic_age_fillna.csv` 一起交过去，就能问两个 DataFrame 在年龄列上哪里不一样。这种多源联动的特性让工具更灵活，也更实用，适合需要横向比对不同数据源的场景。

[PRE3]

#### 10.6.2 Pandas Dataframe Agent 的集成

LangChain 除了对 CSV 文件提供支持，还把交互能力延伸到了 pandas 数据帧，特别适合问答类任务。

Pandas Dataframe Agent 的主要作用就是和 pandas 数据帧打交道，方便查询和取数。需要注意的是，它在内部会调用 Python 代理并执行由大模型生成的 Python 代码，如果生成的代码存在风险，执行时可能带来意外后果，所以使用时要谨慎。

要搭建一个可复用、可扩展的 LLM 应用，通常先用 `create_pandas_dataframe_agent` 生成一个能操作数据帧的代理，`ChatOpenAI` 与 `OpenAI` 则负责接入大模型，让代理能读懂自然语言并给出自然语言回答，`AgentType` 用来声明代理的种类。随后用 pandas 库把 `titanic.csv` 读成数据帧 `df`，之后就能借助 LangChain 的工具和代理与它互动。

[PRE4]

常见做法是选用 `ZERO_SHOT_REACT_DESCRIPTION` 作为代理类型，下面是一个初始化的示例：

[PRE5]

另一种可选方案是用 `OPENAI_FUNCTIONS` 类型初始化。这种方式的构建逻辑不同，它依赖于与 ChatOpenAI 相关的特定 OpenAI 功能来驱动代理。

使用 `OPENAI_FUNCTIONS` 的初始化示例：

[PRE6]

和 CSV Agent 一样，初始化完成后 Pandas Dataframe Agent 就可以直接跑查询，取出数据帧里的信息。它同样支持多表场景，比如把多个数据帧传进去，询问两个表在年龄列上差异的行数。

[PRE7]

执行结果：

> Entering new AgentExecutor chain...

Thought: I need to compare the age columns in both dataframes

Action: python_repl_ast

Action Input: len(df1[df1['Age'] != df2['Age']])

Observation: 177

Thought: I now know the final answer

Final Answer: 177 rows in the age column are different.

> Finished chain.

'177 rows in the age column are different.'

##### 10.6.3 PowerBI Dataset Agent 的集成

Power BI 本身擅长可视化和报表，但在需要编程方式查询分析数据集时，LangChain 的 PowerBI Dataset Agent 能让提问更自然，不必再写 DAX 表达式。

PowerBI Dataset Agent 可以直接与 Power BI 数据集交互，例如描述数据表、统计记录数或做多维度分析。

初始化所用到的类有：

- `create_pbi_agent` 和 `PowerBIToolkit` 来自 `langchain.agents.agent_toolkits`。
- `PowerBIDataset` 来自 `langchain.utilities.powerbi`。
- `ChatOpenAI` 来自 `langchain.chat_models`。
- `AgentExecutor` 来自 `langchain.agents`。

为支持 Azure 身份验证，需要执行 `pip install azure-identity` 安装 `azure.identity` 包。代理访问 Power BI 数据集时依赖 Azure 认证，这里使用 `DefaultAzureCredential()` 来获取合适的凭据。

[PRE8]

在查询层面，支持的操作比较丰富。

先通过 `agent_executor.run("Describe table1")` 让代理给出数据表的描述信息；再比如统计记录数，可以调用 `agent_executor.run("How many records are in table1?")`。

[PRE9]

代理可以完成记录数的计算：

[PRE10]

为了让模型更好地理解 Power BI 相关问题，还可以加入少量自定义的 few-shot 提示模板，帮助模型生成更准确的 DAX 查询。交互时务必确保拥有正确的权限和凭证。

目前 LangChain 的 PowerBI Dataset Agent 仍在持续完善中，生产环境使用前建议充分测试。

[PRE11]

## 第 10 章

集成

借助 LangChain 的 PowerBI Dataset Agent 与 Power BI 数据集交互，为开发者提供了一种更自然的数据查询和分析方式。无需深入掌握 DAX 语法，就能更灵活、更直观地与数据对话。

### 10.7 Retrievers 集成指南

Retriever 集成聚焦于为开发者提供高效便捷的信息检索手段。首先是 Arxiv API Wrapper，专为检索学术文献而设，能从 arXiv 数据库获取研究资料；其次是 Azure Cognitive Search Wrapper，实现与 Azure 平台的深度联动，高效精准地检索各类信息；最后是 Wikipedia API Wrapper，简化了从维基百科提取内容的流程，无需了解底层技术细节即可获取公开信息。

#### 10.7.1 WikipediaRetriever 集成

Wikipedia 作为庞大的在线知识库，LangChain 的 WikipediaRetriever 能从中获取相关文档并进行查询。首先安装 Python 包 `wikipedia`。

[PRE12]

###### LangChain 入门指南

下面是 WikipediaRetriever 的常用参数。默认 `lang` 为 `"en"`，可在指定语言的维基百科中搜索。`load_max_docs` 默认为 `"100"`，用于限制下载文档数量，实验阶段建议取更小值，目前上限为 `"300"`。`load_all_available_meta` 默认为 `"False"`，仅下载核心字段如发布日期、标题和摘要；设为 `"True"` 时会额外下载其他字段。

调用非常简单，使用 `get_relevant_documents()` 并传入查询文本即可。例如查询 “HUNTER X HUNTER” 相关文档：

[PRE13]

查看文档的元数据或内容：

[PRE14]

若要做问答，需要先准备 OpenAI API Key，随后可把 ConversationalRetrievalChain 与 ChatOpenAI 和 WikipediaRetriever 结合，实现交互式问答。例如：

[PRE15]

对每个问题都能通过代码获取并打印答案。注意两点：使用 API Key 时不要将其暴露在公开代码中；与 Wikipedia 交互可能受频率限制，请合理控制请求频率。

LangChain 的 WikipediaRetriever 让开发者能轻松与维基百科互动，获取相关文档并用于问答，大幅降低了从维基百科获取知识并应用到自有系统的门槛。

#### 10.7.2 ArxivRetriever 集成

arXiv 是一个免费的分发与开放获取的学术档案馆，收录了物理、数学、计算机科学、定量生物学、定量金融、统计学、电气工程与系统科学以及经济学等领域的论文。ArxivRetriever 是 LangChain 与 arXiv 的集成组件，可用于检索相关学术文档。

首先安装 Python 包 `arxiv`。

[PRE16]