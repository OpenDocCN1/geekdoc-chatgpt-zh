[PRE0]

输出以下信息。

[PRE1]

# 第 10 章 链式调用实战指南

## 用链串联 LLM 调用流程

本章先讲解如何把基础大模型接入链，再说明链本身的构成、序列化和反序列化方法，最后覆盖链内记忆的使用技巧。

### 10.1 【实战】把 LLM 接入链中

本小节通过实操演示，把原生大模型接入链后如何搭建问答场景。

**参考代码** 完整示例可在本书配套资料 “/Chapter10/LLMChain.ipynb” 中查看。

LLMChain 是链的入门级实现。它会先读取一个提示模板，用用户的输入填充模板，再把结果投递给 LLM 并返回回复。

接下来我们借助 LLMChain 搭建一条能与大模型交互的通路，提升对话的效率和准确度。

（1）先定义提示模板，代码如下。

[PRE2]

（2）再组装一条简易链，接收用户问题、按模板格式化后送入大模型，代码如下。

[PRE3]

输出以下信息。

> '武汉是湖北省的省会，其特产包括以下几个方面：\n\n1. 茶叶：武汉是中国重要的茶叶产地之一。当地的茶叶品种丰富多样，如闭合春茶、黄鹤楼茶等，这些茶叶以其独特的口感和营养价值而被广泛认可。\n\n2. 黄陂荸荠：是武汉的特色水果，其口味清甜，非常适合夏季食用。\n\n总的来说，武汉的特产包括茶叶、黄陂荸荠等，这些农产品以其独特的口感和营养价值而被广泛认可。\n'

若涉及多个变量，建议一次性以字典形式传入，这样更简洁高效，代码如下。

[PRE4]

输出以下信息。

> '武汉的市花是梅花。\n\n作为武汉的市花，梅花在武汉的城市形象塑造中扮演着重要的角色。梅花不仅具有独特的花朵形态、鲜艳的色彩和浓郁的香味，还承载了很多文人墨客的情怀，被视为中华民族精神的重要象征。\n'

### 10.2 链体系概览

本节梳理链的整体脉络，涵盖基础链与文档链，以及 chain_type 参数、调用方式和安全要点。

#### 10.2.1 链的基本概念

链由可复用的组件串联而成。

- 简单场景下，直接把链接到 LLM 就能满足需求。
- 复杂场景下，单接 LLM 不够，还需叠加多层链式调用才能达成目标。

LangChain 提供了标准链接口，支持多组件组合、链内嵌套链。链可以把用户输入接收、格式化后交给 LLM。借助组合与扩展，能构建更复杂的业务逻辑。

> 提示 组件链化的思路简洁有力，既降低了复杂应用的开发难度，也提升了模块化程度，便于调试、维护与迭代。

LangChain 借助 asyncio 为链提供异步能力。异步方法目前已在 LLMChain 的 arun / apredict / acall、LLMMathChain 的 arun / acall、ChatVectorDBChain 与 QAChain 中可用。

0.1 版本后 LangChain 将链分为 LCEL 链与遗留链。在全部 LCEL 替代方案就绪前，遗留链仍保留兼容。

LCEL 链的详细内容见本书第 13 章。遗留链的概况见表 10-1。

**表 10-1 遗留链的详细信息**

