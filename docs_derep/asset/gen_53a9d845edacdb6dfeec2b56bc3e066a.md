GraphRAG：让知识图谱成为RAG的推理骨架

微软开源的GraphRAG，其核心思路是用知识图谱把分散在不同文档中的信息连起来，让RAG不再把知识库当成一堆孤立文本。系统先基于知识库构建出实体与关系的网络，检索时除了看节点本身的文字，还会顺着图边去找关联实体，从而获得更宽泛的上下文支撑，检索结果因此更具关联性和逻辑性。

整体工作分为索引和查询两大阶段。

索引阶段主要做两件事。文本切分与实体抽取，把文档拆成段落或句子级别的单元，借助大模型识别出实体、关系以及关键陈述，比如人物、地点、事件等，用来搭建初始图谱。层次聚类与社区归纳，则是用Leiden算法对图进行分层聚类，形成紧密的社区结构，并自底向上生成社区摘要。举例来说，在一个学术库里，数学和物理可能各自聚成独立社区，每个社区都会产出一份主题概要。

查询阶段提供两种模式。全局检索面向整体性问题，例如“过去五年AI研究的趋势”，系统会汇总多个社区摘要进行多轮整合，输出聚合式答案。局部检索则聚焦具体实体，比如“微软Cosmos DB的新功能”，通过向量库定位相关实体，再结合社区报告提炼精准回应。

GraphRAG的优势和代价都很突出。它能大幅扩大覆盖面，把检索物与背后关联节点的关系链条揭示出来，但对算力消耗较大，且生成时容易带入过多无关上下文。针对这些痛点，业界出现了多条优化路径。

LazyGraphRAG大幅压缩了建图成本。它不依赖大模型抽取实体关系，而是通过NLP名词短语和共现关系搭建轻量图谱，索引开销约为传统GraphRAG的0.1%。查询时再动态调用大模型做相关性评估，并结合最佳优先搜索与广度优先搜索，答案质量接近完整版，查询成本却显著下降。

OG-RAG引入领域本体，把文档映射为超图，利用贪心算法挑选覆盖最多相关节点的超边，生成紧凑上下文。比如在农业场景下预定义“作物-土壤”关系，能显著提升领域查询的精确度。

HyBGRAG融合文本向量检索与图结构检索，并通过路由器和反思模块动态调优策略。若首轮结果不够理想，大模型评论器会给出反馈，自动切换检索模块，提升可靠性。

总体看，GraphRAG通过把结构化知识图谱与大模型深度结合，正在重塑检索与生成的范式。随着效率与质量的持续平衡，医疗、金融等领域的应用已在落地。未来在多模态扩展与自适应框架成熟后，GraphRAG有望成为AI认知能力的重要引擎，支撑更智能可信的生成应用。

### 10.2 上下文检索：让分块恢复语义连续性
上下文检索由Anthropic提出，主要解决传统RAG因文本分块导致语义割裂的问题。它在嵌入前为每个块补充解释性上下文，提升检索精度与生成质量。

检索时不仅看查询语义，还会融合会话历史、用户偏好和任务目标等上下文。连续对话中，用户问“公司在2025年第二季度的收入增长”，如果块里只有“收入增长3%”而缺少公司名和时间，就无法匹配。上下文检索会把这些缺失信息自动补进检索范围。

实现方式主要有两类。上下文嵌入，利用大模型为每个块生成约50到100个token的描述性上下文，例如把“收入增长3%”扩展为“该片段来自公司2025年第二季度财报，上季度收入3.14亿美元，本季度增长3%”。上下文BM25，则把原始文本与上下文描述合并建索引，增强术语匹配能力。

Anthropic的实验显示，采用混合检索结合向量语义与BM25，前20个相关块的检索失败率下降约49%，再加入重排可额外降低67%。借助Prompt Caching，文档可一次性载入缓存，减少重复调用大模型的token消耗。面对动态更新的挑战，可结合增量图谱构建或滑动窗口策略进行优化。

