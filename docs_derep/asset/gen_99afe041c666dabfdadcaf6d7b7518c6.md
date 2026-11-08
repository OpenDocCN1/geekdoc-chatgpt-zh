store.save_local(store_path)

        return store

    async def get_retriever(self, keywords: List[str], page: int):

        """

        Retrieves documents and returns a retriever based on the documents.

        Args:

            keywords (List[str]): Keywords to search documents.

            page (int): Page number for pagination of results.

        Returns:

            Retriever instance or FAISS vector store.

        """

        print(f"开始实时查询 bilibiliAPI 获取数据")

        docs = await self.get_docs(keywords, page)

        print(f"接收到的 bilibili 数据为：{docs}")

        print("------------------------")

[PRE0]

整体来看，该模块承担了三大核心职责。

##### 1. 数据获取（get_docs）
借助封装的 get_bilibili.bilibili_detail_pipeline 方法，以异步方式按关键词抓取 B 站视频信息，并把结果规范化为 LangChain 的 Document 对象。每个对象都包含正文内容与元信息，为后续检索和增强问答提供可用素材。

##### 2. 向量化存储（create_vector_store）
抓取到的文本先经 RecursiveCharacterTextSplitter 智能切分，单段上限 1000 字且保留 300 字重叠，随后调用 OpenAI Embedding 生成向量并写入本地 FAISS 向量库。若传入 store_path，则可完成本地持久化。

##### 3. 文档检索（get_retriever）
基于已构建的向量库实例化 Retriever，实现关键词的实时语义相似检索，返回与查询最贴近的文档集合，可直接接入 RAG 生成流程。

#### 8.1.7 搭建首个 LangChain 生成链
LangChain 的设计思想是链式组合，把可复用的单元通过流水线串联起来。以最简链式结构为例，就能组装出一套面向 B 站问答的生成链：接收用户问题和上下文，用提示模板组织输入，交由大模型生成，再对输出做结构化解析。

项目中 BiliAgent-main/bili_server/generate_chain.py 就是第一条完整生成链，内部由三部分构成。

##### 1. PromptTemplate
用于定义标准化的提示，明确模型角色、限制条件和使用规范。用户提问与视频描述等上下文会被填入 <context> 与 <question> 标签，形成清晰输入结构，相关代码如下。

[PRE1]

##### 2. LLM模型
采用 ChatOpenAI 接入 OpenAI 对话模型。后续可自由替换模型实例，只需在链中更换模型对象，提示与上下文逻辑无需改动。示例代码如下。

[PRE2]

##### 3. StrOutputParser
大模型原生输出常带结构或元数据，如 JSON 或完整响应。引入 StrOutputParser 可把结果提纯为干净文本字符串，便于展示或后续调用。该解析器已由官方封装，直接导入即可使用。

[PRE3]

三者的串联实现方式如下。

[PRE4]

调用时只需准备包含 context 与 input 的字典，通过 invoke 即可触发，操作非常简洁。

[PRE5]

#### 8.1.8 四大高阶链路优化手段
基础链搭建并接入数据后，下一步聚焦 BiliAgent 的核心优化能力，如图8.11 所示。

[PRE6]

图8.11 BiliAgent架构中的核心优化模块

项目 BiliAgent-main/bili_server/grader.py 将提升系统智能度的优化能力统一收敛到 GraderUtils 类中。四个高阶模块均遵循 Prompt+LLM+OutputParser 的标准执行范式。

##### 1. 评估文档与用户问题的相关性
该链用于在检索阶段提前筛除明显不匹配的文档，避免无关上下文污染生成结果。核心代码如下。

[PRE7]

提示词要求模型对文档与问题的匹配度进行判断，并以标准的 JSON 形式返回 yes 或 no。调用方式如下。

[PRE8]

典型输出：

retrieval_grader_results: {'score': 'no'}

##### 2. 通过幻觉评分器评估答案的有效性
此链用于检验模型回答是否建立在事实之上，防止张冠李戴或编造内容。它将回答与支撑文档对比，输出可信性判断。核心代码如下。

[PRE9]

使用示例：

[PRE10]

典型输出：

hallucination_grader_results:{'score': 'yes'}

##### 3. 构建用于数据分析的代码解释器
当问题涉及数据分析或代码生成时，该链可对模型输出的 Python 代码进行正确性与相关性校验，并给出简要反馈。核心代码如下。

[PRE11]

##### 4. 使用问题重写机制提升检索效果
该优化器在检索前对用户原问题进行语言重构，使表达更契合向量库的检索习惯，对口语化或结构松散的提问尤其有效。核心代码如下。

[PRE12]

四个优化器被封装为统一接口，可独立运行、灵活组合。传入模型实例后，通过 .invoke() 即可对任意输入做评估或改写。

#### 8.1.9 从 LangChain 平滑过渡到 LangGraph
LangChain 自 2023 年起就形成活跃社区来规范大模型应用标准。当前已发布多个大版本，其体系中 RAG 与 AI Agent 是两大支柱。LangChain 中 Agent 的底层结构如图8.12所示。

[PRE13]

在 LangChain 框架内，AI Agent 通常拆分为 Agent、Message 与 Toolkits 三部分。Agent 本身由大模型+提示+输出解析器构成的 Chain 组成，用于处理输入。输入来源包括用户的原始提问 Input、子任务的模型响应 Model Response，以及携带背景的 Context。Agent 的输出会连接到工具库，具体调用哪项工具由 Agent 对子任务的拆分决定。大模型调用外部函数分为识别工具和执行工具两步，Messages→AI Agent→Toolkits 流程负责拆解任务、定位工具并提取名称与参数，属于静态决策阶段；而将决策落地则由 AgentExecutor 完成。

综上，Agent 负责决策但不直接执行，AgentExecutor 负责运行。两者结合才能形成完整 Agent。其中 AgentExecutor 调用 Agent 并执行其指定的工具，实现闭环。

LangChain 仍是主流的大模型应用框架。v0.3 版本已基本完成从传统类到 LCEL 的迁移，让分步序列的定义与执行更简洁高效。实质上 LangChain 构建的是有向无环图 DAG。随着 Agent 需求增长，大模型使用从工具调用转向复杂推理，LangGraph 应运而生，以支持循环与条件交互，弥补 LCEL 线性序列在高级系统中的局限。

在 LangChain 中通过 LCEL 构建 Chain 的最简代码示例如下。