| 链 | 函数调用 | 其他工具 | 说明 |
| :--- | :--- | :--- | :--- |
| APIChain | 不支持 | Requests Wrapper | LLM 把查询转成 API 请求并执行，返回结果再交给 LLM 生成回复，实现自动化问答流程 |
| OpenAPIEndpointChain | 不支持 | OpenAPI Spec | 与 APIChain 思路相近，针对 OpenAPI 端点优化，交互更流畅 |
| ConversationalRetrievalChain | 不支持 | Retriever | 面向文档的对话链，支持历史记录，回填检索查询，检索后与对话一起送 LLM 生成回复 |
| StuffDocumentsChain | 不支持 |  | 把文档列表拼接成统一指令送 LLM，需注意上下文窗口限制 |
| ReduceDocumentsChain | 不支持 |  | 分块处理文档，逐块调用 LLM 后合并，直至能装入最终调用，适合大批量并行处理 |
| MapReduceDocumentsChain | 不支持 |  | 先对每份文档做 LLM 初步处理，再用 ReduceDocumentsChain 合并 |
| RefineDocumentsChain | 不支持 |  | 先生成初始答案，再顺序遍历剩余文档逐步完善，不支持并行 |
| MapRerankDocumentsChain | 不支持 |  | 让 LLM 为每个文档给出答案及置信分，保留置信度最高的，适合单文档决策 |
| ConstitutionalChain | 不支持 |  | 先出答案再依据宪法原则修正，确保输出符合规范 |
| LLMChain | 不支持 |  | 基础查询链，直接对 LLM 发起问答 |
| ElasticsearchDatabaseChain | 不支持 | Elasticsearch Instance | NL 转 Elasticsearch 查询、执行并总结，适用于自然语言检索 ES |
| FlareChain | 不支持 |  | 检索增强技术，用于探索式深度检索 |
| ArangoGraphQAChain | 不支持 | Arango Graph | NL 转 Arango 查询，执行后交 LLM 生成回复 |
| GraphCypherQAChain | 不支持 | Cypher Graph | NL 构建 Cypher 查询执行后交 LLM 回复 |
| FalkorDBGraphQAChain | 不支持 | Falkor Database | NL 转 FalkorDB 图查询执行后交 LLM 回复 |
| HugeGraphQAChain | 不支持 | Huge Graph | NL 转 Huge Graph 查询执行后交 LLM 回复 |
| KuzuQAChain | 不支持 | Kuzu Graph | NL 转 Kuzu Graph 查询执行后交 LLM 回复 |
| NebulaGraphQAChain | 不支持 | Nebula Graph | NL 转 Nebula Graph 查询执行后交 LLM 回复 |
| NeptuneOpenCypherQAChain | 不支持 | Neptune Graph | NL 转 Neptune Graph 查询执行后交 LLM 回复 |
| GraphSparqlChain | 不支持 | SparQL Graph | NL 转 SparQL 查询执行后交 LLM 回复 |
| LLMMath | 不支持 |  | 把问题转为数学表达式，用 numexpr 计算得到精确答案 |
| LLMCheckerChain | 不支持 |  | 用第二次 LLM 调用验证初始答案，提升可靠性 |
| LLMSummarizationChecker | 不支持 |  | 多次 LLM 调用构建摘要，侧重准确性而非速度成本 |
| create_citation_fuzzy_match_chain | 支持 |  | 用 OpenAI 函数调用作答并标注来源，提升可追溯性 |
| create_extraction_chain | 支持 |  | 用 OpenAI 函数调用从文本抽取所需信息 |
| create_extraction_chain_pydantic | 支持 |  | 基于函数调用抽取信息并映射到 Pydantic 模型，集成更紧密 |
| get_openapi_chain | 支持 | OpenAPI Spec | 用 OpenAI 函数调用查询 OpenAPI |
| create_qa_with_structure_chain | 支持 |  | 用函数调用做问答并按指定结构返回 |
| create_qa_with_sources_chain | 支持 |  | 用函数调用回答问题并给出引用 |
| QAGenerationChain | 不支持 |  | 从文档生成问答对，用于评估检索性能 |
| RetrievalQAWithSourcesChain | 不支持 | Retriever | 检索后问答并直接引用来源，适合需标注出处 |
| load_qa_with_sources_chain | 不支持 | Retriever | 对传入文档问答并引用来源，适合直接处理文档 |
| RetrievalQA | 不支持 | Retriever | 先检索相关文档再交 LLM 生成回复 |
| MultiPromptChain | 不支持 |  | 在多个提示间路由输入，适合多指令分发 |

续表