这首诗想呈现的，是月亮既作为一处自然景致，也在中国情怀里承载牵挂与思念的画面。每个人的月亮观都不一样，你完全可以按自己的体悟去调整词句和风格。先问问自己：月亮在你心里代表什么？是寂寞时陪着你的存在？是夜里最柔和的光？还是连着过往与未来的那座桥？把这些念头当成出发点去写就好。

### 8.3.6 LCEL中的RunnablePassthrough

RunnablePassthrough 在 LCEL 里属于一种特殊的可运行单元，它对传入的数据不做任何加工，原封不动地送往下一步。常在流程中需要透传或补充额外信息时使用。

RunnablePassthrough 的使用方式主要有两种。

- 原样透传：RunnablePassthrough()，直接把输入返回。
- 字段扩展：RunnablePassthrough.assign(key=func)，借助函数为输入增加新字段。

### 【示例8.8】 RunnablePassthrough示例。
[PRE0]

输出：

[PRE1]

### 8.3.7 LCEL中的RunnableBranch

RunnableBranch 能够依据输入的条件动态挑选执行路径。它接受一个判断函数和若干分支组件，通过函数的返回值决定走哪条分支。简单说就是在链路中实现条件路由，效果类似 if-else。

### 【示例8.9】 LCEL中的RunnableBranch。
[PRE2]

输出：

[PRE3]

## 8.4 LangChain框架中的记忆

本部分系统介绍 LangChain 的记忆机制，涵盖概念、接口设计和落地实践。

### 8.4.1 记忆的概念

在 LangChain 里，记忆负责保存和调取对话历史或其他上下文，让模型能够延续之前的交互。对构建有状态的对话机器人而言，这保证了连贯性和上下文感知。框架提供了多种记忆实现：

- ConversationBufferMemory：保存完整对话记录。
- ConversationBufferWindowMemory：只保留最近 N 条消息。
- ConversationSummaryMemory：以摘要形式保存对话。
- EntityMemory：基于抽取的实体进行记忆。
- VectorStoreRetrieverMemory：借助向量库存储并检索记忆。

记忆组件通常配合 ChatOpenAI 等聊天模型使用，通过 memory_key 参数把历史注入模型调用。

### 8.4.2 BaseChatMessageHistory接口及其子类

BaseChatMessageHistory 是 LangChain 管理消息历史的抽象基类，统一了存储与读取的接口。主要能力包括：

- add_user_message(message:str)：写入用户消息。
- add_ai_message(message:str)：写入 AI 回复。
- clear()：清空历史。
- messages：读取全部消息的属性。

常见子类如下。

#### 1）内存存储实现

- ChatMessageHistory：在内存中临时保存消息。

#### 2）持久化存储实现

- FileChatMessageHistory：以文件如 JSON、CSV 形式存储。
- RedisChatMessageHistory：基于 Redis 存储。
- MongoDBChatMessageHistory：基于 MongoDB 存储。
- SQLChatMessageHistory：基于 SQL 数据库如 SQLite、PostgreSQL 存储。
- CassandraChatMessageHistory：基于 Cassandra 存储。

这些实现让历史可以跨会话保留，也能在分布式环境下共享。

### 8.4.3 RunnableWithMessageHistory

RunnableWithMessageHistory 是 LangChain 用于组装具备记忆能力的运行组件的工具。它把消息历史与链或模型绑定，自动完成上下文的注入与更新。主要作用：

- 从用户输入和模型输出中自动抽取消息。
- 把历史格式化为模型可接受的输入。
- 支持多种记忆类型和多种历史存储方式。

#### 【示例8.10】RunnableWithMessageHistory（LangChain+Qwen）。
[PRE4]

输出：

[PRE5]

### 8.4.4 基于LangChain的聊天机器人

搭建聊天机器人时，记忆是核心环节。下面示例演示如何用 RunnableWithMessageHistory 配合 ConversationBufferMemory 构建一个简易对话机器人。

#### 【示例8.11】基于LangChain的聊天机器人（适配LangChain最新版+Qwen）。
[PRE6]

输出：

[PRE7]

该实现完整体现了 LangChain 的对话记忆能力，可在多轮交互中保持上下文的一致性。

## 8.5 LangChain框架中的检索增强生成

检索增强生成 Retrieval-Augmented Generation，RAG，是把外部知识检索与大语言模型生成结合起来的一种技术。模型在回答时能够实时引用外部数据源，缓解静态知识局限和幻觉问题。下面展开 RAG 的核心流程与要点。

### 8.5.1 检索增强生成介绍

RAG 的本质是让模型在生成时参考外部知识。

(1) RAG 主要解决 LLM 的两大短板：

① 知识时效不足：模型训练截止，导致无法回答最新信息，比如当年的新政策。
② 幻觉倾向：模型可能生成看似合理却错误的结论，比如虚构论文并附引用。

(2) 工作方式是：用户提问后，先从外部知识库如文档、网页、数据库中检索相关信息，再把检索结果作为上下文喂给 LLM，由模型基于事实生成答案。

(3) 与微调的区别：微调是改写模型参数让其“记住”知识，RAG 则是让模型“查阅”外部知识，更灵活、无需重训，适合知识频繁更新的场景。

(4) 典型应用：企业内部文档问答、客服系统的产品手册检索、法律医疗咨询中的案例库参考等。

#### 【示例8.12】LangChain框架RAG流程（LangChain+Qwen）。