让大模型回答更完整：从提问设计到工具调用

下面用 2022 年冬奥会冰壶项目做例子，说明检索到位但提问没设计好也会漏答。下面是模型在训练或检索时可能看到的维基片段摘要。

男子冰壶四强赛段信息中显示了铜牌战。比赛在 2022 年 2 月 18 日 14:05 进行，美国对加拿大。美国队个人命中率：John Landsteiner 80%，Matt Hamilton 86%，Chris Plys 74%，John Shuster 69%，全队 77%。加拿大队：Geoff Walker 84%，Brett Gallant 86%，Mark Nichols 78%，Brad Gushue 78%，全队 82%。

女子冰壶铜牌战在 2022 年 2 月 19 日 20:05。瑞士对阵瑞典。瑞士：Melanie Barbezat 79%，Esther Neuenschwander 75%，Silvana Tirinzoni 81%，Alina Pätz 64%，全队 75%。瑞典：Sofia Mabergs 89%，Agnes Knochenhauer 80%，Sara McManus 81%，Anna Hasselborg 76%，全队 82%。

混合双人项目参赛组合如下。澳大利亚女将 Tahli Gill 配男将 Dean Hewitt；加拿大女将 Rachel Homan 配男将 John Morris；中国女将 Fan Suyuan 配男将 Ling Zhi；捷克女将 Zuzana Paulová 配男将 Tomáš Paul；英国女将 Jennifer Dodds 配男将 Bruce Mouat；意大利女将 Stefania Constantini 配男将 Amos Mosaner；挪威女将 Kristin Skaslien 配男将 Magnus Nedregotten；瑞典女将 Almida de Val 配男将 Oskar Eriksson；瑞士女将 Jenny Perret 配男将 Martin Rios；美国女将 Vicky Persinger 配男将 Chris Plys。

混合双人金牌战安排在 2022 年 2 月 8 日 20:05，意大利对挪威。意大利 Stefania Constantini 83%，Amos Mosaner 90%，合计 87%。挪威 Kristin Skaslien 70%，Magnus Nedregotten 69%，合计 69%。

问题：2022 冬奥会冰壶金牌获得者是谁？

文中给出的提示性结论写道：冬奥冰壶共有两支金牌队伍，男子是瑞典队 Niklas Edin、Oskar Eriksson、Rasmus Wranå、Christoffer Sundgren、Daniel Magnusson；女子是英国队 Eve Muirhead、Vicky Wright、Jennifer Dodds、Hailey Duff、Mili Smith。

在这个案例里，第一篇提供给模型的文章其实覆盖了冰壶三项的奖牌信息，但后续的输出却更集中在男子和女子两个赛场，混双被忽略。问题不在检索 Search 步骤没找到资料，而是在提问 Ask 步骤的推理没把三项都问全。改进提问是最直接的办法，提升模型能力也有效，比如换成更强的 GPT-4，示例代码如下：

[PRE0]

官网提供的以上代码的运行结果为：

> The gold medal winners in curling at the 2022 Winter Olympics are as follows: \n\nMen's tournament: Team Sweden, consisting of Niklas Edin, Oskar Eriksson, Rasmus Wranå, Christoffer Sundgren, and Daniel Magnusson. \n\nWomen's tournament: Team Great Britain, consisting of Eve Muirhead, Vicky Wright, Jennifer Dodds, Hailey Duff, and Mili Smith. \n\nMixed doubles tournament: Team Italy, consisting of Stefania Constantini and Amos Mosaner.

GPT-4 很成功，完整识别出了全部 12 位冰壶金牌得主。

# 第 13 章 OpenAI API 进阶应用实践

本节介绍如何借助 LangChain 让 OpenAI 模型调用外部工具来增强能力，通过 LangChain 把自定义工具接入大语言模型，形成一个能够使用工具来回答用户问题的智能代理。

LangChain 是面向语言模型应用的开发框架，它提供构建具备上下文感知、能与环境动态交互的多层 LLM 应用的方法。作为代理，它简化了开发工作，也让用户体验更灵活。给 LLM 用工具的核心原因是模型存在知识时效性和幻觉问题，工具调用可以让模型在可控上下文里查阅外部知识库或调用内部 API，而不是硬靠记忆。这样模型能动态获取信息并进行解析。

当模型具备工具调用能力时，它可以直接从搜索引擎、API 或数据库获取上下文。与直接作答不同，有工具的模型可以先执行中间步骤收集信息。工具还能组合使用，比如先用搜索工具找定量信息，再用计算器做运算。

用 LangChain 搭建工具链大致分为：

- 导入依赖并连接 Pinecone 向量数据库。
- 构建 LLM 代理，采用改进的 ReAct 框架做思维链推理，提升推理质量。
- 加入历史记录，让代理记住对话上下文。
- 搭建可被工具访问的知识库，增强检索能力。
- 为代理提供多工具接入，验证是否会在回答时主动调用工具。

下面先导入库并建立到 Pinecone 向量数据库的连接，Pinecone 只是其中一种向量存储，LangChain 原生支持 AnalyticDB、Annoy、AtlasDB、Chroma、Deep Lake、ElasticSearch、FAISS、LanceDB、Milvus、MyScale、OpenSearch、PGVector、Pinecone、Qdrant、Redis、SupabaseVector-Store、Tair、Weaviate、Zilliz 等，其他存储可自行定制连接器。

导入库的示例代码如下：

[PRE1]

要获取 Pinecone 的 API Key，可先注册免费账号，再把密钥写入下面的 api_key 变量，或设为环境变量 PINECONE_API_KEY，示例代码为：

[PRE2]

以上代码的运行结果为：

[PRE3]

登录 pinecone 官网 https://app.pinecone.io/ 后，可以查看已创建的索引，如图 13-2 所示。

[PRE4]