### 10.3 模块化RAG：把RAG拆成可拼装的组件
随着需求膨胀，传统RAG的复杂度上升，灵活性和可扩展性受限。同济大学高云帆等人提出的模块化RAG框架，把RAG拆解为可独立替换的模块，形成类似乐高的可重构体系。

图10-3 Modular RAG把传统的RAG系统拆分成多个组件

模块化RAG技术图谱

- 模块与操作符
- 典型RAG流程模式
- RAG流程实现
- 最佳行业案例

模块类型-模块-操作符

| 模块类型 | 索引构建 | 预检索 | 检索 | 后检索 | 生成 | 协调 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 模块 | Chunk Optimization, Structural Organization | Query Routing, Query Expansion | Method Selection, Method Tuning | Repack, Compression/Refinement | Generator Selection, Generator Tuning | Scheduling, Fusion |
| 操作符 | Split, Merge, Embedding, Indexing | Query Transformation, Query Formulation | Search, Retrieval, Reranking | Filtering, Aggregation, Summarization | Prompting, Decoding, Post-processing | Routing, Orchestration, Feedback |

RAG流程模式及实现

推理阶段

调优阶段

- 顺序型
- 条件分支型
- 多路分支型
- 循环型

[PRE0]
[PRE1]
[PRE2]
[PRE3]
[PRE4]

最佳行业案例

[PRE5]

这种设计跳出了线性的检索-生成路径，引入路由、调度与融合，支持条件分支、循环等复杂模式。论文列出了顺序型、条件分支型、多路分支型、循环型等常见模式并分析了实现细节。

主要挑战在于模块间协作复杂。常用对策是引入Agent协作，多智能体分工或自适应决策模型来优化交互。Corrective RAG的自我纠正循环可根据反馈自动调整检索与生成，提升准确率与效率。

### 10.4 代理式RAG：让系统主动规划检索与决策
从RAG到Agentic RAG的演进，标志着系统从被动应答转向主动参与检索与决策。它不仅响应查询，还能自主制定检索计划、挑选信息源、质疑结果并迭代优化，逐渐演化为具备策略思考的智能信息交互体。

核心模式可归纳为四类。

- 反思模式：代理通过迭代评估自身决策和输出质量来优化策略。例如医疗诊断中，根据患者反馈调整检索范围，提高诊断准确性。
- 规划模式：把复杂查询拆解为子任务实现多步推理。金融分析代理可按“数据检索→趋势分析→风险评估”流程生成投资建议。
- 工具使用模式：接入外部API、数据库等工具扩展能力。法律代理可调用合同库与合规规则库生成法律意见书。
- 多智能体协作模式：多个专业代理协同完成复杂任务。客服场景下，FAQ检索、工单处理、情感分析代理可协同提升效率与体验。

CRAG、Self-RAG、RRR等改进也可视作迈向Agentic RAG的阶梯。其典型特征包括对检索文档进行相关性打分、必要时重写查询、利用网罗搜索补全信息。当初始检索不充分时，系统可根据查询复杂度切换单步或多步检索，显著增强复杂问题的回答能力。

### 10.5 多模态RAG：突破文本局限的跨模态检索
当信息扩展到图像、音频、视频等多种模态，Multi-Modal RAG成为必要。它能从图像提取特征、从视频截取片段、从音频提取关键词，并与文本检索结果融合，提供更全面的知识支撑。

技术架构主要包括三层。多模态编码器，如CLIP，把跨模态语义对齐，例如将“红色跑车”文本与对应图像关联。动态检索策略根据查询类型优先处理模态，视觉问答中优先检索图像并辅以文本。跨模态融合则借助注意力机制整合异构信息，医疗报告分析时同时考虑影像与文本。

代表框架有阿里巴巴的OmniSearch和HyBGRAG，均支持文本、表格、图像混合检索，减少幻觉并增强复杂推理。典型场景包括医疗影像分析结合CT与病历生成诊断建议、工业质检整合产品图像与缺陷库实时报警、教育实现视频与原理文本联动检索、零售推荐融合评论、图片与购买记录实现个性化推荐。

未来轻量化多模态模型将降低算力门槛，使消费级硬件也能高效运行。动态路由机制可按任务自动选择最优模态组合，例如优先语音输入后转为文本。Agentic RAG与Multi-Modal RAG的融合更具潜力，自动驾驶中多模态代理可实时整合路况图像、传感器数据与交通规则库进行动态路径规划；医疗、制造等垂直领域的专用MM-RAG框架也前景广阔。

### 10.6 小结：RAG正走向全能信息中枢
除本章所述进展外，RAG生态仍在快速演化。

- 图灵完备RAG，TC-RAG：让检索流程可编程，通过嵌入算子和逻辑，动态组合查询与数据处理，完成复杂推理。
- 迭代式检索，如ITER、CRAG、Self-RAG、RRR：多次调用检索模块，利用上轮结果与生成器反馈修正查询，逐步逼近用户期望。
- 自适应检索，如FLARE：依据系统状态、用户反馈和上下文变化动态调整策略，通过在线学习持续改进准确性。
- IRCoT：将思维链引入检索，不仅返回文档，还呈现检索路径与中间推理，降低盲目性。
- PlanRAG：在检索阶段加入规划，先查领域核心知识再拓展上下游，使结果更条理，便于生成利用。
- T-RAG：引入任务分解与角色分工，多检索器分别处理子任务后汇总，供给生成器多维度材料。
- AutoRAG：自动化优化流程，自动选择策略与参数并闭环调优，减少人工干预。
- RAFT：把检索知识融入大模型微调训练，使推理时具备“知识植入”特性，提升回答实用性。

小冰：这些策略让RAG不再只是海量内容里捞信息，而是具备分层思考、灵活适配与多模态融合的能力。未来的RAG更像全能信息经纪人，自动筛选、分析并整合有价值信息。

咖哥：没错，这些复杂范式的加入，把RAG从单一检索升级为多维度、多策略的知识获取与利用框架。它将与AI Agent、MCP等技术协同，为更高阶的智能交互铺路。

## 参考文献

[1] FAN W, DING Y, NING L, et al. A survey on RAG meeting LLMs: Towards retrieval-augmented large language models[C]//Proceedings of the 30th ACM SIGKDD Conference on Knowledge Discovery and Data Mining. New York: ACM, 2024: 6491-6501.

[2] LIN X V, CHEN X, CHEN M, et al. RADIT: Retrieval-augmented dual instruction tuning[EB/OL]. (2023-10-02)[2025-04-07]. https://arxiv.org/abs/2310.01352.

[3] GAO L, MA X, LIN J, et al. Precise zero-shot dense retrieval without relevance labels[EB/OL]. (2022-12-20)[2025-04-07]. https://doi.org/10.48550/arXiv.2212.10496. DOI: 10.48550/arXiv.2212.10496.

[4] KIM G, KIM S, JEON B, et al. Tree of clarifications: Answering ambiguous questions with retrieval-augmented large language models[C]//Proceedings of the 2023 Conference on Empirical Methods in Natural Language Processing. Stroudsburg: ACL, 2023: 1046-1062.

[5] SARTHI P, ABDULLAH S, TULI A, et al. RAPTOR: Recursive abstractive processing for tree-organized retrieval[EB/OL]. (2024-01-18)[2025-04-07]. https://arxiv.org/abs/2401.18059.

[6] NOGUEIRA R, CHO K. Passage re-ranking with BERT[EB/OL]. (2019-01-14)[2025-04-07]. https://arxiv.org/abs/1901.04085.

[7] XU F, SHI W, CHOI E, et al. RECOMP: Improving retrieval-augmented LMs with compression and selective augmentation[EB/OL]. (2023-10-06)[2025-04-07]. https://arxiv.org/abs/2310.04408.

[8] JIANG H, WU Q, LIN C Y, et al. LLMLingua: Compressing prompts for accelerated inference of large language models[C]//Proceedings of the 2023 Conference on Empirical Methods in Natural Language Processing. Stroudsburg: Association for Computational Linguistics, 2023: 13358-13376.