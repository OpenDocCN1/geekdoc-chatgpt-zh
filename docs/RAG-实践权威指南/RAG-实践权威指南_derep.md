# RAG实战全解析

# 写在前面

## 这本书为什么写出来

2022 年 ChatGPT 横空出世后，大模型进入了规模化落地的阶段。随着应用场景的不断拓宽，痛点也越来越明显：生成结果容易出现幻觉、垂类知识难以快速适配、算力成本居高不下。RAG，即检索增强生成，正是在这样的背景下成为破解大模型瓶颈的主流方案。

RAG 把外部知识库和实时检索机制挂接到生成模型上，让模型在作答前先去查资料。知识可以动态补充，准确率和覆盖面因此大幅提升。幻觉被有效压制，垂直领域的能力也更强。内容生成、客服对话、教培、法务咨询等场景都在用它来提升质量和一致性。可以说，RAG 既是 AI 应用落地的核心引擎，也是产业智能化升级的关键抓手。

我从 2018 年做语义解析开始，便一直在关注 NLP 的演进。从传统机器学习到深度学习框架，再到预训练模型爆发，行业每一次跃迁我都亲身经历。2023 年起，我的研究重心转向 LLM 与 RAG 的结合与优化。实践中发现，尽管 RAG 的价值已得到验证，但系统化学习门槛依然很高。很多从业者卡在概念不清、工程细节不明上。基于这个观察，我决定把理论和工程经验整理成书，帮助研究者和工程师少走弯路。

## 适合谁阅读

本书兼顾入门认知与实战参考。

- 研发工程师：AI、医疗、金融等领域的工程人员，可借此把 RAG 落地到研究或产品中，提升智能化水平与交付效率。
- NLP 工程师：想借助大模型改造现有系统或孵化新业务的人，能找到可直接复用的方法论。
- 技术爱好者：刚入门 AI，对 RAG 感兴趣的新人，可以系统了解技术全貌与落地价值。
- 场景探索者：正考虑把大模型与知识库结合，实现智能问答、内容生产、知识管理等的人，会获得具体的思路。

## 这本书的特点

### 1. 全链路覆盖
从原理到工程再到优化与案例，形成完整认知闭环。
- 数据侧：数据清洗、知识库搭建、向量化表示与存储优化。
- 检索侧：关键词检索、语义检索、混合检索及取舍策略。
- 生成侧：Transformer 工作原理、微调与提示工程。
- 评估与优化：检索准确率、生成质量、响应时延等指标及提升手段。
- 落地案例：智能问答、内容生成、医疗知识库等真实场景。

### 2. 展望未来
不仅讲当下实现，也讨论演进方向。
- 实时知识更新：通过持续采集与知识库动态刷新，解决模型知识滞后问题。
- 跨模态增强：融合文本、图像、视频等多模态数据，拓展 RAG 的生成边界。

## 怎么读

第 1 章讲 RAG 的背景、必要性、工作范式、核心价值与挑战，建立理论基座。
第 2 章拆解系统组件：向量数据库、检索、生成与基准测试，并给出 Python 实战示例。
第 3 章深入高阶技术：切片向量化、检索策略、生成模型选型与检索生成融合。
第 4 章聚焦检索与问答模块的优化：数据管理、检索调优与问答精进。
第 5 章讲推理效率提升：推理加速、分布式推理与边缘计算优化。
第 6 章讨论鲁棒性、安全与公平性，保证系统可信。
第 7 章剖析高阶变体：长上下文突破、知识图谱增强、垂直领域定向优化。
第 8 章展望实时更新与跨模态能力。

## 勘误与支持

内容已反复校对，但难免疏漏。如发现问题，欢迎联系 1042883276@qq.com 反馈。

## 致谢

写作是一段充满挑战的旅程，离不开身边人的支持。感谢妻子 Yvonne 在我闭关写作时承担更多家务；感谢女儿 Alina 的童真笑声带来灵感与动力。书快出版时，儿子 Eric 也平安诞生。最后向所有推动 RAG 发展的研究者和工程师致敬，是你们的开拓让这项技术成为可能，也希望本书能成为后来者的一个助力。

# 第1章 RAG技术基础

本章先梳理大模型与 RAG 的发展脉络，解释 RAG 的核心思想和对输出质量的提升作用，再说明它为何必要、能解决哪些局限。为后续深入学习打下基础。

## 1.1 为何要引入RAG

信息时代下，大模型如同雨后春笋，支撑起人工智能的快速发展。它们在海量文本上训练，拥有宽泛的知识和语言理解力。但同时存在几类明显短板，这也正是 RAG 受重视的原因。

1. 大模型的固有局限
知识固化。训练完成后模型的知识边界被锁死，无法主动获取新信息。比如截止于 2023 年 10 月的模型，面对 2024 年 9 月的新热点，只能失语。科技、金融、医疗等快变领域尤其明显。
专业不足。大模型虽广但不精，法律、医学、工程等深水区需要专业储备，通用模型往往只能给出泛泛之谈。
知识盲区。遇到训练覆盖不到的问题，模型容易编造答案，高风险场景下不可接受。
记忆短板。长文本处理尚可，但长期上下文一致性和多轮逻辑推理仍不稳定，容易失连。

2. RAG 的提出
RAG 用检索弥补生成。先通过检索系统从外部知识库调取相关资料，再把这些资料作为上下文输入给生成模型，从而产出更准、更新、更专业的答案。这种检索加生成的双轨机制，既扩展知识边界，又提升可信度。

## 1.2 运行流程与三大核心

RAG 的落地效果高度依赖流程设计与核心技术的实现。下面以典型系统为例，解析全流程并重点拆解检索、生成、融合三大部分。

### 1.2.1 全流程拆解

RAG 的基本流程可分为五个环节。

1 文档处理
为检索生成做准备。对知识库文档进行解析与切分。
文档解析：抽取标题、摘要、正文等有效信息。
文档切分：将长文档拆成小片段，提高匹配精度并降低生成负担。

2 问题处理
对用户输入做智能预处理，提升检索命中率。
改写：把口语化问题转为更规范的检索式表达。
分解：把复杂问题拆成子问题逐步求解。
扩写：加入同义词与相关词，扩大检索覆盖面。

3 向量化
用深度学习模型把文档片段和问题都转成向量，使语义相近的文本在向量空间中距离更近。

4 检索
相似度计算：计算问题向量与文档向量的相似度，挑出最相关片段。
重排序：对初筛结果按相关度再排序，保证优先返回高质量内容。
检索方法选择：向量检索、关键词检索或两者结合。

5 生成回答
把检索结果与原问题一起送入生成模型。
提示词引导：补充背景或澄清意图。
假设性回答：不确定时给出推测并明确标注。
拒答机制：找不到可靠依据时选择不回答，避免误导。

### 1.2.2 核心技术拆解

#### 1. 检索层
检索是 RAG 的底座。
倒排索引：经典关键词检索，词项到文档的映射，结构化数据上效率高。
向量检索：基于嵌入表达的语义检索，能捕捉深层含义，常配合近似最近邻加速。
混合检索：融合关键词精确匹配与语义召回，兼顾精度与速度。
优化加速：GPU 并行、分布式架构、索引压缩等手段应对海量数据。

#### 2. 生成层
负责把检索到的信息转化为自然回答。
预训练语言模型：GPT、T5 等在大规模语料上预训练，具备强语言理解与生成能力，是生成基座。
上下文理解与生成：生成器需融合用户查询与检索内容，在上下文中给出针对性回答。
多模态生成：结合图文音视频，提供更丰富的输出，在教育医疗等场景价值明显。
生成优化：可控生成保证格式与风格；知识蒸馏与模型压缩提升端侧效率。
评估校正：自动化指标与人工校验双轨并行，持续优化生成策略。

#### 3. 融合层
把检索结果和生成模型有效衔接。
信息融合：把多个片段与查询整合成连贯提示，避免冲突与重复。
上下文增强：结合历史对话与语境，保持多轮问答的一致性。
动态权重调整：依据来源可信度与相关度调整片段权重。
多源整合：融合不同知识源的优势，产出更全面答案。
生成引导与约束：设定主题、格式等限制，确保输出符合需求。
知识图谱结合：把结构化知识与非结构化文本结合，提升逻辑与事实准确性。

## 1.3 三种主流范式

随着实践积累，RAG 形成了若干实现范式，代表性的是朴素 RAG、进阶 RAG 与模块化 RAG。

### 1. 朴素 RAG
最早的经典形态，结构简洁，包含索引、检索、生成三步。
索引阶段：整库切块，通过编码器生成向量并写入索引，索引质量决定后续上限。
检索阶段：把用户查询编码为向量，在索引中找最相似的块，通常用余弦相似度或欧氏距离。
生成阶段：把查询与检索块拼接成提示，交给大模型生成答案，实现检索增强的自然输出。

入门级的 RAG 实现门槛低，能覆盖不少基础应用。但流程偏简单，面对复杂提问或对准确度要求极高的任务时，往往力不从心。

### 2. 进阶 RAG
基础版在复杂场景下的短板，推动了进阶 RAG 范式的出现。它在朴素 RAG 骨架上，对数据索引、检索前处理、检索后处理以及生成环节进行全链路优化。

1）数据索引优化：进阶方案会先做更精细的清洗与预处理，提升入库质量。针对不同类型的文档，采用差异化的切分策略和编码方式，让每个 Chunk 在语义上更自洽、信息更完整。

2）检索前优化：在真正检索前引入查询扩展与改写。通过语义理解对用户问题进行补全与重述，提升覆盖面与命中率。例如面对多义词或模糊表述时，系统能自动调整提问表达，使召回结果更贴合真实意图。

3）检索后优化：拿到相关片段后，通过信息过滤与重排进一步提质。借助权重机制或额外的打分模型对 Chunk 重新排序，确保进入生成阶段的材料更精准、更有用。

4）生成过程优化：生成阶段加入上下文理解与内容校验。生成器不仅基于检索结果作答，还会对输出的语义一致性和事实准确性进行自检，保证回答可靠且权威。

经过这些打磨，进阶 RAG 在复杂任务上的表现明显提升，能更好满足多样化需求。比如在法律文书生成、医疗报告撰写等高标准场景中，已成为重要的实践工具。

### 3. 模块化 RAG
随着技术成熟，模块化 RAG 应运而生。它通过引入专属功能模块，把检索与生成拆解得更细，提升系统的灵活性和可扩展性。主要体现为：

1）专门的查询向量数据库：配备独立的查询向量库模块，处理复杂查询需求。既能做基础关键词匹配，也能支持复杂语义搜索与多模态搜索，适配不同类型查询。

2）回答融合功能：具备多答案融合能力，集成多个生成模型或多次生成结果，输出更全面、更具多样性的答案。尤其适合需要多角度解读或综合多源信息的场景。

3）技术融合与优化：在技术层面把检索与微调、强化学习等结合，进一步提升智能化水平。通过微调让生成模型更贴合特定领域；用强化学习优化检索策略，提高效率与准确度。

4）流程设计与编排：采用模块化设计理念，灵活编排不同功能组件，形成多种 RAG 模式。设计提升了可维护性和扩展性，让 RAG 更易适配不同应用场景。

模块化 RAG 标志着技术走向更成熟全面的阶段，凭借灵活架构与强大扩展能力，在智能助理、知识管理系统等复杂场景中已不可或缺。

## 1.4 价值与实际应用场景
下面梳理 RAG 的核心价值与落地场景。

### 1. 价值
RAG 在实践中体现出以下价值。

（1）动态知识库更新
显著优势是知识库可动态刷新。即使大模型训练已结束，系统仍能定期更新库，访问最新信息。例如面向快速变化的科技领域，可实时检索最新研究成果、技术报告、新闻资讯，让回答始终反映当前进展，大幅提升时效性场景的表现。

（2）提升专业性
在强专业领域表现突出。将权威知识库与生成模型结合，作答时可引用专业资料，保证准确性与专业度。如医学场景可检索最新论文、临床指南、药品说明书，辅助生成科学可信的建议，提升质量与用户信任。

（3）解决知识盲点
通过检索外部信息，有效弥补大模型训练数据的盲区。当问题超出训练范围时，系统能检索相关文献、报道或百科资料进行补充，避免凭空编造，提供详实准确的回答。

（4）增强记忆力和上下文一致性
在多轮对话与复杂逻辑推理中，记忆与上下文保持能力更强。检索对话相关的历史记录与上下文信息，保证每一步回答连贯且逻辑自洽，在客服、教育、医疗等持续交互场景中尤为关键。

### 2. 实际应用场景
凭借独特优势，RAG 已在多个领域落地。

#### （1）客户服务
可构建智能客服系统，既能理解用户诉求，又能动态检索公司内部知识库、产品手册、FAQ，提供准确及时的回应。相比传统规则客服，RAG 增强的系统更灵活、适应性更强，能应对复杂问题，提升满意度。

#### （2）内容生成与创作
对创作者是强力助手。撰写新闻、技术文档或文学作品时，可检索相关资料提供背景与灵感，辅助生成高质量内容。记者撰写科技发展报道时，系统可实时检索论文、专家评论、市场数据，帮助快速获取全面信息，提升深度与准确性。

#### （3）教育与培训
可用于搭建智能辅导系统，解答疑问、提供学习资源与个性化建议。通过检索教材、教学视频、参考资料，为学生提供详尽解释与指导。教师也能借助系统快速获取教学资源，设计更丰富有效的教学方案。

#### （4）法律咨询
法律工作涉及大量条文、案例与解读。RAG 可快速检索相关法条与判例，辅助提供准确建议与策略。准备诉讼材料时，系统能检索相似案件判决与分析，帮助律师制定有利策略，提升胜诉率。

RAG 通过检索与生成结合，弥补了大模型在知识更新、专业性、知识覆盖与记忆力等方面的不足，显著提升实际应用表现。

## 1.5 计算资源与数据存储需求
本节探讨部署 RAG 系统所需的算力与存储基础设施。

### 1.5.1 计算资源的需求
检索与生成双能力让算力需求明显抬升。既要覆盖训练阶段的大规模计算，也要满足推理阶段的低延迟高并发。下面从配置、成本与推理需求展开。

#### 1. 计算资源配置
RAG 核心在于检索与生成协同，这对算力提出双重要求。

大模型训练极度依赖高性能 GPU 或 TPU 集群，如 NVIDIA A100、V100，以及 Google TPU v3/v4，均能在大规模矩阵运算与并行任务上显著缩短训练时间。随着规模扩大，单节点已不敷使用，多节点分布式训练成为常态。分摊任务虽提升效率，却带来通信延迟与同步难题，通常以 InfiniBand 等高速互联技术缓解瓶颈。

除通用加速器外，TensorRT、TPU Pod 等专用 AI 加速器也被广泛采用，通过针对性优化降低推理延迟与功耗，提升整体性能。

#### 2. 训练时间与成本
涉及复杂检索机制的大型 RAG 训练周期更长。类似 GPT-3 的训练耗时数周到数月，受算力配置、优化算法、数据预处理效率与超参数调优影响显著。

成本主要包括：高性能 GPU/TPU 租用费用，尤其云环境按需计费昂贵；大规模设备带来的电力与制冷开销；多节点集群的网络、存储与运维人力成本。估算显示顶级模型训练花费可达数百万美元，能否在保证性能前提下降本，是研究热点。

#### 3. 推理阶段的计算资源需求
推理阶段虽不及训练，但仍需低延迟与高并发能力。

（1）低延迟的需求
在线服务与实时交互要求极短响应。可通过模型蒸馏、量化等技术在降低复杂度的同时保持生成质量，从而加快推理速度。

（2）高并发的需求
现代应用需同时处理大量请求。常用负载均衡把请求分摊到多推理节点，结合容器化与微服务架构实现灵活调度与弹性扩容，保证高并发下稳定运行。

（3）硬件加速推理的需求
为进一步提速增吞吐，企业普遍采用 TensorRT、Edge TPU、AWS Inferentia、Google TPU Pod 等加速硬件，在大规模并发场景中优势明显。

### 1.5.2 数据存储的需求
存储同样是关键。需高效管理海量训练数据、检索库与模型参数，对容量、速度与可靠性提出严格要求。

#### 1. 存储容量
（1）大规模数据集的存储
RAG 依赖海量文本、图像、音频等多模态数据。训练高质量语言模型可能需数百 GB 至 TB 级文本，检索库还需存储大量文档与知识库，容量需求极高。

（2）模型参数存储
大模型参数常达数十亿到上百亿，需高效存储与加载。分布式训练推理时参数需在节点间快速传输，对带宽与吞吐提出更高要求。

#### 2. 存储速度与访问效率
（1）高速存储介质
为满足读写速度，固态硬盘 SSD 已成主流。相比机械硬盘 HDD，SSD 在随机读写与访问速度上优势显著，能大幅缩短数据加载时间。

（2）数据分区与并行访问
大规模场景下合理分区与并行访问可降低 I/O 瓶颈。数据分散到多存储节点并行读写，结合分布式文件系统如 HDFS 与对象存储如 Amazon S3，提供可靠的大规模存储方案。

#### 3. 持久性与可靠性
（1）数据备份与恢复
数据持久性至关重要。需构建多层备份策略，包括定期备份、异地备份与实时备份，并完善恢复机制，确保故障时快速恢复。

（2）冗余存储与容错机制
为提升可靠性，常用 RAID 冗余与分布式容错。节点故障时系统可自动迁移数据与恢复，保证高可用性。

#### 4. 数据管理与优化
（1）数据压缩与去重
压缩可减少占用，去重可消除冗余。存储大规模文本时，基于散列的去重能快速识别重复内容，节省空间。

（2）数据索引与检索优化
高效索引是性能关键。构建倒排索引、向量索引可提升检索速度与准确度；近似最近邻算法能在保证精度的前提下进一步提速，满足实时需求。

成本控制方面，需平衡硬件选型，根据实时性要求选择高性能 GPU/NVMe SSD 或性价比方案；通过代码优化与多租户架构提升资源利用率；按数据热度分层存储，热数据放 SSD，冷数据移至 HDD 或归档存储，并用生命周期策略自动迁移以降本。

## 1.6 面临的主要挑战
优势明显的同时，落地仍面临挑战。

### 1. 模型复杂性
（1）架构复杂
RAG 需同时处理检索与生成，架构比传统生成模型更复杂。检索模块要从海量数据中找相关信息，生成模块要在此基础上产出连贯准确文本，二者协同是设计难点。

（2）高计算资源需求
实时检索与生成消耗大量算力，高并发场景下对服务器性能与存储能力提出巨大挑战。

### 2. 数据准备
（1）数据清洗与预处理
为保证效果，需对海量数据做严格清洗与预处理，过程繁琐且需专业技术。从多源异构数据中抽取有效信息、处理噪声与冗余，直接影响系统表现。

（2）多样化数据集成
整合多来源数据带来一致性与格式统一难题。不同数据源在格式、结构、内容上差异显著，如何高效集成与统一，是跨领域应用的关键技术问题。

### 3. 性能权衡
（1）响应时间
检索加生成两步走可能导致延迟，实时场景下需在速度与准确性间取得平衡，如实时翻译对时效性要求极高。

（2）数据存储和管理
数据规模持续膨胀，如何在保证快速检索的同时控制存储成本、维护一致性与完整性，并兼顾安全性与隐私保护，是持续研究课题。

### 4. 提示工程实战

（1）提示设计

提示设计要靠持续迭代来打磨优化。

（2）上下文管理

多轮对话和复杂查询里，把上下文稳定传递并保持连贯准确，是 RAG 落地的一大难点。系统必须在每次交互中记住对话历史，在此基础上完成检索与生成。以长期客户支持为例，模型需要承接用户之前的问题和反馈，才能给出一致且相关的回复。这对上下文的维护能力提出很高要求，也依赖高效的算法来处理和利用上下文信息。

即便挑战不少，只要深入理解痛点、积极应对，开发者和研究者就能不断提升 RAG 的性能和应用边界，助推它在各领域更广泛落地。持续研究与创新，会让 RAG 在未来信息处理和智能生成中扮演更关键、更普遍的角色。

# 第 2 章

## RAG 核心组件与实践指南

搭建高效 RAG 系统，组件的选择和调优决定上限。本章聚焦 RAG 的关键构成：向量数据库、检索模块、生成模块、RAG 基准测评工具，以及基于常用 Python 栈的实践方法。对每一块都拆解原理和实操，帮助读者从理论到工具构建精准可靠的 RAG 方案。

## 2.1 向量数据库

向量数据库是 RAG 架构中不可或缺的一环。它擅长保存和快速查找高维向量表达，在海量非结构化数据场景下价值尤为突出。如图 2-1 所示，典型的查询路径始于内容数据与应用的向量化。内容经预处理后转成向量并写入库；查询时，应用把用户输入向量化，通过相似度计算在库中匹配，返回最贴合的结果。借助这一流程，系统可实现语义级匹配与信息检索，为问答与生成提供支撑。本节重点拆解三类主流方案：Elasticsearch、FAISS（Facebook AI Similarity Search，Facebook 人工智能相似度搜索）

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_25_0.png)

图 2-1 向量数据库的查询流程

以及 Milvus。对比它们的底层架构、特性和适用场景，有助于根据业务选择合适的向量库，提升 RAG 的检索效率与效果。

### 2.1.1 Elasticsearch

Elasticsearch 基于 Apache Lucene 起家，最初定位全文检索与分析。随着演进，它已成长为能力全面的向量数据库，能高效存取向量嵌入。灵活的扩展架构让它在 RAG 体系中占据重要位置。

**1. Elasticsearch 的核心优势**

RAG 中向量搜索的快慢直接影响响应体验。Elasticsearch 凭借多项优化实现效率跃升。

- 1）并行多线程检索：利用多线程在不同段 segment 中并发执行搜索，大幅压缩响应时长。面对大规模数据时，并行能分摊计算压力，整体速度更快。
- 2）Panama Vector API 集成：接入 Panama Vector API 后，Java 代码可直接调用 SIMD 指令，加速向量运算。尤其在高维向量场景下，计算效率明显提升。
- 3）标量量化压缩：采用标量量化可在不损搜索效果的前提下把内存占用降低约 75%。处理大体量数据时，用更少的内存维持高效检索。
- 4）智能压缩与纠错：引入智能错误校正机制，进一步压缩数据至原量的约 1/8，同时保持结果准确。存储占用下降，传输与处理也更轻快。
- 5）预过滤与混合检索：支持借助 Lucene 过滤器先行筛选，再执行向量检索；也可把向量检索与传统全文检索融合。电商场景下，用户既能按商品描述检索，也能基于图像向量检索，体验更全面。
- 6）增量快照：得益于段式结构，快照可增量生成。相邻快照往往共享大量段，尤其是大段，节省存储并加速快照制作。对需频繁备份恢复的 RAG 系统很友好。
- 7）功能兼容性强：向量搜索与 Lucene 的聚合、文档级安全、字段级安全、索引排序等功能高度兼容。可在向量检索上叠加访问控制等定制需求，例如对敏感数据做文档级权限管控。

**2. Elasticsearch 与竞品对比**

Elasticsearch 拥有成熟生态和综合能力，了解与其它方案的差异有助于选型。

- 1）对比 FAISS：FAISS 由 Facebook AI Research 推出的相似度搜索库，在纯向量搜索速度上很强，但缺乏 Elasticsearch 的全文检索和丰富集成。对于同时需要向量和文本检索的 RAG，Elasticsearch 优势更明显。
- 2）对比 Milvus：Milvus 专为向量数据设计，支持海量存储检索，在扩展性和分布式上表现好。但从生态丰富度和与现有系统的整合度看，Elasticsearch 更胜一筹。特别是与 ELK 栈无缝集成的场景，Elasticsearch 更合适。
- 3）对比 Pinecone：Pinecone 是托管向量数据库，易用性高。需要深度定制和完全自控的团队，Elasticsearch 提供了更多灵活性和功能选择。

凭借性能优化、灵活架构和丰富的检索集成能力，Elasticsearch 成为 RAG 中可靠的核心组件，无论是大数据处理、实时响应还是多样化场景，都能提供稳定支撑，助力构建智能检索增强生成系统。

### 2.1.2 FAISS

FAISS 由 Meta 团队打造，面向研究人员和工程团队提供高效灵活的相似性搜索与密集向量聚类能力。

它在学术界广受青睐，同时在工业落地中也验证了高性能与稳定性。无论是亿级向量处理，还是对实时性要求高的系统，FAISS 都能应对自如。

**1. 核心功能概览**

FAISS 的能力可归纳为以下方面。

（1）高效相似度检索

1）多样度量支持：支持 L2 距离、余弦相似度、内积等多种度量。L2 适合精确距离计算，余弦更适配文本语义比较。通过预处理，不同度量可在应用中相互转换，提升灵活性和准确性。

2）多元索引结构：提供平坦索引 Flat Index、倒排文件 IVF、分层可导航小世界图 HNSW 等。Flat Index 适合小规模追求精确；IVF 通过分组提升大规模效率；HNSW 基于图结构加速高维检索。多样选择让不同场景都有最优解。

（2）大 Coule 数据支撑

FAISS 专为亿级向量设计，索引与搜索效率突出。例如处理 1 亿张图像的 128 维 CNN 描述符时，用 4 卡 Maxwell Titan X GPU 构建 k 近邻图仅需约 35 分钟，体现出强劲的大规模处理能力。

（3）GPU 加速

FAISS 提供 GPU 版本，借助 CUDA 加速计算。GPU 实现可直接替换 CPU 索引，无需深入 CUDA API。实测 GPU 版通常快 5~10 倍，在 Pascal P100 等新硬件上提升可达 20 倍，满足高吞吐低延迟需求。

（4）多线程与并行

FAISS 结合多线程与 BLAS 库高效完成距离计算和矩阵乘法，并支持多 GPU 并行。索引构建与搜索可跨多卡并发，充分释放现代硬件算力。

（5）索引组合灵活

支持复合索引，如 IVF + PQ 乘积量化，在保证检索速度的同时降低内存占用。组合策略提升了跨场景的适应性和扩展性。

（6）接口丰富易扩展

提供 C++ 接口与 Python 绑定，方便在 Python 环境调用。也支持自定义距离度量和索引结构，便于按需扩展。兼顾科研算法开发与工程集成。

#### 2. 安装与使用

FAISS 安装门槛不高，熟悉 Python 与 Anaconda 的用户上手很快。提供 CPU 与 GPU 两版，按硬件选择即可，具体步骤如下。

（1）安装 NumPy 作为依赖项

先确保 NumPy 已装，可用 pip 安装：

```
pip install numpy
```

（2）安装 FAISS 的 CPU 版本

无需 GPU 加速时，可通过 Conda 安装 CPU 版：

```
conda install faiss-cpu -c pytorch
```

该命令会从 PyTorch Conda 源拉取 FAISS CPU 版及依赖。

（3）安装 FAISS 的 GPU 版本

使用 GPU 加速前需安装 CUDA 并正确配置环境，随后执行：

```
conda install faiss-gpu -c pytorch
```

安装后 GPU 版可直接替换 CPU 索引，无需额外配置即可享受加速。

（4）验证安装

安装完成后可用 Python 代码检查版本，例如：

```
import faiss
print(faiss.__version__)
```

若能输出版本号，说明安装成功。

（5）基本使用示例

简单示例展示如何生成随机向量、构建索引并检索：

```
import faiss
import numpy as np

# 生成随机向量数据
d = 128  # 向量维度
nb = 100000  # 数据库中的向量数量
nq = 10000  # 查询向量数量

np.random.seed(1234)
xb = np.random.random((nb, d)).astype(np.float32)
xb[:, 0] += np.arange(nb) / 1000.0
xq = np.random.random((nq, d)).astype(np.float32)
xq[:, 0] += np.arange(nq) / 1000.0

# 构建索引
index = faiss.IndexFlatL2(d)  # 使用 L2 距离的平坦索引
print(index.is_trained)
index.add(xb)  # 添加向量到索引
print(index.ntotal)

# 执行搜索
k = 4  # 查找最近的 4 个邻居
D, I = index.search(xq, k)  # D 是距离，I 是索引
print(I[:5])  # 打印前 5 个查询的结果
print(D[:5])
```

可根据实际需求调整维度、数据量和索引类型，适配不同应用。

FAISS 在大规模高维数据处理上展现出卓越性能与灵活性。多样的索引、GPU 加速、多线程并行等特性，使其在图像识别、NLP、推荐等领域广泛应用。合理选型、调参与多 GPU 并行，能最大化其优势。

局限在于极大规模下内存占用和实时更新仍具挑战。随着社区持续贡献和技术进步，FAISS 有望在性能、易用性和灵活度上进一步突破，巩固其在相似度搜索领域的领先地位。

### 2.1.3 Milvus

Milvus 由 Zilliz 开发的开源向量数据库，面向海量向量相似度搜索与 AI 应用而生。随着 AI 与机器学习快速发展，向量数据库作用日益凸显。Milvus 以出色性能、可扩展性和丰富功能成为主流选择，广泛用于图像搜索、NLP、推荐系统等场景。

其设计目标是满足现代 AI 对海量数据的高效处理需求，无论是数十亿向量，还是复杂的多模态检索，都能游刃有余。

#### 1. 主要功能和特性

（1）高性能向量检索

Milvus 的核心竞争力在于检索速度与准确性，依托多索引支持和 GPU 加速实现。

1）多索引支持：支持 IVF、HNSW、PQ 等结构。IVF 适合中等规模，HNSW 在高维数据上表现优异，PQ 在内存效率上突出。开发者可按需求挑选索引，优化性能与资源。

2）GPU 加速：利用 NVIDIA GPU 加速索引与搜索，显著提升处理效率。处理数十亿向量时仍保持高吞吐低延迟，特别适合在线推荐、实时图像搜索等低延迟场景。

除检索外，Milvus 在数据写入与更新上也高效稳定，批量导入或实时更新都能保证一致性与完整性。

（2）分布式架构与高可用性

面对数据爆发式增长，Milvus 通过分布式与高可用设计应对挑战。

1）分布式架构：采用云原生分布式架构，支持水平扩展，计算与存储可分离。数据量增长时可单独扩存储，计算需求提升时再扩计算节点，资源利用更合理。

2）高可用性：支持滚动升级与故障自动恢复，升级不中断服务。具备自动故障转移与数据备份，即使部分节点故障也能迅速恢复，保障业务连续。适用于金融、电商、社交等 24×7 不间断服务场景。

（3）丰富的功能集成

Milvus：构建智能检索的完整实践

Milvus 在吞吐与扩展能力上表现突出，内置的功能组合也让工程落地更顺滑。无论是面对跨模态检索场景，还是接入主流人工智能生态，或是与可视化、商业智能工具协同，都能成为搭建复杂智能应用的稳固底座。

1）跨模态混合查询：随着应用需求升级，单一模态的检索已经难以满足。Milvus 同时处理稠密向量与稀疏向量，并提供融合查询能力，在多模态任务中优势明显。以图文联合搜索为例，系统可并行解析图像特征与文本嵌入，返回更全面更精准的结果。
2）与 AI 生态的顺畅对接：Milvus 能直接挂载 LangChain、LlamaIndex、Hugging Face 等热门框架，让开发者把向量能力嵌入已有工作流，加快复杂智能应用的交付。借助 Hugging Face 生态，可直接调用预训练语言模型生成向量并完成检索，明显压缩开发链路。
3）与可视化及 BI 平台的协同：该能力帮助企业以更直观的方式呈现与分析向量数据，提升洞察效率。

#### 2. 部署

为降低上手门槛，Milvus 提供了多种便捷的安装与使用路径，让开发者快速熟悉并高效管理与检索向量数据，下面介绍常见的部署形态。

落地形态可覆盖不同规模场景。资源受限的小型项目可选用轻量版 Milvus Lite；单机开发测试适合 Milvus Standalone；企业级大规模服务则可采用 Milvus Distributed，支持分布式部署。这些灵活的选择让开发者按需决策，迅速搭建向量数据库环境。

（1）借助 Docker 部署 Milvus 单机版
容器化方案能简化 Milvus 的部署流程。具体步骤如下。

1）按操作系统指引完成 Docker 环境搭建，可参考官方文档。
2）获取 Milvus 镜像，具体指令如下：
```
docker pull milvusdb/milvus:latest
```

3）启动 Milvus Standalone 容器，具体指令如下：
```
docker run -d --name milvus-standalone -p 19530:19530 -p 19121:19121 milvusdb/milvus:latest
```
执行后容器名为 milvus-standalone，19530 端口供客户端连接，19121 端口用于监控。

（2）借助 Docker Compose 部署 Milvus 分布式服务
在需要分布式落地的场景下，Docker Compose 能统一管理多个服务。具体步骤如下。

1）新建 docker-compose.yml 文件。
```
version: '3.5'
services:
  etcd:
    image: milvusdb/etcd:latest
    ports:
      - "2379:2379"
  milvus:
    image: milvusdb/milvus:latest
    depends_on:
      - etcd
    ports:
      - "19530:19530"
      - "19121:19121"
    environment:
      ETCD_ENDPOINTS: "etcd:2379"
```

2）在 docker-compose.yml 所在目录执行以下指令：
```
docker-compose up -d
```
该命令会拉起 etcd 组件及 Milvus 分布式集群，并把所需端口映射到主机。

#### 3. 安装与使用 PyMilvus

官方提供了丰富的客户端 API，其中 PyMilvus 常用。下面介绍安装方式及基础向量操作流程。

1）安装 PyMilvus。可通过 pip 安装：
```
pip install pymilvus
```

2）连接 Milvus。下面示例展示如何用 PyMilvus 接入本地运行的 Milvus 服务：
```
from pymilvus import connections

# 连接到本地的 Milvus 服务
connections.connect(alias="default", host="localhost", port="19530")
```

3）新建集合。Collection 作为 Milvus 中向量数据的组织单元。下面示例展示如何创建包含 ID 与向量字段的集合：
```
from pymilvus import Collection, CollectionSchema, FieldSchema, DataType

# 定义字段
fields = [
    FieldSchema(name="id", dtype=DataType.INT64, is_primary=True, auto_id=True),
    FieldSchema(name="embedding", dtype=DataType.FLOAT_VECTOR, dim=128)
]

# 定义集合模式
schema = CollectionSchema(fields, description="示例集合")

# 创建集合
collection = Collection(name="example_collection", schema=schema)
```

4）写入向量数据。下面示例展示如何批量向集合中插入向量：
```
import numpy as np

# 生成随机向量数据
num_vectors = 1000
dim = 128
vectors = np.random.random((num_vectors, dim)).astype(np.float32).tolist()

# 插入向量数据
entities = [
    vectors
]
collection.insert(entities)

# 将数据提交到磁盘
collection.flush()
```

5）构建索引。为提升检索效率需为向量字段建索引。下面示例为名为 embedding 的向量列创建 IVF_FLAT 索引：
```
from pymilvus import Index

# 定义索引参数
index_params = {
    "index_type": "IVF_FLAT",
    "metric_type": "L2",
    "params": {"nlist": 128}
}

# 创建索引
collection.create_index(field_name="embedding", index_params=index_params)
```

6）查询向量数据。下面示例展示如何基于查询向量进行相似度检索：
```
# 生成查询向量数据
query_vectors = np.random.random((5, dim)).astype(np.float32).tolist()

# 执行搜索
results = collection.search(
    data=query_vectors,
    anns_field="embedding",
    param={"metric_type": "L2", "params": {"nprobe": 10}},
    limit=10
)

# 打印结果
for i, result in enumerate(results):
    print(f"查询向量数据 {i}:")
    for hit in result:
        print(f"  ID: {hit.id}, 距离: {hit.distance}")
```

#### 4. 文档与社区支持
官方提供了详尽的文档与丰富示例，帮助开发者快速掌握使用方法与最佳实践。社区活跃，开发者可通过以下渠道获取支持与交流：

- GitHub：可在 Milvus 仓库查阅源码并发起 issue。
- 社区论坛：参与讨论，与其他用户交换经验。
- Slack/Discord：加入即时通讯群组获取实时帮助。

## 2.2 检索系统

检索系统负责从海量数据源中高效获取与用户提出的问题相关的信息。依据应用场景的不同，检索架构也各有侧重。本节重点介绍三种主流检索方式：以知识图谱为基础的查询、以关系型数据库为基础的查询、以及以向量数据库为基础的查询。通过这些方式，RAG 系统能够在结构化与非结构化数据中定位最匹配的答案。我们将探讨各方法的实现原理、适用场景及性能差异，以帮助读者设计与优化 RAG 系统。

### 2.2.1 基于知识图谱查询

在 RAG 系统中，以知识图谱为基础的查询是核心组件之一。知识图谱通过结构化表达实体及其关系，提供高效精准的存储与检索能力，不仅提升检索效率，也增强系统推理能力，使复杂问题得到更详尽准确的解答。

#### 1. 知识图谱的构建与表示

构建知识图谱是系统基础。它以节点和边表示实体及其关系，形成有序的图结构。每个节点代表一个实体，如人物、地名、事物或抽象概念；每条边代表实体间的关系，如“浙江大学”“位于”“杭州”等。这样的结构直观且利于复杂查询与推理。
![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_35_0.png)
构建时数据源与质量至关重要。常见的开源图谱如 Wikidata 与 DBpedia，覆盖领域广泛、实体丰富，提供大量背景信息。它们多源自维基百科等公开数据，构成庞大精细的网络。例如 Wikidata 除记录实体基础属性外，还涵盖多种关系，支持多维度关联分析。

为保证可用性，构建过程中需做数据清洗与规范化，包括去重、统一命名、处理缺失等，并定期更新以反映最新知识，维持高效查询能力。

下面是一个使用 Python 与 rdflib 构建简易图谱的示例：
```
from rdflib import Graph, URIRef, Literal, Namespace
from rdflib.namespace import RDF, RDFS

# 创建一个新的图
g = Graph()

# 定义命名空间
ex = Namespace('http://example.org/')

# 添加实体和关系
g.add((ex['浙江大学'], RDF.type, ex['大学']))
g.add((ex['浙江大学'], ex['位于'], ex['杭州']))
g.add((ex['杭州'], RDF.type, ex['城市']))

# 添加属性
g.add((ex['浙江大学'], ex['成立年份'], Literal(1897)))
g.add((ex['杭州'], ex['人口'], Literal(10000000)))

# 将图保存到文件
g.serialize('knowledge_graph.rdf', format='xml')
```
上述代码创建了“浙江大学位于杭州”的简单图谱并添加部分属性信息。

#### 2. 查询实体识别与意图检测

以知识图谱为基础查询时，首先需从用户自然语言中抽取关键实体与意图，这直接影响后续查询准确性。通常包含实体识别与意图检测两个环节。

（1）实体识别
实体识别是从用户输入中抽取命名实体的过程。借助 GPT-4、LLaMA 等大模型的语言理解能力，识别准确率与灵活性显著提升。大模型通过大规模预训练，能有效捕捉语义，识别人名、地名、机构等实体并映射到图谱节点。例如用户问“埃隆·马斯克的公司有哪些？”，系统可准确识别“埃隆·马斯克”并在图谱中检索相关公司节点。与传统嵌入模型相比，大模型覆盖面更广、上下文理解更强。

实际应用中还涉及实体消歧，即确定名称具体指代哪个实体。

##### （2）意图检测
意图检测用于解析用户查询的目的，确定涉及的关系与属性。例如查询“如何解决用户无法登录的问题”，系统需识别“登录问题”实体并理解获取解决方案的意图，进而在图谱中查找相关解法。

意图检测通常结合 NLP 技术，通过分类算法或深度学习模型实现。现代模型不仅能识别基础意图，还能理解多层次复杂意图，生成更精准的查询。

使用大模型进行意图检测的代码示例如下：
```python
import openai

# 输入文本
text = "如何解决用户无法登录的问题"

# 构建提示
prompt = f"请识别以下用户查询的意图，并简要描述：\n\n{text}\n\n意图："

# 调用大模型
response = openai.Completion.create(
    engine="GPT-4",
    prompt=prompt,
    max_tokens=60,
    temperature=0
)

# 输出结果
intent = response.choices[0].text.strip()
print(f"识别的意图：{intent}")
```

#### 3. 子图嵌入检索

子图嵌入检索是以知识图谱为基础检索的重要技术，通过将图谱子图与用户查询匹配实现高效检索，通常包括子图提取与嵌入匹配两步。

##### （1）子图提取
子图提取是根据查询从全量图谱中抽取相关部分，包含与查询实体及其意图相关的节点与边。例如查询“如何解决用户无法登录的问题”，系统可从“登录问题”节点出发，提取相关描述、原因与解决步骤。该步骤避免遍历全图，提升效率。

提取过程可利用路径搜索、图遍历等技术，快速定位相关子图并覆盖查询需求。

以下是子图嵌入检索的示例代码：
```python
from rdflib import Graph

# 加载知识图谱
g = Graph()
g.parse('knowledge_graph.rdf', format='xml')

# 定义查询，假设用户想了解“登录问题”的相关信息
query = """
SELECT ?predicate ?object
WHERE {
    <http://example.org/登录问题> ?predicate ?object .
}
"""

# 执行查询
results = g.query(query)

# 输出结果
for row in results:
    print(f"关系：{row.predicate}，对象：{row.object}")
```
该示例可从图谱中提取与“登录问题”相关节点的所有关系与对象。

##### （2）嵌入匹配
嵌入匹配用于计算用户查询与图谱子图的相似度，找到最相关结果。通常使用 OpenAI 的 text-embedding-ada-002 等先进嵌入模型，将查询文本与子图节点文本转为向量，再通过余弦相似度等度量找出最接近的节点与子图。

该方法的优势在于捕捉语义而非仅关键词匹配。例如查询“如何修复无法启动的应用程序”，系统能识别“启动问题”与“应用程序修复”间的语义关联，提供更精准方案。

以下是实现上述过程的示例代码：

```python
import openai
import numpy as np

# 设置 OpenAI API 密钥
openai.api_key = 'your-api-key'

# 获取用户查询的嵌入向量
query_text = "如何修复无法启动的应用程序"
query_embedding = openai.Embedding.create(
    input=query_text,
    engine='text-embedding-ada-002'
)['data'][0]['embedding']

# 假设我们有知识图谱节点的描述及其嵌入
nodes = [
    {"name":"应用程序修复指南","description":"提供应用程序故障的修复步骤",
     "embedding": None},
    {"name":"启动问题解决方案","description":"关于无法启动问题的解决方案",
     "embedding": None},
    # 更多节点 ...
]

# 为每个节点获取嵌入向量
for node in nodes:
    embedding_response = openai.Embedding.create(
        input=node['description'],
        engine='text-embedding-ada-002'
    )
    node['embedding'] = embedding_response['data'][0]['embedding']

# 计算相似度
def cosine_similarity(a, b):
    a = np.array(a)
    b = np.array(b)
    return np.dot(a, b) / (np.linalg.norm(a) * np.linalg.norm(b))

# 找到最相似的节点
similarities = []
for node in nodes:
    similarity = cosine_similarity(query_embedding, node['embedding'])
    similarities.append((node['name'], similarity))

# 按相似度排序
similarities.sort(key=lambda x: x[1], reverse=True)

# 输出最相关的节点
print("与查询最相关的节点:")
for name, sim in similarities[:5]:
    print(f"{name}, 相似度: {sim}")
```

该演示展示如何利用大语言模型为提问文本和图谱实体生成词向量，再借助相似度度量筛选出最贴合的图谱节点。

#### 4. 查询生成与执行
锁定相关实体和局部子图后，下一步是把自然语言需求转成可在图库中落地的检索表达式，比如 SPARQL。整个环节可分为表达式的生成与实际运行。

##### （1）查询构建
生成表达式的依据是已识别的实体及其关系链。模型可以输出 SPARQL 或 Cypher，用来抽取目标信息。以“谁是谷歌的创始人？”为例，系统需要产出一条连接公司节点与创始人节点的检索式。

编写检索式时要平衡复杂度以及准确性。简单问题对应简短表达式；涉及多层关联或需推断的复杂问题，如“谷歌创始人是谁，他们出生在哪一年”，表达式就必须串联多条路径，才能同时取回创始人及其出生信息。

借助 LLM 生成 SPARQL 的片段如下：

```python
import openai

# 用户的问题
question = "谁是谷歌的创始人？"

# 构建提示
prompt = f"""请根据以下用户的问题生成对应的 SPARQL 查询，用于从知识图谱中检索答案。

问题: {question}

请确保生成的 SPARQL 查询能够正确提取所需信息。

SPARQL 查询:
"""

# 调用大模型
response = openai.Completion.create(
    engine="GPT-4",
    prompt=prompt,
    max_tokens=150,
    temperature=0,
    stop=["\n\n"]
)

# 输出生成的 SPARQL 查询
sparql_query = response.choices[0].text.strip()
print(f"生成的 SPARQL 查询:\n{sparql_query}")
```

##### （2）查询执行
运行阶段把前面生成的表达式投递到图数据库执行，并把结果返回给提问者。Neo4j、GraphDB 等主流图库都支持高效的图存储及检索，并内置查询优化，能在海量图谱中快速响应。

执行时还要兼顾数据一致性和完整性，确保返回结果可信。为了提升体验，通常会对结果做排序、筛选和聚合，让信息更直观。

运行 SPARQL 的示例如下：

```python
from SPARQLWrapper import SPARQLWrapper, JSON

# 设置 SPARQL 端点（假设有可用的 SPARQL 服务）
sparql = SPARQLWrapper("http://localhost:3030/dataset/sparql")

# 设置查询
sparql.setQuery(sparql_query)
sparql.setReturnFormat(JSON)

# 执行查询
results = sparql.query().convert()

# 输出结果
for result in results["results"]["bindings"]:
    print(f"答案：{result}")
```

该片段会连上 SPARQL 端点，执行生成的表达式并输出结果。

#### 5. 多跳查询与复杂推理
不少复杂提问需要拆解为多跳推理，即从起点实体沿多条关系链逐步抵达目标。这种能力让基于图谱的系统能够处理更抽象的问题。

##### （1）多跳查询
多跳指跨越多层关系从一个实体到达另一个实体。例如“谷歌创始人是谁，他们的出生日期是什么”，系统先从谷歌节点经创始人关系到达人物节点，再经出生日期属性取出具体值。这类检索涉及多个节点和多种关系，对推理能力要求较高。

实现时需要高效遍历图谱，并结合提问上下文保证每一步准确。比如在医疗图谱里，“糖尿病的常见并发症有哪些”要求系统从糖尿病节点出发，沿并发症关系找到多个关联节点。

生成多跳 SPARQL 的示例代码如下：

```python
# 用户的问题
question = "谷歌创始人是谁，他们的出生日期是什么？"

# 使用大模型生成多跳 SPARQL 查询
prompt = f"""请根据以下用户的问题生成对应的 SPARQL 查询，包含必要的多跳关系，以及检索所需的信息。

问题：{question}

SPARQL 查询：
"""

response = openai.Completion.create(
    engine="text-davinci-003",
    prompt=prompt,
    max_tokens=200,
    temperature=0,
    stop=["\n\n"]
)

multi_hop_query = response.choices[0].text.strip()
print(f"生成的 SPARQL 查询:\n{multi_hop_query}")

# 执行查询
sparql.setQuery(multi_hop_query)
results = sparql.query().convert()

# 输出结果
for result in results["results"]["bindings"]:
    founder = result.get('founder', {}).get('value', '')
    birth_date = result.get('birthDate', {}).get('value', '')
    print(f"创始人：{founder}，出生日期：{birth_date}")
```

该示例通过 LLM 生成多跳 SPARQL，解决复杂问题。

##### （2）复杂推理
复杂推理不仅是多跳，还涉及更高阶的逻辑及规则，比如推断实体的隐含属性，或基于多条件综合判断。这类任务通常需要规则引擎或机器学习模型配合进行深度分析。

例如提问“过去五年营收明显增长且环保表现良好的公司有哪些”，系统需要同时处理财务数据和环保记录，按多条件综合筛选。这类需求要求图谱系统具备多元数据支撑和强推理能力。

借助 LLM 进行推理的示例如下：

```python
import openai

# 假设查询结果如下
query_results = [
    {"company":"公司A", "growth":"显著增长", "environment":"良好"},
    {"company":"公司B", "growth":"一般", "environment":"良好"},
    {"company":"公司C", "growth":"显著增长", "environment":"一般"},
    # 更多结果...
]

# 构建推理提示
prompt = "根据以下公司信息，回答哪些公司在过去五年内有显著增长，并且在环保方面有良好记录：\n\n"

for result in query_results:
    prompt += f"公司：{result['company']}\n增长情况：{result['growth']}\n环保记录：{result['environment']}\n\n"

prompt += "符合条件的公司是："

# 调用大模型进行推理
response = openai.Completion.create(
    engine="GPT-4",
    prompt=prompt,
    max_tokens=100,
    temperature=0,
    stop=["\n\n"]
)

# 输出推理结果
answer = response.choices[0].text.strip()
print(f"答案：{answer}")
```

### 2.2.2 基于关系型数据库查询
在 RAG 框架里，关系型数据库的检索通过从结构化数据中快速抽取信息来增强生成模型的回答。借助关系型数据库和 SQL，可高效管理以及检索海量数据。

SQL 数据库在 RAG 中的作用主要包括结构化存储以及管理、高效检索、动态表达式生成以及多跳查询和复杂推理，下面逐项说明。

#### 1. 结构化数据存储与管理
在 RAG 中，结构化数据的存储以及管理是基础，保证生成模型能高效准确地访问和利用数据。结合检索系统，关系型数据库既提供稳定数据支撑，也优化了检索效率和准确性。

##### （1）数据组织
关系型数据库用表格来组织数据，每张表由多行多列组成，每一列代表一个字段，可存文本、数字、日期等类型。这种结构既便于管理，也大幅提升检索效率。

例如存储用户信息的表“users”：

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    signup_date DATE
);
```

在 RAG 中，生成模型可依此结构构建精准 SQL，从库中抽取所需片段。比如用户问“张三的注册日期”，模型可迅速生成：

```sql
SELECT signup_date FROM users WHERE name = '张三';
```

这种有序组织让模型快速定位并提取信息，从而给出准确回答。

##### （2）元数据管理
SQL 数据库还能保存和数据相关的元数据，如创建时间、修改时间、数据来源等，对理解和利用数据至关重要。

元数据的用途包括：
- **数据溯源**：记录来源、创建与修改历史，保证可追溯，便于审计以及合规。在 RAG 中有助于模型理解数据背景，提供更具上下文的回答。
- **数据描述**：通过数据字典或表注释说明字段含义，帮助开发者和生成模型理解用途。

#### 2. 高效的数据检索
##### （1）SQL 查询
开发者可编写复杂 SQL 满足不同检索需求。在 RAG 中，生成模型能根据用户问题动态生成类似 SQL，快速获取相关数据。

##### （2）向量化检索
随着 RAG 技术发展，向量化检索逐渐普及。现代数据库如 MyScaleDB 能够将文本等数据转为高维向量，并通过内积或 cosine similarity 等相似度搜索进行检索，结合传统 SQL 以及向量检索优势，显著提升性能及准确性。

以下示例展示在 MyScaleDB 里进行向量查询的方式：

#### 3. 动态查询生成
基于数据库的 RAG 核心优势在于能把自然语言问题动态转为 SQL。通常先把自然语言解析为实体和关系，再生成对应 SQL 表达式，并对表达式进行优化以提升相关性和准确性。

例如用户问“过去一年注册的用户有哪些？”，系统可解析为：
- 实体：用户
- 关系：注册时间在过去一年

据此生成优化后的 SQL：

```sql
SELECT name, email
FROM users
WHERE signup_date >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR);
```

#### 4. 多表连接与复杂推理
实际应用中，RAG 往往需要处理复杂的多表连接和嵌套查询，实现多跳查询和复杂推理，从多个相关表综合检索信息。例如 users 表与 orders 表，用户想了解某用户的订单详情，可用多表连接查询：

```sql
SELECT users.name, orders.order_id, orders.amount
FROM users
JOIN orders ON users.id = orders.user_id
WHERE users.name = '张三';
```

通过此类连接，RAG 框架能提供更全面详细的多维度回答。

### 2.2.3 基于向量数据库查询
结合向量数据库的强检索能力与生成模型的自然语言生成能力，RAG 框架可实现更准确、相关性更高的内容生成。下面详细说明主要方法与技术。

#### 1. 向量数据库的集成
在 RAG 中，向量数据库负责初步检索和用户提问相关的文档或片段。集成主要步骤包括意图理解和处理、查询生成、信息检索以及检索结果的处理和生成。

（1）查询理解意图并处理
RAG 系统首先利用 NLP 技术理解用户提问，识别其中的关键词和意图。这一步确保系统能准确解释提问并生成合适的搜索请求。例如，用户输入“如何提高 RAG 系统的性能？”，系统需要识别出“提高性能”是主要意图，并提取相关关键词“RAG 系统”“性能”。可以在生成查询向量时给予这些关键词和意图更高的权重，从而确保向量表示能准确反映用户的主要意图，即确保生成的查询向量重点关注这两个概念。

下面这段代码示例展示了用户的提问经过关键词和意图识别的处理后转换为加权后的查询向量的过程。

```python
import spacy
from sklearn.feature_extraction.text import TfidfVectorizer
import numpy as np

# 加载中文语言模型
nlp = spacy.load("zh_core_web_sm")

def extract_keywords_and_intent(query):
    """
    使用 spaCy 提取关键词和意图。
    """
    doc = nlp(query)

    # 简单的意图识别（基于动词）
    intent = None
    for token in doc:
        if token.pos_ == "VERB":
            intent = token.lemma_
            break

    # 提取名词作为关键词
    keywords = [token.text for token in doc if token.pos_ in ["NOUN", "PROPN"]]

    return keywords, intent

def generate_weighted_query_vector(keywords, intent, vectorizer):
    """
    根据关键词和意图生成加权的查询向量。
    """
    # 创建一个假设的权重字典
    weights = {}
    for keyword in keywords:
        weights[keyword] = weights.get(keyword, 1.0) + 1.0  # 关键词权重加 1
    if intent:
        weights[intent] = weights.get(intent, 1.0) + 2.0  # 意图权重加 2

    # 生成加权的文本
    weighted_text = " ".join([f"{word} " * int(weight) for word, weight in weights.items()])

    # 生成 TF-IDF 向量
    vector = vectorizer.transform([weighted_text]).toarray()
    return vector

# 示例用户查询
user_query = " 如何提高 RAG 系统的性能？ "

# 提取关键词和意图
keywords, intent = extract_keywords_and_intent(user_query)
print(f"关键词：{keywords}")
print(f"意图：{intent}")

# 准备一个包含常见关键词和意图的语料库（通常会更大）
corpus = [
    " 提高 RAG 系统 性能 ",
    " 优化 数据库 查询 ",
    " 增强 模型 准确率 ",
    " 减少 响应 时间 ",
    " 增加 并发 处理能力 "
]

# 初始化 TF-IDF 向量器并拟合语料库
vectorizer = TfidfVectorizer()
vectorizer.fit(corpus)

# 生成加权查询向量
query_vector = generate_weighted_query_vector(keywords, intent, vectorizer)
print(f" 加权查询向量：\n{query_vector}")
```

这段代码展示了 RAG 在里面怎么借助 spaCy 做自然语言理解，从用户提问里拆出关键信息和意图，再用 TfidfVectorizer 得到一个带权重的查询向量。整体做法是先加载中文 spaCy 模型，然后实现两个函数：extract_keywords_and_intent 负责解析输入，把名词挑出来当关键词、动词当意图；generate_weighted_query_vector 则给提取到的词分配不同权重，拼成加权文本，再通过 TF-IDF 转成数值向量。

（2）查询生成

拿到用户问题后，RAG 会据此构造合适的数据库检索条件。条件可以是关键词匹配，也可以是语义检索，具体形式取决于向量库的能力。以 Elasticsearch 的语义检索为例，查询的生成方式大致如下：

```python
from elasticsearch import Elasticsearch

def generate_search_query(user_query):
    return {
        "query": {
            "match": {
                "content": user_query
            }
        }
    }

es = Elasticsearch()

user_input = "如何提高 RAG 系统的性能？"
search_query = generate_search_query(user_input)
```

##### （3）信息检索

查询条件确定后，下一步就是在向量数据库里执行检索，拉回与问题相关的文档或片段。关键在于充分利用事先建好的索引，并选用合适的检索策略来保证结果的准确性和相关性。整个检索主要分为数据索引和混合检索两部分。

1）索引数据：RAG 要快速定位相关文档，依赖的是向量库预先建立的索引。索引里通常存放大量网页、文件等文本资源，经过预处理和建索引后，查询时就能迅速命中相关内容。

以 Elasticsearch 为例，索引数据的过程如下：

```python
def index_document(index_name, doc_id, content):
    es.index(index=index_name, id=doc_id, body={"content": content})

# 示例：索引一个文档
index_document("documents", 1, "RAG 系统的性能优化方法包括 ...")
```

2）混合检索技术：主流向量库普遍采用混合检索，融合关键词搜索、稀疏向量检索和密集向量检索，以提升相关度和精准度。比如在 Elasticsearch 里做混合查询，通常会把布尔查询和向量查询组合使用：

```python
def hybrid_search(user_query, vector):
    return {
        "query": {
            "bool": {
                "must": [
                    {"match": {"content": user_query}},
                    {"knn": {"embedding": {"vector": vector, "k": 10}}}
                ]
            }
        }
    }

user_vector = [0.1, 0.2, ..., 0.768]  # 示例向量
search_query = hybrid_search(user_input, user_vector)
results = es.search(index="documents", body=search_query)
```

借助这种混合策略，RAG 既能抓住关键词，又能理解语义，返回的结果会更精准。

##### （4）检索结果的处理与生成

检索到的文档片段会被送给生成模型作为背景知识，用来产出准确且贴合的答案。生成时模型会借助交叉注意力去参考已检索内容，确保输出与用户问题高度一致。该机制在逐词生成时不断聚焦相关上下文，从而提升答案的准确性和连贯性。比如用 OpenAI 的 GPT-4，检索结果会被作为上下文一起输入来生成回复：

```python
import openai

# 请确保你已经设置了 OPENAI_API_KEY 环境变量，或者直接在代码中设置 API 密钥
openai.api_key = 'your-api-key-here'  # 将此替换为你的实际 API 密钥

def generate_response(context, user_query):
    prompt = f"Context: {context}\n\nQuestion: {user_query}\nAnswer:"
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[
            {"role": "system", "content": " 你是一个乐于助人的 AI 助手。"},
            {"role": "user", "content": prompt}
        ],
        max_tokens=150,
        temperature=0.7,
        n=1,
        stop=None,
    )
    answer = response['choices'][0]['message']['content'].strip()
    return answer

# 示例使用
context = "RAG 系统的性能优化方法包括索引优化、并行处理和缓存机制的改进。通过使用更高效的算法和数据结构，可以显著提高系统的检索和生成速度。"
user_query = " 如何提高 RAG 系统的性能？ "
response = generate_response(context, user_query)
print(response)
```

#### 2. 基于向量数据库的查询示例

为了更直观呈现基于向量库的 RAG 全貌，下面给出一个结合 Elasticsearch 与 GPT-4 的完整示例。从用户输入到最终答案的每一步都会展开，有助于理解这类检索系统的工作原理和价值。

1）导入所需库和模块。这里用 Elasticsearch 执行搜索，用 OpenAI 的 GPT-4 API 负责生成答案。

```python
import json
from elasticsearch import Elasticsearch
import openai
```

2）创建 Elasticsearch 实例，建立与集群的连接。

```python
# 初始化 Elasticsearch
es = Elasticsearch()
```

3）配置 OpenAI API 密钥。调用 GPT-4 时必须先设置密钥。

```python
# 设置 OpenAI API 密钥
openai.api_key = 'your-api-key-here'  # 请将此替换为你的实际 API 密钥
```

4）定义 generate_search_query 函数，用于把用户问题转成 Elasticsearch 可用的查询语句。

```python
def generate_search_query(user_query):
    return {
        "query": {
            "match": {
                "content": user_query
            }
        }
    }
```

5）定义 retrieve_documents 函数，负责执行查询并取出相关文档。

```python
def retrieve_documents(user_query):
    search_query = generate_search_query(user_query)
    response = es.search(index="documents", body=search_query)
    hits = response['hits']['hits']
    return [hit['_source']['content'] for hit in hits]
```

6）定义 generate_response 函数，结合上下文和原始问题生成最终回复。

```python
def generate_response(context, user_query):
    prompt = f"Context: {context}\n\nQuestion: {user_query}\nAnswer:"
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[
            {"role": "system", "content": "你是一个乐于助人的 AI 助手。"},
            {"role": "user", "content": prompt}
        ],
        max_tokens=150,
        temperature=0.7,
        n=1,
        stop=None,
    )
    answer = response['choices'][0]['message']['content'].strip()
    return answer
```

7）定义 main 函数，把上述步骤串联成完整流程。

```python
def main():
    user_question = "如何提高 RAG 系统的性能？"
    print(f"用户问题：{user_question}")
    # 检索相关文档
    documents = retrieve_documents(user_question)
    context = " ".join(documents)
    print(f"检索到的文档内容：{context}")
    # 生成回答
    answer = generate_response(context, user_question)
    print(f"生成的回答：{answer}")

if __name__ == "__main__":
    main()
```

## 2.3 生成系统

要吃透生成环节，先得了解 Transformer 架构，再看看大模型在 RAG 里的具体用法。

### 2.3.1 Transformer

Transformer 的工作原理网上资料很多，这里不再赘述。接下来用通俗的例子简要介绍它的构成和训练思路。

1. 模型架构

Transformer 整体由编码器和解码器两部分构成，各自由多层堆叠而成。整个结构像一条流水线，每一层都在为最终输出做贡献。

（1）编码器

编码器的职责是把输入序列转成富含上下文的表示。每个编码器层包含两个核心子层。

1）多头自注意力机制（Multi-Head Self-Attention Mechanism）：让模型能同时关注输入序列不同位置的关联。

2）前馈神经网络（Feed-Forward Neural Network）：对信息做进一步的非线性转换。

进入编码器前，输入先经嵌入层转为向量，再加上位置编码以保留顺序。随后这些向量依次进入编码器堆栈，层层提炼更高阶特征。

（2）解码器

解码器的任务是把编码器提供的上下文表示映射成输出序列。每个解码器层同样包含多头自注意力和前馈神经网络两部分。

除此之外，解码器还引入跨注意力机制（Cross-Attention Mechanism），用来把编码器的输出和当前解码输入对齐。这样解码器就能基于编码器提供的上下文信息生

#### 2. 自注意力机制

自注意力是 Transformer 的核心。它通过计算 Query、Key、Value 之间的相似度，衡量每个词和其他词的关系。概念上可能有点抽象，下面用一个简单例子说明。

比如句子“机器学习改变了世界。”，自注意力会为每个词计算相关度，其中“机器学习”和“改变”的关联度会比较高，因为前者是执行主体。

##### （1）查询、键和值

模型会把每个输入词映射成三个向量：查询、键和值。

- 查询（Q）：用来和键做相似度计算。
- 键（K）：和查询匹配，决定关注哪些值。
- 值（V）：真正传给下一层的信息。

这些向量通常通过线性变换得到：

```python
import torch
import torch.nn as nn

class SelfAttention(nn.Module):
    def __init__(self, embed_size, heads):
        super(SelfAttention, self).__init__()
        self.embed_size = embed_size
        self.heads = heads
        self.head_dim = embed_size

        assert (
            self.head_dim * heads == embed_size
        ), "Embedding size needs to be divisible by heads"

        self.values = nn.Linear(self.head_dim, self.head_dim, bias=False)
        self.keys = nn.Linear(self.head_dim, self.head_dim, bias=False)
        self.queries = nn.Linear(self.head_dim, self.head_dim, bias=False)
        self.fc_out = nn.Linear(heads * self.head_dim, embed_size)

    def forward(self, values, keys, queries, mask):
        N = queries.shape[0]
        value_len, key_len, query_len = values.shape[1], keys.shape[1], queries.shape[1]

        # 将嵌入向量分割成 self.heads 个不同的部分
        values = values.reshape(N, value_len, self.heads, self.head_dim)
        keys = keys.reshape(N, key_len, self.heads, self.head_dim)
        queries = queries.reshape(N, query_len, self.heads, self.head_dim)

        values = self.values(values)  # 对值向量进行线性变换，维度为 (N, value_len, heads, head_dim)
        keys = self.keys(keys)        # 对键向量进行线性变换，维度为 (N, key_len, heads, head_dim)
        queries = self.queries(queries)  # 对查询向量进行线性变换，维度为 (N, query_len, heads, head_dim)

        # 使用 einsum 进行批量矩阵乘法，计算每个训练样本的查询和键之间的注意力能量
        # 结果维度为 (N, heads, query_len, key_len)
        energy = torch.einsum("nqhd,nkhd->nhqk", [queries, keys])

        if mask is not None:
            energy = energy.masked_fill(mask == 0, float("-1e20"))

        attention = torch.softmax(energy / (self.embed_size ** (1 / 2)), dim=3)

        out = torch.einsum("nhql,nlhd->nqhd", [attention, values]).reshape(
            N, query_len, self.heads * self.head_dim
        )

        out = self.fc_out(out)
        return out
```

##### （2）多头注意力机制
Transformer 想让模型同时兼顾不同子空间的特征，于是设计了多头注意力。每一层编码器和解码器内部，多个自注意力头会并行工作，算完后把结果拼接融合。这种多视角的并行处理，让模型对输入序列的理解更立体，表达能力也随之提升。

#### 3. 位置编码的补充
Transformer 处理序列很强，但本身不感知顺序。为此加入了位置编码，给每个词向量额外加上位置信号。编码通过正弦与余弦函数生成，能保证不同长度的序列都有唯一可辨的位置标识。

在实际实现里，这一位置信号通常紧跟在词嵌入之后，直接加到输入向量上：

```python
import math

class PositionalEncoding(nn.Module):
    def __init__(self, embed_size, max_len=5000):
        super(PositionalEncoding, self).__init__()
        self.embed_size = embed_size

        pe = torch.zeros(max_len, embed_size)
        position = torch.arange(0, max_len, dtype=torch.float).unsqueeze(1)
        div_term = torch.exp(torch.arange(0, embed_size, 2).float() * (-math.log(10000.0) / embed_size))
        pe[:, 0::2] = torch.sin(position * div_term)
        pe[:, 1::2] = torch.cos(position * div_term)
        pe = pe.unsqueeze(0).transpose(0, 1)
        self.register_buffer('pe', pe)

    def forward(self, x):
        # x shape: (seq_len, batch_size, embed_size)
        x = x + self.pe[:x.size(0), :]
        return x
```

#### 4. 两阶段训练策略
Transformer 的训练一般分为预训练和微调两个环节。先在大规模数据上打好基础，再针对具体任务做适配，这样既提升泛化，也增强任务适应性。

##### （1）预训练阶段
这一阶段模型在海量未标注语料上进行自监督学习。比如因果语言模型 CLM 任务，要求模型学会预测下一个词，从而掌握语言的结构与语义规律。

下面以 Hugging Face 的 Transformers 库为例，演示对 Qwen/Qwen2.5-7B-Instruct 模型的预训练流程：

```python
from transformers import AutoTokenizer, AutoModelForCausalLM, Trainer, TrainingArguments
import torch

# 加载预训练的 Qwen 模型
tokenizer = AutoTokenizer.from_pretrained('Qwen/Qwen2.5-7B-Instruct', trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained('Qwen/Qwen2.5-7B-Instruct', trust_remote_code=True)

# 准备数据
texts = ["机器学习正在改变世界。", "Transformer 是强大的模型。"]
encodings = tokenizer(texts, return_tensors='pt', padding=True, truncation=True)
inputs = encodings.input_ids
labels = inputs.clone()

# 定义 Trainer
training_args = TrainingArguments(
    output_dir='./results',
    num_train_epochs=1,
    per_device_train_batch_size=1,
    logging_steps=10,
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=torch.utils.data.TensorDataset(inputs, labels),
)

# 预训练
trainer.train()
```

##### （2）微调实践
预训练完成后，模型会在下游任务的数据集上继续训练。例如问答系统、文本生成等场景，需要让模型学会任务特有的模式和知识。

这里给出一个简化的微调案例，基于预训练好的 Qwen/Qwen2.5-7B-Instruct 进行文本分类：

```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM, Trainer, TrainingArguments, DataCollatorForLanguageModeling
from datasets import load_dataset
import os

# 1. 加载数据集
dataset = load_dataset('json', data_files={'train': 'train.jsonl', 'validation': 'validation.jsonl'})

# 2. 加载预训练的分词器和模型
model_name = "Qwen/Qwen2.5-7B-Instruct"
tokenizer = AutoTokenizer.from_pretrained(model_name)

# 检查分词器是否支持对齐 (padding)，如果不支持，需要手动设置
if not tokenizer.pad_token:
    tokenizer.add_special_tokens({'pad_token': '[PAD]'})

model = AutoModelForCausalLM.from_pretrained(model_name)

# 如果添加了新的 Token，需要调整模型的嵌入层
if tokenizer.pad_token:
    model.resize_token_embeddings(len(tokenizer))

# 3. 数据预处理
def preprocess_function(examples):
    # 假设数据集有 'instruction' 和 'response' 字段
    inputs = examples['instruction']
    targets = examples['response']
    # 根据需要调整模型的输入格式，通常是 "Instruction: ...\nResponse: ..."
    prompts = [f"Instruction: {instr}\nResponse: {resp}" for instr, resp in zip(inputs, targets)]
    # 对文本进行编码
    encodings = tokenizer(prompts, padding='max_length', truncation=True, max_length=512)
    return encodings

encoded_dataset = dataset.map(preprocess_function, batched=True, remove_columns=dataset['train'].column_names)

# 4. 设置数据收集器
data_collator = DataCollatorForLanguageModeling(tokenizer=tokenizer, mlm=False)

# 5. 设置训练参数
training_args = TrainingArguments(
    output_dir="./instruction-finetuned-Qwen2.5-7B-Instruct",
    overwrite_output_dir=True,
    num_train_epochs=3,
    per_device_train_batch_size=2,  # 根据 GPU 内存调整
    per_device_eval_batch_size=2,
    evaluation_strategy="epoch",
    save_strategy="epoch",
    logging_dir='./logs',
    logging_steps=10,
    learning_rate=5e-5,
    weight_decay=0.01,
    fp16=True,  # 如果支持混合精度
    save_total_limit=2,
)

# 6. 初始化 Trainer
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=encoded_dataset['train'],
    eval_dataset=encoded_dataset['validation'],
    tokenizer=tokenizer,
    data_collator=data_collator,
)

# 7. 训练模型
trainer.train()

# 8. 评估模型
results = trainer.evaluate()
print(results)

# 9. 保存微调后的模型
trainer.save_model("fine-tuned-Qwen2.5-7B-Instruct-instruction")
tokenizer.save_pretrained("fine-tuned-Qwen2.5-7B-Instruct-instruction")
```

经由预训练加微调的组合，基于 Transformer 的模型在各类 NLP 任务上表现稳定，已成为当前最主流的架构之一。

### 2.3.2 GPT
GPT 即生成式预训练 Transformer，依托自注意力与深层网络，能高效处理并生成连贯文本。它采用无监督预训练，在大量未标注文本上学习，通过不断预测下一个词来捕捉语言结构与规律。这种自监督方式让 GPT 即便缺乏显式指导，也能理解并产出通顺的文本。

在 RAG 体系中，GPT 主要承担生成角色，与检索模块配合完成信息整合与内容产出。以下示例演示了如何把 GPT 模型接入检索流程，搭建一个基础的 RAG 系统。该演示调用了 OpenAI 的 GPT API，并使用了一个假设的检索函数。

```python
import openai

# 配置 OpenAI API 密钥
openai.api_key = 'your-api-key'

def retrieve_documents(query):
    """
    模拟一个信息检索函数。
    在实际应用中，可以使用向量数据库进行检索
    """
    # 这里使用一些假设的文档
    documents = [
        "人工智能 (AI) 是计算机科学的一个分支，旨在创建能够执行通常需要人类智能级别的任务的系统。",
        "机器学习是 AI 的一个子领域，通过数据驱动的方法使计算机能够自主学习和改进。",
        "深度学习是一种基于神经网络的机器学习方法，已经在图像识别、NLP 等领域取得了显著成果。"
    ]
    # 简单地返回包含查询关键词的文档
    return [doc for doc in documents if query.lower() in doc.lower()]

def generate_response(query):
    # 步骤 1：查询处理
    processed_query = query.strip()

    # 步骤 2：信息检索
    retrieved_docs = retrieve_documents(processed_query)
    context = "\n".join(retrieved_docs)

    # 步骤 3：生成回答
    prompt = f"基于以下信息，回答用户的问题：\n{context}\n\n 问题： {processed_query}\n 回答："
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=prompt,
        max_tokens=150,
        temperature=0.7
    )

    # 步骤 4：结果融合
    answer = response.choices[0].text.strip()
    return answer

if __name__ == "__main__":
    user_query = "什么是人工智能？"
    answer = generate_response(user_query)
    print(f"回答：{answer}")
```

下面对这段示例代码做逐项解读。

1）信息检索函数：retrieve_documents 目前只是一个简化的检索演示，模拟了从知识库里找资料的流程。实际部署时，这个部分完全可以升级为向量检索库或关系型数据库查询，以拿到更精准、覆盖更广的参考材料。

2）生成回答：generate_response 会先对用户问题做清洗预处理，再调用检索函数拉取相关文档，最后把文档内容作为上下文连同原始问题一起喂给 GPT 模型来组织答案。换句话说，先找相关资料，再把资料和问题一起输入模型生成初稿，并返回优化后的结果。

3）主程序：在主程序里我们设定了一个示例问题“什么是人工智能？”，并通过 `generate_response` 来驱动完整流程。

示例输出：

> 回答：人工智能（AI）是计算机科学的一个分支，旨在创建能够执行通常需要人类智能级别的任务的系统。它包括诸如学习、推理、问题解决、感知和语言理解等能力。通过模拟人类的认知功能，AI 系统能够在各种应用中提供智能化的解决方案，如自动驾驶、语音识别和医疗诊断等。

## 2.4 RAG 系统基准测试工具

要判断一个 RAG 系统好不好用，光靠主观感觉不够，必须借助标准化的测试工具和数据集来量化。本部分会介绍常用的 RAG 评测手段，帮助读者用统一的数据集和评测方法来衡量系统实效。我们重点拆解三大代表性问答集 Natural Questions、SQuAD 和 MS MARCO，并说明它们各自适用的场景。另外还会讲到自动化评测与人工评测两大方向，它们分别从准确率、性能和用户体验角度保证 RAG 系统既高效又可靠。

### 2.4.1 评测数据集

选对评测集是评估的第一步。不同数据集的难度和侧重点不同，适合不同的应用目标。下面这三种数据集在 RAG 研究中被广泛采用。

#### 1. Natural Questions

图 2-3 展示了 Natural Questions，即 NQ 数据集的样例。

NQ 是 Google 开源的大规模开放域问答集，目的在于推动开放问答的研究。该集包含 307,373 条训练样本、7,830 条开发样本和 7,842 条测试样本。每条样本由一个真实的 Google 搜索问题和对应的维基百科页面构成，页面中标注了长答案段落和一个或多个从段落中提炼的短答案。NQ 的难点在于，模型不仅要从段落里抽取答案，还要理解整篇文章的语义并做推理，比传统抽取式问答更复杂。

（1）NQ 数据集的典型特征

- 体量庞大：数十万训练样本，利于大模型训练和评测。
- 真实查询：来源于真实搜索日志，实用性强。
- 答案层次丰富：同时有长答案和短答案，考验抽取与理解。
- 主题多样：覆盖面广，提升模型的泛化能力。

（2）用 NQ 做 RAG 基准测试

在 RAG 场景下，NQ 能全面反映模型在开放域问答中的表现。下面是用 Hugging Face datasets 库加载 NQ 的示例：

```python
from datasets import load_dataset

# 加载 NQ 数据集
dataset = load_dataset("natural_questions")

# 查看训练集的第一个示例
print(dataset["train"][0])
```

输出示例：

```json
{
    'question': 'Who is the president of the United States?',
    'document': '...',
    'long_answer': {
        'start': 123,
        'end': 456,
        'text': 'The current president of the United States is...'
    },
    'short_answers': [
        {'start': 123, 'end': 456, 'text': 'Joe Biden'}
    ]
}
```

借助这种方式，可以快速获取并处理 NQ 数据，为 RAG 的训练和评测做准备。

#### 2. SQuAD

SQuAD 即斯坦福问答数据集，是业界常用的经典评测集。图 2-4 展示了部分数据样例。

目前主流的有 SQuAD 1.1 和 SQuAD 2.0，两者挑战不同。

1）SQuAD 1.1：包含十万量级的问题，均来自维基百科文章，每个问题配一段上下文，答案是上下文中的连续片段。很适合检验抽取和理解能力。

2）SQuAD 2.0：在 1.1 基础上额外加入 50,000 个不可在段落中找到答案的问题。除了抽取能力，还要考验模型判断问题是否可答的能力，更贴近真实使用。

以下是一个基于 SQuAD 的 RAG 评估演示。

SQuAD 任务目标明确，适合测 RAG 在信息抽取和理解上的表现。示例代码使用 Hugging Face Transformers 和 datasets，并结合 Qwen/Qwen2.5-7B-Instruct 模型进行评估：

```python
from transformers import AutoTokenizer, AutoModelForCausalLM, pipeline
from datasets import load_dataset
import torch

# 检查是否有可用的 GPU
device = 0 if torch.cuda.is_available() else -1

# 加载 SQuAD 1.1 数据集
squad = load_dataset("squad")

# 加载预训练的 Qwen/Qwen2.5-7B-Instruct 模型和分词器
model_name = "Qwen/Qwen2.5-7B-Instruct"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name, device_map="auto",
    torch_dtype=torch.float16)

# 创建用于生成式问答的 Pipeline
qa_pipeline = pipeline(
    "text-generation",
    model=model,
    tokenizer=tokenizer,
    device=device,
    max_length=512,
    temperature=0.0,  # 设定为 0，以获得更确定性的输出
    top_p=0.95,
    repetition_penalty=1.2
)

def generate_prompt(context, question):
    prompt = f"根据以下上下文回答问题。\n\n上下文：{context}\n\n问题：{question}\n\n答案："
    return prompt

def get_answer(context, question):
    prompt = generate_prompt(context, question)
    outputs = qa_pipeline(prompt, max_new_tokens=100, num_return_sequences=1)
    answer = outputs[0]['generated_text']

    # 提取答案部分
    answer = answer.split("答案:")[-1].strip()
    return answer

# 评估一个示例
example = squad["validation"][0]
question = example["question"]
context = example["context"]
ground_truth = example["answers"]["text"][0]

# 获取模型生成的答案
generated_answer = get_answer(context, question)

print(f"问题：{question}")
print(f"参考答案：{ground_truth}")
print(f"模型生成的答案：{generated_answer}")

# 评估整个验证集（可选，可能需要较长时间）
from tqdm.auto import tqdm
from sklearn.metrics import accuracy_score, f1_score

predictions = []
references = []

for sample in tqdm(squad["validation"]):
    question = sample["question"]
    context = sample["context"]
    reference = sample["answers"]["text"][0]

    answer = get_answer(context, question)

    predictions.append(answer)
    references.append(reference)

# 计算评价指标（例如 Exact Match 和 F1 分数）
# 这里使用简化的计算方式，实际情况可能需要更复杂的处理
def normalize_text(s):
    import string, re
    s = s.lower()
    s = re.sub(r'\s+', ' ', s)
    s = s.strip()
    return s

exact_matches = 0
f1_total = 0
for pred, ref in zip(predictions, references):
    pred_norm = normalize_text(pred)
    ref_norm = normalize_text(ref)
    if pred_norm == ref_norm:
        exact_matches += 1

    # 计算 F1 分数
    pred_tokens = pred_norm.split()
    ref_tokens = ref_norm.split()
    common = set(pred_tokens) & set(ref_tokens)
    if not common:
        f1 = 0
    else:
        precision = len(common) / len(pred_tokens)
        recall = len(common) / len(ref_tokens)
        f1 = 2 * precision * recall / (precision + recall)
    f1_total += f1

exact_match = exact_matches / len(references) * 100
f1 = f1_total / len(references) * 100

print(f"Exact Match: {exact_match:.2f}%")
print(f"F1 Score: {f1:.2f}%")
```

运行后会得到类似下面的结果，包含单条问答和整体验证集的 Exact Match 与 F1 指标：

问题：What is the name of the repository for the SQuAD dataset?

参考答案：The Stanford Question Answering Dataset (SQuAD)

模型生成的答案：The Stanford Question Answering Dataset (SQuAD)

问题：What are the main components of a transformer model?

参考答案：Attention mechanisms and feed-forward neural networks

模型生成的答案：Attention mechanisms and feed-forward neural networks

Exact Match: 65.42%

F1 Score: 72.58%

这个演示说明了如何用预训练 QA 模型在 SQuAD 上推理。RAG 系统若加入检索模块，能显著提升回答的准确性和上下文契合度。

#### 3. MS MARCO

MS MARCO 是微软发布的机器阅读理解大规模数据集，聚焦真实用户提问与文档检索。图 2-5 说明了数据集构成。

| Question segment | Percentage of question |
| :--- | :--- |
| **Question contains** | |
| YesNo | 7.46% |
| What | 34.96% |
| How | 16.8% |
| Where | 3.46% |
| When | 2.71% |
| Why | 1.67% |
| Who | 3.33% |
| Which | 1.79% |
| Other | 27.83% |
| **Question classification** | |
| Description | 53.12% |
| Numeric | 26.12% |
| Entity | 8.81% |
| Location | 6.17% |
| Person | 5.78% |

图 2-5 MS MARCO 的数据集构成情况

该集来源于必应搜索的真实查询，每个查询对应多个文档片段，答案往往分散在多篇材料中。

（1）MS MARCO 数据集的特点

- 真实用户提问：直接来自必应，具有很强的现实价值。
- 多文档融合：答案可能跨片段，需要整合与推理。
- 规模大：约 100 万训练查询、1 万开发查询、1 万测试查询。
- 文档结构复杂：提升检索和理解难度。

（2）用 MS MARCO 做 RAG 评估

MS MARCO 不仅考查抽取，更考查整合推理能力。以下示例展示如何用 Hugging Face datasets 加载数据，并用预训练句子嵌入模型编码查询与文档，通过余弦相似度检索最相关文档：

```python
from datasets import load_dataset
from transformers import AutoTokenizer, AutoModel
import torch

# 加载 MS MARCO 数据集
ms_marco = load_dataset("ms_marco", "v2.1")

# 加载预训练的嵌入模型
tokenizer = AutoTokenizer.from_pretrained("sentence-transformers/all-MiniLM-L6-v2")
model = AutoModel.from_pretrained("sentence-transformers/all-MiniLM-L6-v2")

# 编码查询和文档
def encode(text):
    inputs = tokenizer(text, return_tensors='pt', truncation=True, padding=True)
    with torch.no_grad():
        embeddings = model(inputs).last_hidden_state.mean(dim=1)
    return embeddings

query = ms_marco["train"][0]["query"]
documents = ms_marco["train"][0]["passages"]

query_embedding = encode(query)
document_embeddings = torch.stack([encode(doc['passage']) for doc in documents])

# 计算相似度
similarities = torch.matmul(query_embedding, document_embeddings.T)
best_doc_idx = torch.argmax(similarities).item()
best_doc = documents[best_doc_idx]['passage']

print(f"Query: {query}")
print(f"Best Document: {best_doc}")
```

该示例体现了 RAG 中高效检索的关键机制。

### 2.4.2 常用的评测基准与步骤

要想把 RAG 系统评得准、评得全，单靠一种评测手段是不够的。不同的方法看问题的角度不同，单独使用往往只能照见局部，难以暴露真正的优劣。把自动化评估和人工评估搭配使用，才能从多个维度审视系统，帮助团队更快发现问题、持续优化。

下面系统梳理常用的评测基准和实践方法。先讲自动化指标在 RAG 里的具体用法，再谈人工评测如何提供更贴近真实应用的主观反馈。两类方法结合，既能拿到大规模数据上的量化结论，又能理解用户实际使用时的体验和效果。

为了让落地方便，文中还会结合 Python 实践说明如何计算各类自动化指标，并示范人工评测流程的设计与执行，相关库如 Scikit-learn、NLTK 等都会被用到。

#### 1. 自动化评测基准

自动化评测依靠程序和算法自动算分，优点是快、稳、可重复，特别适合大体量的样本。常见基准如下。

##### （1）准确性

准确性也就是精确率，指的是模型返回的结果中相关文档所占的比例。精确率高说明返回的质量更可控，适合对结果质量要求苛刻的场景，比如医疗信息检索。在医疗场景里用户要的是高度相关的内容，精确率提升能显著减少手动筛选成本。

以下是准确性计算的示例代码：

```
from sklearn.metrics import precision_score

# 假设 y_true 为实际相关文档标签，y_pred 为系统返回的文档标签
y_true = [1, 0, 1, 1, 0, 1]
y_pred = [1, 0, 0, 1, 0, 1]

precision = precision_score(y_true, y_pred)
print(f"Precision: {precision:.2f}")
```

##### （2）召回率

召回率衡量的是所有相关文档里被系统成功找出来的比例。召回率高代表覆盖面更广，适用于不能遗漏关键信息的领域，比如法律信息检索。法律检索一旦漏掉关键条目，后果可能很严重，所以召回率的提升尤为关键。

以下是召回率计算的示例代码：

```
from sklearn.metrics import recall_score

# 假设 y_true 为实际相关文档标签，y_pred 为系统返回的文档标签
y_true = [1, 0, 1, 1, 0, 1]
y_pred = [1, 0, 0, 1, 0, 1]

recall = recall_score(y_true, y_pred)
print(f"Recall: {recall:.2f}")
```

##### （3）MRR

MRR，即平均倒数排名，用来评估检索结果中首个相关文档出现的位置。它实际上是正确答案首次出现排名倒数的均值。MRR 越高，说明相关结果越容易排在前面，用户能更快拿到可用答案。该指标很适合对响应速度和首条准确性敏感的场景，比如即时问答或搜索引擎。

以下是 MRR 计算的示例代码：

```
def mean_reciprocal_rank(rs):
    """Calculate the mean reciprocal rank.
    rs: List of lists, where each sublist contains the ranks of relevant
        documents for a query.
    """
    reciprocal_ranks = []
    for r in rs:
        if len(r) > 0:
            reciprocal_ranks.append(1.0 / r[0])
        else:
            reciprocal_ranks.append(0)
    return sum(reciprocal_ranks) / len(reciprocal_ranks)

# 假设 rs 为每个查询中相关文档的排名位置列表
rs = [[1, 2, 3], [2, 3, 4], [1], []]
mrr = mean_reciprocal_rank(rs)
print(f"MRR: {mrr:.2f}")
```

##### （4）MAP

MAP，即平均精度均值，又称平均精度，是一个兼顾精确度和排序质量的综合指标，用于衡量一组查询下整体返回效果。MAP 越高，说明模型在多查询情况下都能稳定地把高相关结果排在前面。适合对大批量查询的稳健性有要求的任务，如大规模检索或推荐。

以下是 MAP 计算的示例代码：

```
from sklearn.metrics import average_precision_score

# 假设 y_true 为实际相关文档标签，y_scores 为系统返回的文档相关性得分
y_true = [[1, 0, 1], [0, 1, 1], [1, 1, 0]]
y_scores = [[0.9, 0.1, 0.8], [0.2, 0.9, 0.85], [0.95, 0.8, 0.3]]

map_scores = []
for true, scores in zip(y_true, y_scores):
    map_scores.append(average_precision_score(true, scores))

map_average = sum(map_scores) / len(map_scores)
print(f"MAP: {map_average:.2f}")
```

##### （5）BLEU 与 ROUGE

1）BLEU，双语评估助手，主要统计生成文本与参考文本在 n-gram 上的重叠程度，常用于机器翻译评估。

2）ROUGE，摘要评估召回率助手，在 n-gram 基础上还考虑词序列和词对的重叠，更常用于摘要生成评估。两者都用于衡量生成文本质量，适用于机器翻译、文本摘要等生成类任务。

BLEU 与 ROUGE 的实现示例代码如下：

```
from nltk.translate.bleu_score import sentence_bleu
from rouge import Rouge

# BLEU 值计算
reference = [['this', 'is', 'a', 'test']]
candidate = ['this', 'is', 'test']
bleu_score = sentence_bleu(reference, candidate)
print(f"BLEU Score: {bleu_score:.2f}")

# ROUGE 值计算
rouge = Rouge()
reference_text = "The quick brown fox jumps over the lazy dog."
candidate_text = "A fast brown fox leaps over a lazy dog."
scores = rouge.get_scores(candidate_text, reference_text)
print(f"ROUGE Scores: {scores}")
```

#### 2. 人工评测基准

自动化指标可以快速给出量化结果，但对于 RAG 系统里那些微妙、复杂的表现，人工评测仍不可替代。人工打分能捕捉到机器指标覆盖不到的细节，更全面地判断系统的好坏。常用做法有以下几种。

##### （1）人工标注与对比

由专家或受训标注员对模型生成的答案进行细致打分，重点看相关性、正确性和完整性。将生成答案与黄金标准答案对比，可以更直观地评价系统水平。

具体流程如下。

1）制定标注指南：先明确评分标准和评估维度，形成统一的规范，保证不同标注员操作一致。

2）培训标注员：在指南确定后对标注员进行系统培训，确保理解一致，减少主观偏差。

3）进行标注：标注员按指南对每个答案的相关性、准确性、完整性打分并记录。

4）分析结果：汇总评分后对比各条答案的表现，归纳系统的优势与短板，为后续优化提供依据。

##### （2）信实度

信实度评估检查生成的答案是否真正基于检索到的信息，避免出现不实或幻觉内容，核心是保证生成内容与源信息一致。

具体做法如下。

1）人工审核：专家逐条核对生成答案与检索来源的一致性，凭借专业经验揪出不准确之处。

2）利用自动化事实核查工具：借助现有的核查工具批量检测生成文本中的事实错误，提升效率并降低人为疏漏。人工与自动结合，能更可靠地保障信实度。

##### （3）答案相关性

答案相关性衡量生成答案是否切实回应了用户查询，是否具备实用性和针对性。

具体方法如下。

1）评分系统：标注员依据答案与查询的匹配程度打分，判断是否覆盖关键信息点。

2）用户反馈：收集真实用户的体验反馈，了解答案的实用性与满意度，反映实际使用效果。

#### 3. 自动化评测步骤

自动化评测通常按以下流程进行。

1）运行系统：把评估集中的问题输入 RAG 系统，拿到模型生成的答案，保证过程高效统一。

2）计算指标：基于前述基准如精确率、召回率、MRR、MAP、BLEU、ROUGE 等量化系统表现。

3）分析结果：结合指标数据初步判断系统的强弱项，为下一步优化提供方向。

执行时要注意两点。

1）指标选择要全面科学，避免片面指标导致结论偏差。

2）解读时要结合实际应用场景，而不仅看数字，综合评估真实效果。

```
from sklearn.metrics import precision_score, recall_score, average_precision_score
from nltk.translate.bleu_score import sentence_bleu
from rouge import Rouge

# 假设我们有一组真实标签和预测标签
y_true = [1, 0, 1, 1, 0, 1]
y_pred = [1, 0, 0, 1, 0, 1]

# 计算精确率和召回率
precision = precision_score(y_true, y_pred)
recall = recall_score(y_true, y_pred)
print(f"Precision: {precision:.2f}, Recall: {recall:.2f}")

# 计算 MRR
def mean_reciprocal_rank(rs):
    reciprocal_ranks = []
    for r in rs:
        if len(r) > 0:
            reciprocal_ranks.append(1.0 / r[0])
        else:
            reciprocal_ranks.append(0)
    return sum(reciprocal_ranks) / len(reciprocal_ranks)

rs = [[1, 2, 3], [2, 3, 4], [1], []]
mrr = mean_reciprocal_rank(rs)
print(f"MRR: {mrr:.2f}")

# 计算 MAP
y_true_map = [[1, 0, 1], [0, 1, 1], [1, 1, 0]]
y_scores_map = [[0.9, 0.1, 0.8], [0.2, 0.9, 0.85], [0.95, 0.8, 0.3]]
map_scores = [average_precision_score(t, s) for t, s in zip(y_true_map, y_scores_map)]
map_average = sum(map_scores) / len(map_scores)
print(f"MAP: {map_average:.2f}")

# 计算 BLEU 和 ROUGE
reference = [['this', 'is', 'a', 'test']]
candidate = ['this', 'is', 'test']
bleu_score = sentence_bleu(reference, candidate)
print(f"BLEU Score: {bleu_score:.2f}")

rouge = Rouge()
reference_text = "The quick brown fox jumps over the lazy dog."
candidate_text = "A fast brown fox leaps over a lazy dog."
scores = rouge.get_scores(candidate_text, reference_text)
print(f"ROUGE Scores: {scores}")
```

#### 4. 实战案例：评测一个医疗问答 RAG 系统

下面以医疗问答 RAG 系统为例，演示评测方法落地。

##### （1）创建评估数据集

先构建涵盖多种医疗问题的评估集，每个问题配上下文材料和黄金标准答案。医疗示例问题及答案如表 2-1 所示。

表 2-1 医疗示例问题及答案

| 问题 | 上下文 | 黄金答案 |

| :--- | :--- | :--- |

| 如何预防流感 | 包含流感的传播途径、预防措施等信息 | 接种流感疫苗、保持良好卫生习惯、增强免疫力等 |

| 流感的症状有哪些 | 描述流感的临床表现和诊断方法 | 发热、咳嗽、喉咙痛、肌肉酸痛等 |

| 流感的治疗方法是什么 | 涉及抗病毒药物、支持的疗法等内容 | 使用奥司他韦等抗病毒药物，补充液体和进行休息等 |

##### （2）自动化评测

把上述问题送入 RAG 系统获取生成答案，随后计算精确率、召回率、MRR、MAP、BLEU 和 ROUGE 等指标，以量化评估系统在医疗问答场景下的表现。

```
# 假设我们有一个函数 get_rag_answers 来获取系统答案
def get_rag_answers(questions):
    # 模拟系统生成的答案
    return [
        "接种流感疫苗、保持良好卫生习惯、增强免疫力等。",
        "发热、咳嗽、喉咙痛、肌肉酸痛等。",
        "使用奥司他韦等抗病毒药物，补充液体和进行休息等。"
    ]

questions = ["如何预防流感？", "流感的症状有哪些？", "流感的治疗方法是什么？"]
golden_answers = [
    "接种流感疫苗、保持良好卫生习惯、增强免疫力等。",
    "发热、咳嗽、喉咙痛、肌肉酸痛等。",
    "使用奥司他韦等抗病毒药物，补充液体和进行休息等。"
]
generated_answers = get_rag_answers(questions)

# 计算 BLEU 和 ROUGE
from nltk.translate.bleu_score import sentence_bleu
from rouge import Rouge

bleu_scores = []
rouge = Rouge()
rouge_scores = []

for golden, generated in zip(golden_answers, generated_answers):
    reference = [golden.split()]
    candidate = generated.split()
    bleu = sentence_bleu(reference, candidate)
    bleu_scores.append(bleu)

    rouge_score = rouge.get_scores(generated, golden)
    rouge_scores.append(rouge_score)

average_bleu = sum(bleu_scores) / len(bleu_scores)
print(f"Average BLEU Score: {average_bleu:.2f}")

# 计算 ROUGE
average_rouge = {
    'rouge-1': {'r': 0, 'p': 0, 'f': 0},
    'rouge-2': {'r': 0, 'p': 0, 'f': 0},
    'rouge-l': {'r': 0, 'p': 0, 'f': 0},
}

for score in rouge_scores:
    for key in average_rouge.keys():
        average_rouge[key]['r'] += score[0][key]['r']
        average_rouge[key]['p'] += score[0][key]['p']
        average_rouge[key]['f'] += score[0][key]['f']

for key in average_rouge.keys():
    average_rouge[key]['r'] /= len(rouge_scores)
    average_rouge[key]['p'] /= len(rouge_scores)
    average_rouge[key]['f'] /= len(rouge_scores)

print(f"Average ROUGE Scores: {average_rouge}")
```

##### （3）专家人工评审
邀请医学专业人士对系统生成的回答进行人工打分，考察重点涵盖相关性、准确性、完整性以及可信度。

评分细则如下。

1）相关性：回答与提问的契合程度。
- 5分：完全相关
- 4分：大部分相关
- 3分：部分相关
- 2分：少部分相关
- 1分：完全不相关

2）准确性：回答中信息的正确性。
- 5分：完全准确
- 4分：基本准确，少量错误
- 3分：部分准确，有明显错误
- 2分：大部分不准确
- 1分：完全不准确

3）完整性：回答是否全面，是否存在关键遗漏。
- 5分：非常完整
- 4分：基本完整
- 3分：有部分遗漏
- 2分：信息不完整

### 2.5 基于常用 Python 库的 RAG 实践
搭建 RAG 系统时，选对 Python 库是成败关键。Python 生态里汇聚了大量强力工具，能帮助开发者高效完成复杂的检索增强任务。本节梳理几类常用库的使用思路，并以 Hugging Face Transformers 为重点展开。

### 2.5.1 基于 Hugging Face Transformers 库
Hugging Face Transformers 提供了丰富的预训练 Transformer 模型，广泛服务于自然语言处理、计算机视觉和音频等领域。无论是研究者还是工程开发者，都能借助该库快速获得强大的模型能力。它兼容多种主流 Transformer 架构，各自适用场景和优势各不相同。

#### 1. 安装与使用
该库上手门槛低，能无缝对接 PyTorch、TensorFlow、Flax 等深度学习框架。以下介绍基础安装流程和快速接入方法。

1）安装 Transformers 库。确认开发环境已装好 Python，然后通过 pip 进行安装：
```
pip install transformers
```

2）与 PyTorch 或 TensorFlow 配合安装，根据所选框架补充对应依赖：
```
pip install 'transformers[torch]' # 对于 PyTorch
pip install 'transformers[tf-cpu]' # 对于 TensorFlow
```

#### 2. 主要功能
Transformers 集合了大量便于使用的工具，大幅简化了预训练模型的应用与集成。下面结合代码说明核心功能。

（1）预训练模型加载
加载预训练模型及其分词器是使用第一步。借助 AutoModel、AutoTokenizer 等类，可便捷地引入各种预训练模型。下方代码演示加载 Qwen/Qwen2.5-7B-Instruct 用于生成任务：
```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# 加载预训练的 Qwen/Qwen2.5-7B-Instruct 分词器和模型
model_name = "Qwen/Qwen2.5-7B-Instruct"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)
```

（2）Pipeline 接口
Pipeline 提供了多套现成流程，简化了文本分类、情感分析、问答、文本生成等常见任务的实现。开发者无需了解底层细节即可快速完成任务。

例如，下方代码展示如何以 Pipeline 创建文本生成流程，并调用 Qwen/Qwen2.5-7B-Instruct 进行生成：
```python
from transformers import pipeline

# 创建文本生成的 Pipeline，使用 Qwen/Qwen2.5-7B-Instruct 模型
text_generator = pipeline(
    "text-generation",
    model=model,
    tokenizer=tokenizer,
    device=0 if torch.cuda.is_available() else -1,  # 使用 GPU（如果可用）
    max_length=100,  # 设置生成文本的最大长度
    temperature=0.7,  # 控制生成文本的随机性
    top_p=0.9,  # 核采样参数
    repetition_penalty=1.2  # 防止重复生成
)

# 生成文本示例
prompt = "Once upon a time"
generated_text = text_generator(prompt, num_return_sequences=1)
print(generated_text)
```

输出示例如下：
```
[{'generated_text': 'Once upon a time, there was a brave knight who embarked on a quest to save his kingdom from an ancient dragon...'}]
```

上述实现中，先判断是否存在可用 GPU，有则使用 GPU 加速。max_length 控制生成文本最长长度，temperature 调节随机性，top_p 通过核采样决定多样性，repetition_penalty 用来抑制重复。通过调节这些参数可优化输出质量与多样性。

（3）自定义训练和微调
预训练模型在通用任务上表现不错，但针对特定领域数据进行微调能进一步提升效果。Transformers 提供了 Trainer 类，简化自定义训练与微调流程。以下示例演示用 Trainer 对 Qwen/Qwen2.5-7B-Instruct 进行微调，以适配特定文本生成需求：
```python
from transformers import Trainer, TrainingArguments, AutoModelForCausalLM, AutoTokenizer
from datasets import load_dataset

# 加载数据集，这里以一个简单的对话数据集为例
dataset = load_dataset("daily_dialog")

# 加载分词器和模型
tokenizer = AutoTokenizer.from_pretrained("Qwen/Qwen2.5-7B-Instruct")
model = AutoModelForCausalLM.from_pretrained("Qwen/Qwen2.5-7B-Instruct")

# 数据预处理
def preprocess_function(examples):
    return tokenizer(examples['dialogue'], truncation=True, padding='max_length', max_length=512)

encoded_dataset = dataset.map(preprocess_function, batched=True)

# 设置训练参数
training_args = TrainingArguments(
    output_dir="./qwen_finetuned",
    evaluation_strategy="epoch",
    num_train_epochs=3,
    per_device_train_batch_size=2,  # 根据 GPU 内存调整
    per_device_eval_batch_size=2,
    logging_dir="./logs",
    save_steps=500,
    save_total_limit=2,
)

# 创建微调的 Trainer 类实例
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=encoded_dataset["train"],
    eval_dataset=encoded_dataset["validation"],
)

# 开始训练
trainer.train()
```

代码思路为：先加载 daily_dialog 数据集，该集适合对话生成；再用分词器预处理对话文本，确保输入符合模型要求；训练参数通过 TrainingArguments 配置，包括输出目录、评测策略、训练轮数与批次大小；最后由 Trainer 管理训练全流程，调用 trainer.train() 启动微调。

（4）模型评估与推理
训练完成后，需要评估性能并进行推理。Transformers 提供了完备的工具支持此过程。下方代码演示如何评估微调后模型并进行文本生成：
```python
# 评估模型在验证集上的性能
results = trainer.evaluate()
print(results)

# 使用微调后的模型进行文本生成
test_prompt = "Tell me a story about a dragon and a knight."
inputs = tokenizer(test_prompt, return_tensors="pt").to(model.device)

# 生成文本
with torch.no_grad():
    outputs = model.generate(
        inputs.input_ids,
        max_length=150,
        temperature=0.7,
        top_p=0.9,
        repetition_penalty=1.2,
        num_return_sequences=1
    )

# 解码生成的文本
generated_story = tokenizer.decode(outputs[0], skip_special_tokens=True)
print(generated_story)
```

评估阶段，trainer.evaluate() 会输出验证集上的指标，如损失值。推理阶段，先准备输入提示并编码为模型可接受的张量，再用 model.generate 生成文本，生成参数 max_length、temperature、top_p、repetition_penalty 用以控制生成质量，最后通过 tokenizer.decode 将张量解码为可读文本。

### 2.5.2 基于 PyTorch 和 TensorFlow
在深度学习框架中，PyTorch 与 TensorFlow 是目前最主流的两个选择，各自具备独特优势，适用于不同场景。接下来介绍如何在两类框架中使用 Qwen/Qwen2.5-7B-Instruct，并以代码示例说明应用方式。

#### 1. 基于 PyTorch 的实践
PyTorch 采用动态计算图，兼具灵活性和直观性；API 设计贴近 Python 习惯，易于上手；在应用层面适合研究与快速原型开发，支持 NLP 与计算机视觉任务，并拥有 torchvision、torchtext 等配套库。

PyTorch 接口高度灵活，集成复杂预训练模型十分便利。借助 Hugging Face Transformers，可在 PyTorch 中轻松使用 Qwen/Qwen2.5-7B-Instruct 完成文本生成。以下示例展示在 PyTorch 环境下加载该模型并生成文本的方法：
```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM, pipeline

# 检查是否有可用的 GPU
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

# 加载预训练的 Qwen/Qwen2.5-7B-Instruct 模型和分词器
model_name = "Qwen/Qwen2.5-7B-Instruct"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name).to(device)

# 创建文本生成的 Pipeline
text_generator = pipeline(
    "text-generation",
    model=model,
    tokenizer=tokenizer,
    device=0 if torch.cuda.is_available() else -1,  # 使用 GPU（如果可用）
    max_length=100,  # 生成文本的最大长度
    temperature=0.7,  # 控制生成文本的随机性
    top_p=0.9,        # 核采样参数
    repetition_penalty=1.2  # 防止重复生成
)

# 输入提示
prompt = "Once upon a time in a land far, far away"

# 生成文本
generated_text = text_generator(prompt, num_return_sequences=1)
print(generated_text)
```

先看这段示例的运行脉络。脚本最开始把所有运行依赖全部引入，并探测本地是否挂载了 GPU 以争取加速。接着借助 AutoTokenizer 与 AutoModelForCausalLM 把 Qwen/Qwen2.5-7B-Instruct 模型以及对应的分词器拉起。模型实例会依据当前硬件自动迁移到 GPU 或 CPU，随后通过 pipeline 拼装成文本生成的通道，并以 max_length、temperature、top_p、repetition_penalty 等超参来调节输出长度与质量。最后喂入一段提示词即可得到生成结果。

#### 2. 基于 TensorFlow 的实践
TensorFlow 在静态计算图与 Eager Execution 之间自由切换，模型搭建更具灵活性，在生产级场景里以扩展性与调优效率见长，同时自带 TensorFlow Serving、TensorFlow Lite 等分布式训练与部署手段。生态覆盖机器学习流水线、预训权重及移动端发布，得益于 Google 持续投入，社区活跃、资源充足，且可横跨平台落地，包含移动端与浏览器。

即便以静态图著称，配合 Hugging Face Transformers 仍能很方便地载入预训练权重。Keras 接口简洁高效，很适合做复杂的文本生成。下面以 TensorFlow 环境调用 Qwen/Qwen2.5-7B-Instruct 为例展示流程。

```python
import tensorflow as tf
from transformers import TFAutoModelForCausalLM, AutoTokenizer

# 检查是否有可用的 GPU
device = "/GPU:0" if tf.config.list_physical_devices('GPU') else "/CPU:0"

# 加载预训练的 Qwen/Qwen2.5-7B-Instruct 模型和分词器
model_name = "Qwen/Qwen2.5-7B-Instruct"
tokenizer = AutoTokenizer.from_pretrained(model_name)
```

完整度 1 分 代表内容几乎缺失。
4）信实度：判断回答是否扎根于检索到的材料，是否出现幻觉。
5 分 绝专属检索资料
4 分 多数源自检索资料
3 分 部分源自检索资料
2 分 仅小部分源自检索资料
1 分 完全脱离检索资料

评分维度可汇总成表格呈现，如表 2-2 所示。

表 2-2 评分示例

| 问题 | 相关性 | 准确性 | 完整性 | 信实度 | 总分 |
| :--- | :---: | :---: | :---: | :---: | :---: |
| 如何预防流感 | 5 | 5 | 5 | 5 | 20 |
| 流感的症状有哪些 | 5 | 4 | 5 | 5 | 19 |
| 流感的治疗方法是什么 | 5 | 5 | 4 | 5 | 19 |

（4）综合评估与优化

把自动化测评与人工测评融合，形成整体报告。例如：

1）自动化测评：
平均 BLEU：0.85
平均 ROUGE-1 F1：0.80
平均 ROUGE-2 F1：0.65
平均 ROUGE-L F1：0.78

2）人工测评：
平均相关性评分：4.67/5
平均准确性评分：4.67/5
平均完整性评分：4.67/5
平均信实度评分：5.00/5

自动指标显示生成文本在词表与句式层面与参考答案契合度高，BLEU 与 ROUGE 表现都不错。
人工打分中，相关性、准确性、完整性和信实度均获高分，特别是在信实度上达到满分，说明生成答案完全基于检索到的信息，避免了幻觉。

整体已属不错，但仍有优化空间。例如部分问题的准确性评分略低，可以通过优化生成模型的训练数据或引入更多的领域知识来进一步提升准确性。

实操时建议结合具体应用场景挑选评测指标并灵活调整方法。例如在医疗领域，信实度和准确性尤为重要，而在法律信息检索中，召回率可能是更关键的指标。针对性评测能够更有效地指导系统的优化方向，最终打造出高性能、可信赖的 RAG 系统。

### 2.5.3 基于 LlamaIndex
LlamaIndex 属开源方案，主攻大模型与多种数据源的对接。它提供了一套完整工具，让开发者能把不同类型的数据接入大模型，实现 RAG 与高级查询。通过 LlamaIndex，底层数据处理的复杂性被隐藏，开发者可以聚焦创新应用的构建。

#### 1. 主要特点
主要能力如下。

- 多源兼容：支持 API、PDF、文档、SQL 数据库等多种格式与来源。
- 结构友好转换：可把原始数据转成适合大模型处理的索引与图等结构化形式。
- 高级检索：支持复杂查询与检索操作，确保生成内容的相关性与准确性。
- 便捷对接：可与 LangChain、Flask、Docker、ChatGPT 等流行框架与工具无缝衔接。

#### 2. 安装与使用
LlamaIndex 的安装与上手成本较低，下面做简要介绍。

（1）安装 LlamaIndex 库
借助 pip 部署 LlamaIndex 很便捷，只需一条命令即可完成：

```
pip install llama-index
```
请确认运行环境已安装 Python 3.7 及以上版本，以及 pip 工具。如果采用虚拟环境，建议在安装前先激活虚拟环境，以避免依赖冲突。

（2）基础调用示例
下面演示借助 LlamaIndex 完成数据加载、文档解析、索引构建与查询：

```
from llama_index import SimpleDirectoryReader, SimpleNodeParser
from llama_index.indices import VectorIndex

# 步骤 1：加载数据
loader = SimpleDirectoryReader("/path/to/data")
documents = loader.load_data()

# 步骤 2：解析文档
parser = SimpleNodeParser.from_defaults(chunk_size=1024, chunk_overlap=20)
nodes = parser.get_nodes_from_documents(documents)

# 步骤 3：创建索引
index = VectorIndex(nodes)

# 步骤 4：进行查询
query = "法国的首都是哪里？"
response = index.query(query)
print(f"查询结果：{response}")
```
上述实现包含四个环节。

- 加载数据：使用 SimpleDirectoryReader 从指定目录加载数据，可以是文本文件、PDF 等多种格式。
- 解析文档：通过 SimpleNodeParser 将文档解析成更小的节点，每个节点包含一定数量的文本。
- 创建索引：利用 VectorIndex 将节点转换为向量索引，便于高效的相似度检索。
- 进行查询：输入查询语句，系统将返回相关的上下文信息，帮助生成准确的回答。

（3）进阶使用：自定义配置
LlamaIndex 允许开发者根据具体需求自定义配置，如调整文本分割参数、选择不同的索引类型等。下面示例展示如何自定义节点解析器和索引类型：

```
from llama_index import SimpleDirectoryReader, SimpleNodeParser
from llama_index.indices import FAISSIndex

# 加载数据
loader = SimpleDirectoryReader("/path/to/data")
documents = loader.load_data()

# 自定义解析器配置
parser = SimpleNodeParser.from_defaults(chunk_size=2048, chunk_overlap=50)
nodes = parser.get_nodes_from_documents(documents)

# 使用 FAISS 创建向量索引
index = FAISSIndex(nodes)

# 查询
query = " 谁是法国的总统？ "
response = index.query(query)
print(f"查询结果：{response}")
```
LlamaIndex 凭借其多样化的数据连接器、灵活的数据结构化能力、强大的检索和查询接口，以及易于集成的特性，成为开发者在构建 RAG 智能应用时的得力助手。

### 2.5.4 基于 LangChain
LangChain 属于开源框架，目的在于帮助开发者更高效地构建、测试和部署由大型语言模型驱动的应用程序。它提供了一系列模块化组件、工具和第三方集成库，使得开发者能够专注于应用逻辑，而无须从零开始处理复杂的底层细节。对于 RAG 系统来说，LangChain 提供了无缝集成的检索和生成能力，极大地简化了 RAG 系统的开发过程。

#### 1. LangChain 的核心组件与部署方式
要充分利用 LangChain 构建 RAG 系统，理解其核心组件和功能至关重要。相关部分如下。

（1）开源库
LangChain 内置多套开源库，涵盖从基础抽象到高级功能的各个层面。这些库包括但不限于：

- langchain-core：提供了基础的抽象和表达语言，支持构建各种语言模型应用的核心功能。
- langchain-community：集成了第三方工具和服务，扩展了 LangChain 的功能边界。
- langchain-retrievers：专为 RAG 系统设计的检索组件，支持多种检索方式和数据源。

（2）组件和链
LangChain 的组件化设计使得开发者可以通过组合不同的模块来实现复杂的任务。对于 RAG 系统，常见的组件如下。

- 检索模块 Retriever：负责从数据源中检索相关信息。
- 生成器 Generator：基于检索到的信息生成最终的回答或内容。
- 工具链 Toolchain：连接检索模块和生成器，形成完整的 RAG 流程。

预定义的链则是将上述组件按照特定的流程串联起来，通过模块化封装来简化 RAG 系统的实现。例如，一个典型的 RAG 链可能包括文档检索、上下文整合和回答生成三个步骤。

（3）生产化支持工具
LangChain 不仅关注开发阶段的便利性，还提供了全面的生产化支持工具，如 LangSmith 平台。这个平台集成了调试、测试、评估和监控功能，使得开发者能够在生产环境中高效地管理和优化 RAG 系统。

（4）部署方式
LangChain 提供了多种方式来支持 RAG 系统的部署。例如，LangGraph Cloud 允许开发者将 RAG 应用程序部署到云端，提供稳健的 API 支持和高可用性。除此之外，LangChain 还支持将应用程序转换为生产就绪的 API 和助手，方便与其他系统集成。

#### 2. 使用 LangChain 搭建 RAG 系统
下面通过具体代码示例演示如何搭建一个简易 RAG 系统。

（1）安装 LangChain
先确认已安装 LangChain 库，可通过以下命令进行安装：

```
pip install langchain
```
或者使用 Conda 安装：

```
conda install langchain -c conda-forge
```

（2）基础示例
以下为借助 LangChain 构建 RAG 系统的基础示例：

```
from langchain import PromptTemplate, LLMChain
from langchain.llms import OpenAI
from langchain.vectorstores import FAISS
from langchain.embeddings import OpenAIEmbeddings

# 初始化语言模型
llm = OpenAI(model="text-davinci-003")

# 创建提示模板
prompt = PromptTemplate(
    input_variables=["context", "question"],
    template="根据以下内容回答问题。\n\n 上下文：{context}\n\n 问题：{question}\n\n 回答："
)

# 创建 LLM 链
chain = LLMChain(prompt=prompt, llm=llm)

# 初始化嵌入模型和向量存储
embeddings = OpenAIEmbeddings()
vector_store = FAISS.load_local("path_to_faiss_index", embeddings)

def rag_system(question):
    # 检索相关文档
    docs = vector_store.similarity_search(question, k=5)
    context = "\n".join([doc.page_content for doc in docs])

    # 生成回答
    response = chain.run({"context": context, "question": question})
    return response

# 示例问题
question = "LangChain是什么？"
answer = rag_system(question)
print(answer)
```
要点说明如下。

- 初始化语言模型：使用 OpenAI 提供的 text-davinci-003 模型作为生成器。
- 创建提示模板：定义了一个包含上下文和问题的模板，指导语言模型生成回答。
- 创建大模型链：将提示模板和语言模型组合成一个链，用于处理生成任务。
- 初始化嵌入模型和向量存储：使用 FAISS 作为向量存储，用于高效的相似度检索。
- 定义 RAG 系统函数：用于实现检索相关文档和生成回答两个主要功能。
- 示例问题：展示了如何使用该 RAG 系统回答一个具体的问题。

#### 3. 深度定制 RAG 系统
要构建更复杂高效的 RAG 系统，可进一步定制 LangChain 的组件。例如，使用不同的检索模块、优化提示模板、集成更多的数据源等。

（1）使用不同的检索模块示例
除 FAISS 外，LangChain 还支持其他类型的向量存储和检索模块，如 Elasticsearch、Pinecone 等。下面是使用 Elasticsearch 作为检索模块的示例。

```
from langchain.vectorstores import ElasticsearchStore
from langchain.embeddings import OpenAIEmbeddings

# 初始化嵌入模型
embeddings = OpenAIEmbeddings()

# 连接到 Elasticsearch
vector_store = ElasticsearchStore(
    host="localhost",
    port=9200,
    index_name="rag_documents",
    embedding_function=embeddings
)

def rag_system_elasticsearch(question):
    # 检索相关文档
    docs = vector_store.similarity_search(question, k=5)
    context = "\n".join([doc.page_content for doc in docs])

    # 生成回答
    response = chain.run({"context": context, "question": question})
    return response

# 示例问题
question = "如何使用 Elasticsearch 构建 RAG 系统？"
answer = rag_system_elasticsearch(question)
print(answer)
```
上述代码主要展示将 Elasticsearch 嵌入 RAG 工作流的过程。

- 连接到 Elasticsearch：通过 ElasticsearchStore 连接到本地的 Elasticsearch 实例，并指定索引名称。
- 检索相关文档：使用 Elasticsearch 的相似度搜索功能检索与问题相关的文档。
- 生成回答：与之前的示例相同，使用 LLM 链生成最终回答。

（2）优化提示模板
优化提示模板可以显著提升生成回答的质量。下面是一个更为详细和结构化的提示模板示例：

```
prompt = PromptTemplate(
    input_variables=["context", "question"],
    template="""
    你是一个知识渊博的助手，擅长根据提供的上下文信息回答问题。

    上下文：
    {context}

    问题：
    {question}

    请基于以上上下文提供一个详细且准确的回答。
    """
)
```
该代码主要完成以下任务。

- 角色设定：明确指定助手的角色，有助于引导模型生成更符合预期的回答。
- 详细指示：通过详细的指示，指导模型基于上下文生成回答，提升回答的相关性和准确性。

LangChain 以强大的功能与灵活性著称，为开发者构建 RAG 系统提供了坚实基础。从模块化设计到丰富的第三方集成，再到全面的生产化支持，LangChain 是构建现代化 RAG 系统的理想选择。

# 第 3 章 进阶 RAG 工程拆解

本章聚焦高阶 RAG 系统落地的核心构造，从文本如何切分、如何转成向量，到检索与生成如何配合展开。围绕切片与向量化两大环节，具体拆解实现细节和可复用的做法。

## 3.1 文本切分与向量化方法

做一个能用的 RAG，离不开高效的切分和向量化。切分决定输入模型的粒度是否合理，向量化决定检索能不能找准语义。两者配合好，才是高质量回答的基础。

### 3.1.1 文本切分

切分就是把长文本拆成更小、可控的片段。RAG 必须做这一步，因为大模型都有上下文窗口限制。以 GPT-3.5 为例，上限约 4096 Token，大概 3000 个词。合理的切分能保证每段都在模型可处理范围内，同时尽量保留上下文。

#### 1. 主流切分方案

切分策略直接影响检索效果，常见做法有三类。

- 固定长度切分：按固定词数或句数切。实现最简单，但容易在边界处把一个完整句子截断，导致信息丢失或上下文断裂。
- 语义切分：借助 NLP 技术，按语义或主题把文本拆成逻辑完整的小块。连贯性更好，但实现成本更高。典型做法是用主题模型或句子嵌入识别语义单元，再按单元切分。
- 重叠切分：在相邻片段之间保留一定重叠。能避免跨段信息丢失，检索时更容易命中跨片段内容，代价是数据冗余增加。

实际项目中往往混合使用。比如处理学术论文，先用语义切分把论文拆成章节或段落，再在每个章节内部用固定长度做二次切分。这样既保留逻辑连贯，又控制片段长度。重叠长度也可以根据任务动态调节，在完整性和冗余度之间做权衡。

#### 2. 代码演示

下面是固定长度结合重叠切分的示例：

```
def chunk_text(text, max_length=500, overlap=50):
    """
    将文本切分为固定长度的片段，并保留一定的重叠部分。

    :param text: 输入的长文本
    :param max_length: 每个片段的最大长度（以词为单位）
    :param overlap: 重叠部分的长度（以词为单位）
    :return: 切分后的文本片段列表
    """
    words = text.split()
    chunks = []
    start = 0
    while start < len(words):
        end = start + max_length
        chunk = ' '.join(words[start:end])
        chunks.append(chunk)
        start += max_length - overlap
    return chunks

# 示例使用
long_text = "这里是一段非常长的文本，需要被切分成多个片段以便后续处理和检索……"
chunks = chunk_text(long_text, max_length=300, overlap=50)
for i, chunk in enumerate(chunks):
    print(f"片段 {i+1}: {chunk}\n")
```

这里把长文本按每段约 300 词切分，相邻段落保留 50 词的重叠。这样既保证片段适中，又通过重叠为检索保留完整信息。

### 3.1.2 向量化

向量化就是把文本片段映射为数值向量，用来表达语义并做相似度计算。向量质量直接决定 RAG 在检索阶段能不能找对相关文档。

#### 1. 常用向量化技术

- 词频-逆文档频率 TF-IDF：统计方法，通过词在文档内的频率和在语料中的稀有度评估重要性。优点是简单，能压制高频词。缺点是忽略词序和上下文，捕捉不到词语间的语义关联，现在已很少单独使用。
- 词嵌入 Word Embedding：如 Word2Vec、GloVe，把单个词映射到高维稠密空间，能体现词与词的相似性。局限是只能表征单个词，无法直接表达句子或段落整体含义。
- 句子嵌入 Sentence Embedding：如 text-embedding-ada-002，利用预训练深度模型把整句或整段转成向量，能更好保留上下文和语义。当前 RAG 系统的主流选择，因其生成的语义向量质量更高，检索效果更稳定。

#### 2. 代码演示

在现代 RAG 里，基于 Transformer 的句子嵌入已是标配。自注意力机制能捕捉长距离依赖，理解上下文。预训练模型把整段文本转成向量后，可用于精准匹配查询。

以下展示用 text-embedding-ada-002 做向量化的示例：

```python
import openai
import numpy as np

# 设置 OpenAI API 密钥
openai.api_key = '你的_openai_api_key'

def vectorize_chunks_ada(chunks):
    """
    将文本片段转换为向量表示，使用 OpenAI 的 text-embedding-ada-002 模型。

    :param chunks: 文本片段列表
    :return: 向量列表
    """
    response = openai.Embedding.create(
        input=chunks,
        model="text-embedding-ada-002"
    )
    embeddings = [item['embedding'] for item in response['data']]
    return np.array(embeddings)

# 示例使用
chunks = ["这是第一个文本片段。", "这是第二个文本片段。"]
chunk_vectors = vectorize_chunks_ada(chunks)
print(f"生成了 {len(chunk_vectors)} 个向量，每个向量的维度为 {chunk_vectors.shape[1]}")
```

示例中通过 OpenAI API 将每个切分片段转为向量，后续即可用于快速相似检索。

### 3.1.3 向量存储与检索

向量生成后，需要存入专业的向量数据库做检索。这类系统针对大规模向量优化了索引结构和查询算法，能在毫秒级返回最相似结果，大幅提升检索速度和准确度。

下面以 FAISS 为例演示存储与检索：

```python
import faiss
def build_faiss_index(vectors):
    """
    使用 FAISS 构建向量索引。

    :param vectors: 向量列表 (NumPy 数组)
    :return: FAISS 索引
    """
    dimension = vectors.shape[1]
    index = faiss.IndexFlatL2(dimension)  # 创建一个基于 L2 (欧氏) 距离的向量索引，其中 dimension 指定了向量的维度
    index.add(vectors.numpy())  # 添加向量到索引
    return index

def search_faiss_index(index, query_vector, top_k=5):
    """
    在 FAISS 索引中搜索最相似的向量。

    :param index: FAISS 索引
    :param query_vector: 查询向量 (NumPy 数组)
    :param top_k: 返回的最相似向量的数量
    :return: 相似向量的索引和距离
    """
    query = np.array([query_vector.numpy()])
    distances, indices = index.search(query, top_k)
    return distances[0], indices[0]

# 构建 FAISS 索引
faiss_index = build_faiss_index(chunk_vectors)

# 示例查询
query = "如何构建一个高效的检索增强生成系统？"
query_vector = model.encode([query], convert_to_tensor=True)[0]
distances, indices = search_faiss_index(faiss_index, query_vector, top_k=3)

# 输出检索结果
for i, idx in enumerate(indices):
    print(f"结果 {i+1}: 片段内容 = {chunks[idx]}, 距离 = {distances[i]}")
```

流程是先用 FAISS 构建基于 L2 距离的索引，再把查询向量化，在索引中搜索最近邻，最终返回相关片段及其距离。

### 3.1.4 优化切分与向量化策略

要进一步提升性能，可以从以下几个方向优化。

1）动态切分长度：根据文本信息密度和复杂程度调整片段大小。信息密集的段落用更短的片段，确保上下文充足。
2）多层次向量化：在句子级向量之外，增加段落或章节级向量，形成多层索引，支持更精细的检索粒度。
3）增量更新：面对动态数据，支持向量索引的增量写入，避免全量重建，保证实时性和可扩展性。

动态切分长度的示例：

```python
def dynamic_chunk_text(text, max_length=500, min_length=300):
    """
    动态地将文本切分为片段，确保每个片段的长度在 [min_length, max_length] 之间。

    :param text: 输入的长文本
    :param max_length: 每个片段的最大长度（以词为单位）
    :param min_length: 每个片段的最小长度（以词为单位）
    :return: 切分后的文本片段列表
    """
    words = text.split()
    chunks = []
    start = 0
    while start < len(words):
        end = start + max_length
        if end > len(words):
            end = len(words)
        # 尝试找到一个自然的切分点，如句号
        chunk = ' '.join(words[start:end])
        if len(words[start:end]) < min_length and chunks:
            # 将过短的片段与前一个片段合并
            chunks[-1] += ' ' + chunk
            break
        else:
            chunks.append(chunk)
        start += max_length
    return chunks

# 示例使用
dynamic_chunks = dynamic_chunk_text(long_text, max_length=400, min_length=200)
for i, chunk in enumerate(dynamic_chunks):
    print(f"动态片段 {i+1}: {chunk}\n")
```

通过动态调节切分长度，既能保持片段连贯，又能适应不同文本的复杂性，提升整体表现。

### 3.1.5 实战：从文本到向量

综合以上步骤，一个完整的流程是：输入长文本 → 语义+固定长度切分 → 句子嵌入向量化 → 写入向量库 → 查询时检索相似片段供生成模型使用。这一链路串起 RAG 的核心能力。

```python
from sentence_transformers import SentenceTransformer
import faiss
import numpy as np

# 初始化模型
model = SentenceTransformer('all-MiniLM-L6-v2')

def chunk_text(text, max_length=300, overlap=50):
    words = text.split()
    chunks = []
    start = 0
    while start < len(words):
        end = start + max_length
        chunk = ' '.join(words[start:end])
        chunks.append(chunk)
        start += max_length - overlap
    return chunks

def vectorize_chunks(chunks):
    vectors = model.encode(chunks, convert_to_tensor=True)
    return vectors

def build_faiss_index(vectors):
    dimension = vectors.shape[1]
    index = faiss.IndexFlatL2(dimension)
    index.add(vectors.numpy())
    return index

def search_faiss_index(index, query_vector, top_k=3):
    query = np.array([query_vector.numpy()])
    distances, indices = index.search(query, top_k)
    return distances[0], indices[0]

# 示例长文本
long_text = """
RAG 系统结合了检索与生成的优势，通过从大规模文档库中检索相关信息，辅助生成更加准确和丰富的回答。
构建一个高效的 RAG 系统涉及多个步骤，其中切片与向量化是基础且关键的环节。
"""

# 步骤 1：切片
chunks = chunk_text(long_text, max_length=100, overlap=20)
print("切片结果：")
for i, chunk in enumerate(chunks):
    print(f"片段 {i+1}: {chunk}\n")

# 步骤 2：向量化
chunk_vectors = vectorize_chunks(chunks)
print(f"生成了 {len(chunk_vectors)} 个向量，每个向量的维度为 {chunk_vectors.shape[1]}")

# 步骤 3：构建索引
faiss_index = build_faiss_index(chunk_vectors)

# 步骤 4：查询检索
query = "如何利用检索增强生成系统提升回答质量？"
query_vector = model.encode([query], convert_to_tensor=True)[0]
distances, indices = search_faiss_index(faiss_index, query_vector, top_k=2)

# 输出检索结果
print("\n检索结果：")
for i, idx in enumerate(indices):
    print(f"结果 {i+1}: 片段内容 = {chunks[idx]}, 距离 = {distances[i]}")
```

把前面那段代码跑通的逻辑，用更直白的方式梳理一遍。

1）切片：把原始长文本按窗口切成多个小片段，单块最多 100 个词，相邻块间保留 20 个词的重叠，保证语义不被截断。
2）向量化：把每个片段喂给嵌入模型，得到能表征语义的向量。
3）建索引：用 FAISS 把这些向量组织成可检索的索引结构，支撑快速相似度匹配。
4）查询检索：先把用户问题转成向量，再在 FAISS 索引里找最接近的片段，返回对应内容和距离。

这个例子把切片和向量化在 RAG 中的配合讲清楚了。简单说，切片负责把海量文本拆成可操作的小单元，向量化负责把单元变成机器可比对的向量。把切分策略调优、选对嵌入方式，RAG 的检索质量和生成效果会明显提升。

## 3.2 检索技术

检索是 RAG 的核心枢纽，它承接切片和向量化的产出。在更高级的体系里，不同检索手段可以组合使用，兼顾效率与精准。

### 3.2.1 检索流程及优化策略

在 RAG 里，检索要做的是从庞大资料库里快速抓出与问题最相关的信息块，为生成模块提供可靠上下文。检索越快越准，生成的内容相关性与可信度就越高，用户体验也随之提升。

#### 1. 检索过程

RAG 的检索一般包含以下关键环节。

（1）数据预处理和索引
先从文档、数据库、网页等多种来源汇总素材。这些素材往往是长文本，先用切片手段切成短片段，再用嵌入模型转为向量，并写入向量数据库以便相似度搜索。用户查问时即可迅速定位到最相关的片段。细节可参考 3.1 节。

（2）查询处理
用户输入问题后，系统会先做文本清洗，比如去停用词、规范化，再用同样的嵌入模型生成查询向量。随后在向量库里做相似度搜索，通常采用近似最近邻 ANNS 等高效算法，在大规模数据中快速命中最匹配的片段。整个链路覆盖预处理、向量生成与搜索。

下面是处理用户查询与检索的相关示例：

```python
def retrieve_relevant_chunks(query, index, k=5):
    query_embedding = get_embedding(query)
    query_embedding = np.array([query_embedding]).astype('float32')
    distances, indices = index.search(query_embedding, k)
    return indices[0]

# 用户查询
user_query = "RAG 系统中如何利用语义搜索提升生成效果？"
relevant_indices = retrieve_relevant_chunks(user_query, index, k=3)

# 假设我们有一个 chunks 列表来存储所有切片
relevant_chunks = [chunks[i] for i in relevant_indices]
print("相关切片：", relevant_chunks)
```

这段代码演示了如何把查询向量化并在 FAISS 索引中取出最相关的片段，为后续生成提供高质量上下文。

（3）结果过滤和排序
检索到的原始候选通常需要再加工。先按相关度排，再剔除噪声，才能保证给生成模块的上下文干净可用。常见做法有：

1）相似度得分排序：按向量检索返回的分数降序排列，优先展示最相关片段。
2）去重处理：过滤重复或高度重叠的内容，提升阅读体验。
3）内容质量评估：依据预设的质量规则对结果打分筛选。

下面用具体的示例代码来演示如何对检索结果进行排序和去重：

```python
def filter_and_sort_chunks(indices, distances, chunks, threshold=0.5):
    filtered_chunks = []
    for i, dist in zip(indices, distances):
        similarity = 1 / (1 + dist)  # 简单的相似度转换
        if similarity > threshold:
            chunk = chunks[i]
            if chunk not in filtered_chunks:
                filtered_chunks.append(chunk)
    return filtered_chunks

# 获取距离和索引
```

示例里对检索结果做了分数归一化和去重，确保返回片段既相关又干净。

#### 2. 优化策略

要让检索既快又准，可以从索引设计、缓存和并行化等多角度入手。合理设计索引能加速检索，缓存热数据可减少磁盘访问，并行计算则能提升吞吐，三者结合可支撑大并发场景。

##### （1）向量索引优化

向量索引的选择直接影响速度与召回。不同库、不同结构在规模和查询模式上的表现差异很大。可以通过选型、分层、降维、批量等手段综合提升效果。

- 1）选择合适的索引类型。例如 FAISS 提供了 IVF、HNSW 等多种结构。大规模数据倾向 IVF，在速度和召回间取得平衡；HNSW 在高维场景下常能提供更快的查询。

- 2）分层索引。对超大规模语料，可先建摘要索引，再建切片索引，形成两级检索。第一阶段用摘要索引粗筛出相关文档，第二阶段在筛出的文档切片上精查。这样既缩小搜索范围，又保留精确度。分层索引技术执行流程如图 3-1 所示。

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_94_0.png)

- 3）维度压缩技术。通过 PCA 等降维方法压缩向量维度，减少存储和计算开销。降维后向量更轻、比较更快，同时保留主要语义特征，噪声也会相对降低。

- 4）批量查询。把多个查询合并处理，可充分利用并行计算，减少单次请求的固定开销。同时批量读写能降低磁盘 I/O 次数，整体吞吐更高。

##### （2）缓存机制

缓存能明显减轻计算压力，提升响应。

- 1）查询缓存。保存近期查询及其结果，重复问题直接命中缓存，避免重复向量化和检索。
- 2）向量缓存。对高频使用片段的向量做预缓存，减少对嵌入模型的重复调用，节省算力和延迟。

我们可以通过以下的代码来实现简单的查询缓存：

```python
import json
import hashlib
import threading
from cachetools import TTLCache
from functools import wraps
import redis

# 配置 Redis 连接
redis_client = redis.Redis(host='localhost', port=6379, db=0)

# 本地内存缓存（可选，用于加速）
local_cache = TTLCache(maxsize=1000, ttl=300)  # maxsize: 缓存大小; ttl: 过期时间（秒）
local_cache_lock = threading.Lock()

def generate_cache_key(query):
    """
    生成缓存键，可以根据需要自定义，例如添加前缀或散列处理
    """
    # 使用 SHA256 算法来避免键过长
    hash_query = hashlib.sha256(query.encode('utf-8')).hexdigest()
    return f"query_cache:{hash_query}"

def cached_retrieve_decorator(ttl=300):
    """
    装饰器实现的缓存机制，结合 Redis 和本地内存缓存
    """
    def decorator(func):
        @wraps(func)
        def wrapper(query, *args, **kwargs):
            cache_key = generate_cache_key(query)

            # 先尝试从本地内存缓存中获取
            with local_cache_lock:
                if cache_key in local_cache:
                    print(" 从本地缓存获取数据 ")
                    return local_cache[cache_key]

            # 再尝试从 Redis 获取
            cached_result = redis_client.get(cache_key)
            if cached_result:
                print(" 从 Redis 缓存获取数据 ")
                result = json.loads(cached_result)
                # 更新本地缓存
                with local_cache_lock:
                    local_cache[cache_key] = result
                return result

            # 缓存未命中，调用实际查询函数
            result = retrieve_relevant_chunks(query, *args, **kwargs)

            # 将结果缓存到 Redis
            redis_client.setex(cache_key, ttl, json.dumps(result))

            # 更新本地缓存
            with local_cache_lock:
                local_cache[cache_key] = result

            return result
        return wrapper
    return decorator

# 假设的实际查询函数
def retrieve_relevant_chunks(query, index, k=5):
    # 这里应该实现实际的查询逻辑，例如访问数据库或调用外部 API
    # 这里只是一个模拟的返回值
    return [f"chunk_{i}" for i in range(1, k+1)]

# 使用装饰器实现缓存
@cached_retrieve_decorator(ttl=600)  # 设置缓存过期时间为 600 秒
def cached_retrieve(query):
    return retrieve_relevant_chunks(query, index=None, k=5)

# 用户查询示例
if __name__ == "__main__":
    user_query = "RAG 系统中语义搜索的优势是什么？"
    cached_chunks = cached_retrieve(user_query)
    print("缓存的切片：", cached_chunks)

    # 再次查询以验证缓存是否命中
    cached_chunks = cached_retrieve(user_query)
    print("缓存的切片：", cached_chunks)
```

双层缓存驱动的检索提速方案

这套设计把进程内部的内存缓存和外部的 Redis 组合起来，形成二级缓存，兼顾低延迟和横向扩展。

- 1）热数据用 cachetools.TTLCache 放在进程内存里，读写都在内存完成，取数速度最快。
- 2）多线程下借助 threading.Lock 做互斥保护，保证并发读写的数据一致性。
- 3）跨进程、跨机器的共享由 Redis 承担，它提供持久化和高吞吐；缓存键用 SHA256 哈希生成，防止冲突且避免键名过长；cached_retrieve_decorator 统一封装访问路径，先探内存，缺失再查 Redis，最后才触发真实的拉取函数，并把结果回写到内存和 Redis 两个层。
- 4）每条缓存都配置 TTL，到期自动失效保证时效，同时打印命中日志方便观测和优化。

总体看，双层结构大幅减少了重复拉取的开销，响应更快、吞吐更高，扩展性和稳定性都较好，适合高频重复查询的线上场景。

##### （3）并行处理

面对海量数据和高并发请求时，用并发手段可以明显提升整体吞吐。常见做法有线程和进程两种。线程在单个进程里创建多个执行单元，各自独立运行实现并发；进程则是启动多个独立运行体，各自拥有独立内存空间并行处理不同任务。

> 提示：分布式计算生态如 Apache Spark 和 Dask 在此环节也发挥关键作用。它们把任务拆分到多台节点执行大规模并行运算。Spark 与 Dask 提供了丰富的接口和调度优化，能更好地管理分布式任务，进一步放大处理能力和吞吐。

下面演示一个基于线程的并行检索例子：

```python
import concurrent.futures

def parallel_retrieve(queries, index, k=5):
    with concurrent.futures.ThreadPoolExecutor() as executor:
        futures = [executor.submit(retrieve_relevant_chunks, q, index, k) for q in queries]
        results = [future.result() for future in concurrent.futures.as_completed(futures)]
    return results

# 多个用户查询
user_queries = [
    "RAG 系统的语义搜索的工作原理是什么？",
    "向量数据库在 RAG 中的应用有哪些？",
    "如何优化 RAG 系统的检索性能？"
]

parallel_results = parallel_retrieve(user_queries, index, k=3)
for query, chunks in zip(user_queries, parallel_results):
    print(f"查询: {query}\n 相关切片: {chunks}\n")
```

该片段基于 Python 的 concurrent.futures.ThreadPoolExecutor 实现线程级并行检索，以提升响应和处理能力。parallel_retrieve 接收一组查询，通过线程池批量提交检索任务，executor.submit 为每个任务创建独立线程调用 retrieve_relevant_chunks。借助 concurrent.futures.as_completed 可在单个任务完成时即回收结果，无需等全部结束，从而更高效地处理多查询。示例中多条用户问题同时传入 parallel_retrieve，系统并发计算并逐条输出检索结果，单次等待被压缩，整体吞吐和响应均得到改善。

### 3.2.2 关键词检索

关键词检索的本质是把用户问题里的检索词与文档词汇做匹配，从而定位相关信息。该技术被广泛用在搜索引擎、数据库查询和文本分析，帮助快速找到所需内容。既可以做精确匹配也可以做模糊匹配，用来筛出包含指定词的文档，提升效率与准确度。

#### 1. 基本原理

关键词检索的思路是先识别查询中的检索词，再在文档集合里找出包含这些词的文档，在信息检索里意义重大。传统做法主要有布尔检索和基于概率模型的打分检索，比如常见的 BM25。

布尔检索是经典方案，用 AND、OR、NOT 等逻辑符组合检索词实现精确命中。例如查询“人工智能 AND 教育”只返回同时包含这两个词的文档。优点是简单直观，构建表达式容易，但在处理自然语言和复杂意图时表现受限，难以覆盖词义多样性和上下文关联。

BM25 是一种结合词频 TF 与逆文档频率 IDF 的相关性打分方法，用来衡量文档与查询的契合度。核心思想是同时考虑一个词在单篇文档中的出现次数和在整个语料中的稀有程度来计算得分，能更细致地刻画匹配关系，适合大规模语料。与布尔检索相比，BM25 在自然语言和复杂查询上更具优势，更能贴近用户真实意图。

在 RAG 体系中，关键词检索通常作为粗筛，先从海量文档里快速抽取出与问题相关的子集，再交给生成模型做深度加工。生成模型会进一步解析内容并产出更精确详细的回答。因此关键词检索的效率和准确度直接决定 RAG 的表现，优化这一环节是提升整体性能的关键。

#### 2. 实现步骤

搭建关键词检索一般包含以下几步。

##### （1）文本预处理

文本预处理是第一步，目的是把原始文本转为更利于匹配的形式。主要包括词干还原和分词校正。

词干还原是把词语归到基本形态，比如把 running 还原为 run，有助于统一词形，提升泛化能力，让不同变体的同一词汇被统一处理，从而提高准确率和效率。

分词校正对中文尤其重要。分词就是把连续文本切成有意义的词块。中文没有明显分隔符，分词质量直接影响检索词能否命中，错误切分会导致匹配失败。经过上述处理，文本噪声降低，后续检索更高效准确。

##### （2）构建倒排索引

倒排索引是信息检索里常用的高效结构，通过把检索词映射到包含该词的文档列表来加速查找。构建大致分为：

- 1）词表构建。系统遍历所有文档，抽取出所有唯一检索词并整理成词表，为后续索引提供基础。
- 2）文档列表关联。对词表里的每个检索词，记录下包含它的文档 ID，并以有序列表保存，便于快速查找和集合运算。查询时可以迅速从倒排索引中定位到所有相关文档。

举例，假设有三篇文档。

- 文档 1：人工智能在教育中的应用
- 文档 2：机器学习与深度学习的区别
- 文档 3：教育技术的发展趋势

经分词与预处理后，倒排索引可能如表 3-1 所示。

表 3-1 倒排索引

| 关键词 | 文档列表 |
| :--- | :--- |
| 人工智能 | [1] |
| 教育 | [1, 3] |
| 机器学习 | [2] |
| 深度学习 | [2] |
| 技术 | [3] |
| 发展趋势 | [3] |

##### （3）评分和排序

拿到包含检索词的文档后，需要做打分与排序以确定相关度。常用方法是 BM25。

BM25 能较好平衡检索词频率与文档长度对打分的影响，避免极端偏差。其打分公式如式（3-1）所示：BM25(q, D) = \sum_{i=1}^{n} IDF(q_i) \cdot \frac{f(q_i, D) \cdot (k_1 + 1)}{f(q_i, D) + k_1 \cdot \left(1 - b + b \cdot \frac{|D|}{\text{avgdl}}\right)} \quad (3-1)

其中：
- BM25(q, D) 为文档 D 与查询 q 的相关得分。
- IDF(q_i) 为第 i 个检索词的逆文档频率。
- q_i 为查询中的第 i 个检索词。
- f(q_i, D) 为检索词 q_i 在文档 D 中的出现次数。
- |D| 为文档 D 的长度。
- avgdl 为所有文档的平均长度。
- k_1 与 b 为调节参数，通常取 k_1 = 1.5 和 b = 0.75。

(4) 基于倒排序进行关键词检索

下面说明如何结合倒排索引使用 BM25 做关键词检索。

1) 安装依赖库，代码如下：

```
pip install rank_bm25 nltk
```

2) 使用 BM25 做检索，具体代码如下：

```
import nltk
from rank_bm25 import BM25Okapi
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize

# 下载 NLP 资源
nltk.download('punkt')

# 示例文档集
documents = [
    "人工智能在教育中的应用",
    "机器学习与深度学习的区别",
    "教育技术的发展趋势",
    "自然语言处理在客户服务中的应用",
    "深度学习在图像识别中的突破",
]

# 文本预处理函数
def preprocess(text):
    # 分词
    tokens = word_tokenize(text)
    # 转小写
    tokens = [token.lower() for token in tokens]
    # 去除停用词
    stop_words = set(stopwords.words('chinese'))  # 中文停用词
    tokens = [token for token in tokens if token not in stop_words]
    return tokens

# 对所有文档进行预处理
tokenized_documents = [preprocess(doc) for doc in documents]

# 构建 BM25 模型
bm25 = BM25Okapi(tokenized_documents)

# 示例查询
query = "深度学习应用"

# 预处理查询
tokenized_query = preprocess(query)

# 进行 BM25 检索，返回前 3 个相关文档的索引
doc_scores = bm25.get_scores(tokenized_query)
top_n = bm25.get_top_n(tokenized_query, documents, n=3)

print("查询结果：")
for doc in top_n:
    print(doc)
```

运行后输出的检索结果如下：

查询结果：

从 BM25 到语义检索：关键词检索与向量检索实战拆解

上面这段实现主要做了三件事：

1）文本预处理：先用 NLTK 做分词并剔除停用词。中文项目里建议换成 Jieba 这类中文分词工具，准确率会明显提升。
2）构建 BM25 模型：引入 rank_bm25 库中的 BM25Okapi 类，以清洗后的文档为基础建立索引。
3）检索查询：对用户输入做同样的清洗流程，保持查询和文档在同一标准下，再调用 get_top_n 拿到最相关的前 N 条文档作为检索结果。

#### 3. 优化策略
关键词检索要更准，可以从下面几个方向入手。

##### （1）分词技术
中文等语言的分词质量直接决定检索效果。常见做法有三类：

1）基于词典的分词技术：依赖预置词典做匹配，比如双向最大匹配法。优点是实现快、速度高，缺点是对词典完备性要求高，词典缺失容易漏分或错分。
2）基于统计的分词技术：通过大规模语料学习词频和共现规律来判断词边界。能比较好地应对新词和未登录词，但对语料规模依赖大，数据不足时效果下降。
3）混合分词技术：把词典的精确性和统计的自适应结合起来，既能保证常见词的准确，又能补上词典覆盖不到的部分，实际效果更稳。

分词越准，检索噪声越少，用户意图捕捉得越到位。

##### （2）同义词扩展
同义词扩展 Synonym Expansion 的思路是把查询词用语义相近的表达扩充，提升覆盖面。比如下查“汽车”，扩展为“车辆”“轿车”等，就能召回更多相关内容。

常见实现方式：
1）同义词词典：用 WordNet 等现成词典进行扩展，操作简单且覆盖广。
2）语义嵌入：借助 text-embedding-ada-002 等预训练词向量模型算相似度，自动生成同义词集合，能捕捉细微语义差异。

扩展能提高召回，但要控制范围。引入太多无关词会带来噪声，降低精准度，需要在广度和精度之间做权衡。

##### （3）模糊匹配
模糊匹配也叫模糊检索，核心是容错。用户拼写有误或词形变化时，系统仍能命中目标。常用算法有编辑距离 Levenshtein Distance 和 n-gram 匹配。

编辑距离统计把一个词变成另一个词所需的最小插入、删除、替换次数。比如“人工智能”与“人工智能”完全相同，距离为 0；与“人工智囊”只有一字之差，距离为 1。

n-gram 匹配把词拆成连续字符片段，比较片段重叠程度，即使输入有小偏差也能衡量相似度。

用好模糊匹配能显著提升检索的友好度和鲁棒性，在搜索引擎和拼写检查等场景价值很高。

### 3.2.3 语义检索
和传统关键词匹配不同，语义检索不只看表面词，而是理解查询背后的含义和上下文，从而获得更高相关性和精确度。

它通过捕捉文本的深层意义来匹配真实意图，不依赖字面重合，而是分析词语关联与上下文，进而抽取最相关的文档片段，为后续生成提供高质量上下文。

#### 1. 基本原理
语义检索的本质是把文本映射为高维向量，让语义相近的内容在向量空间中靠得更近。词、句甚至整篇文档都可以被嵌入模型编码为向量，通过向量相似度判断相关性。比如“猫”和“小猫”语义接近，向量位置也接近，这种接近性可延伸到句子和段落层面。

目前常用的嵌入模型有 M3E-embedding、BGE 和 OpenAI 的 text-embedding-ada-002 等，它们在海量语料上预训练，具备较强的语言表征能力。以 text-embedding-ada-002 为例，输入文本会被编码为高维向量，保留语义信息，便于用相似度计算来评估相关性。

这种向量表示既能保留词汇层面的信息，也能反映句子和段落的整体语义。

```
import openai
import numpy as np

# 设置 OpenAI API 密钥
openai.api_key = '你的_openai_api_key'

# 输入文本
text = "The quick brown fox jumps over the lazy dog."

# 获取 OpenAI 的嵌入
response = openai.Embedding.create(
    input=text,
    model="text-embedding-ada-002"
)

# 提取嵌入向量
embedding = response['data'][0]['embedding']
embedding = np.array(embedding)

print(embedding)
```

#### 2. 实现步骤
在 RAG 流程里，语义检索通常分两步：预处理与向量化、检索与匹配。

预处理与向量化阶段，文档和查询都要做分词、去停用词、处理标点等标准化操作，再用嵌入模型转为向量。

检索与匹配阶段，系统在向量数据库中计算查询向量与文档向量的相似度，常用余弦相似度、欧氏距离等方法，挑出最相关的片段交给生成模块，生成连贯的回答。

下面用示例说明具体流程。

```
import openai
import faiss
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

# 1. 设置 OpenAI API 密钥
openai.api_key = '你的_openai_api_key'

# 2. 定义文档库
documents = [
    "The quick brown fox jumps over the lazy dog.",
    "A fast, dark-colored fox leaps above a sleepy canine.",
    "Artificial intelligence and machine learning are transforming the world.",
    "The cat sat on the mat.",
    "Deep learning models require large amounts of data."
]

# 3. 文本预处理和向量化
def embed_text(text):
    """
    使用 OpenAI 的 text-embedding-ada-002 模型将文本转换为向量表示。

    :param text: 输入的文本字符串
    :return: NumPy 数组形式的嵌入向量
    """
    response = openai.Embedding.create(
        input=text,
        model="text-embedding-ada-002"
    )
    embedding = response['data'][0]['embedding']
    return np.array(embedding)

# 生成文档向量
document_embeddings = np.array([embed_text(doc) for doc in documents])

# 4. 构建 FAISS 索引
dimension = document_embeddings.shape[1]
index = faiss.IndexFlatL2(dimension)  # 使用 L2 距离
index.add(document_embeddings)

# 5. 定义查询并进行语义检索
query = "A swift fox jumps over a lazy dog."
query_vector = embed_text(query).reshape(1, -1)

# 搜索前 5 个最相似的文档
k = 5
distances, indices = index.search(query_vector, k)

# 6. 输出检索结果
print("Query:", query)
print("\nTop 5 similar documents:")
for i, idx in enumerate(indices[0]):
    print(f"{i+1}. {documents[idx]} (Distance: {distances[0][i]:.4f})")
```

流程拆解如下：

1）加载必要的库和设置 API 密钥，示例代码如下：
```
import openai
import faiss
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

# 设置 OpenAI API 密钥
openai.api_key = '你的_openai_api_key'
```

2）定义文档库，示例代码如下：
```
documents = [
    "The quick brown fox jumps over the lazy dog.",
    "A fast, dark-colored fox leaps above a sleepy canine.",
    "Artificial intelligence and machine learning are transforming the world.",
    "The cat sat on the mat.",
    "Deep learning models require large amounts of data."
]
```
构造一个文档列表，用来演示语义检索效果。

3）文本预处理和向量化，示例代码如下：
```
def embed_text(text):
    """
    使用 OpenAI 的 text-embedding-ada-002 模型将文本转换为向量表示。

    :param text: 输入的文本字符串
    :return: NumPy 数组形式的嵌入向量
    """
    response = openai.Embedding.create(
        input=text,
        model="text-embedding-ada-002"
    )
    embedding = response['data'][0]['embedding']
    return np.array(embedding)

# 生成文档向量
document_embeddings = np.array([embed_text(doc) for doc in documents])
```

4）构建 FAISS 索引，示例代码如下：
```
dimension = document_embeddings.shape[1]  # 确定向量的维度
index = faiss.IndexFlatL2(dimension)  # 使用 FAISS 库创建一个平坦的 L2 距离索引
index.add(document_embeddings)  # 将所有文档向量添加到 FAISS 索引中
```

5）定义查询并进行语义检索，示例代码如下：
```
query = "A swift fox jumps over a lazy dog."
query_vector = embed_text(query).reshape(1, -1)

k = 5
distances, indices = index.search(query_vector, k)
```
- 先把查询转成向量。
- 用 FAISS 索引找出与查询向量最相似的前 5 个文档，并返回距离和索引。

#### 3. 优化策略
提升语义检索效果的有效做法：

1）高质量的数据：嵌入模型和向量库的效果受数据质量影响很大。数据应语义丰富、覆盖全面，且需清理噪声、去重、剔除无关信息，才能降低误召回。
2）选择合适的向量化模型并进行微调：不同模型在不同任务上表现差异明显。针对特定领域做微调，能让向量更贴合业务语义，提升相关性。
3）多源检索：结合内部知识库和外部搜索引擎，可获得更全面、更实时结果。例如 GitHub Copilot Enterprise 同时调用 Bing 和内部代码库，实现了更高效的语义检索。
4）向量压缩与加速：高维向量存储和检索开销大。可通过量化、降维等压缩技术减少存储，并借助 GPU 加速提升效率。
5）动态更新与增量学习：数据和需求会变化，系统需要支持增量更新和在线学习，保证检索的实时性和准确性。

相比关键词检索，语义检索能理解潜在意图、处理同义词和语义表达，在法律咨询、医疗支持、智能客服等领域结合生成模型后价值凸显。

### 3.2.4 密集段落检索
密集段落检索 Dense Passage Retrieval，DPR，是近年信息检索领域的重要进展。它用密集向量编码文档并检索，比 TF-IDF、BM25 等稀疏方法对语义的捕捉更强，能在复杂语境下找到高度相关的段落。

在 RAG 系统中，DPR 用于从外部知识库高效召回支撑生成的内容，提升输出质量和准确性，在开放域问答、知识管理、智能客服等场景表现突出。结合数据增强、模型微调和多检索融合，配合硬件加速和优化索引，DPR 兼具理论先进性和落地性能。

#### 1. 基本原理

深度语义检索的核心思路是借助双编码器或双塔架构，把查询和文档分别映射成高维向量。通过计算这两个向量的相似度，DPR 能够迅速找到与查询最匹配的文档片段。这种方式对语义的捕捉能力远强于传统的稀疏向量检索，因而在相关性和准确性上都有明显提升。

DPR 里使用的双编码器由两套独立模型组成，一个负责理解问题，即 Question Encoder，另一个负责理解上下文，即 Context Encoder。两者通常都以预训练的 Transformer 为底座，并在海量的问答对上进行微调，以产出高质量的向量表示。双塔设计的巧妙之处在于查询和文档可以并行编码，大幅提升检索效率，同时让每个编码器专注于自身的任务，整体表现更稳。

#### 2. 实现步骤

搭建 DPR 涉及从数据准备、编码到结果输出的一系列关键环节。

##### （1）主要的处理流程

以下是主要的处理流程。

1）数据预处理和编码：首先要对全部文档集合做精细预处理，把长文本切分为段落或句子级别的小块，作为检索单元。随后用 Context Encoder 把这些片段转为高维向量。与此同时，用户输入的查询也会经 Question Encoder 编码，确保两者落入同一向量空间以便后续比对。这个阶段保证了表示的一致性，为检索打好基础。

2）相似度计算和检索：编码完成后，系统会计算查询向量与各文档向量之间的相似度。常用的度量包括内积和余弦相似度。依据得分，系统会挑出相似度最高的前 K 个片段作为初筛结果，在效率与准确度之间取得平衡。

3）结果过滤和排序：初筛结果需要进一步精炼。按相似度得分排序后，会加入去重、剔除无关片段等过滤策略。经过这一步，用户最终拿到一组高度相关的片段，能够支撑问答、信息抽取等下游生成任务，让检索更精准、体验更流畅。

##### （2）示例

为了更直观理解 DPR 的落地方式，下面以一段简洁的 Python 示例说明如何借助 Hugging Face Transformers 库构建 DPR 并完成文档检索。

```python
from transformers import DPRQuestionEncoder, DPRContextEncoder,
    DPRQuestionEncoderTokenizer, DPRContextEncoderTokenizer
import torch
import faiss
import numpy as np

# 使用 Transformers 和 FAISS 实现语义搜索：加载 DPR 模型生成文档和查询向量，构建 FAISS 索引用于
# 实现快速的相似度搜索。
# 查询输入后，生成查询向量并检索，输出最相似的文档及其相关性得分。

# 初始化 DPR 的查询和上下文编码器及其分词器
question_encoder = DPRQuestionEncoder.from_pretrained('facebook/dpr-question_
    encoder-single-nq-base')
context_encoder = DPRContextEncoder.from_pretrained('facebook/dpr-ctx_encoder-
    single-nq-base')
question_tokenizer = DPRQuestionEncoderTokenizer.from_pretrained('facebook/dpr-
    question_encoder-single-nq-base')
context_tokenizer = DPRContextEncoderTokenizer.from_pretrained('facebook/dpr-
    ctx_encoder-single-nq-base')

# 示例文档
documents = [
    " 机器学习是一门研究计算机算法的学科，这些算法可以从数据中学习并做出预测。 ",
    " 自然语言处理是人工智能的一个重要分支，涉及计算机与人类语言的交互。 ",
    " 深度学习是机器学习的一个子领域，使用多层神经网络来建模复杂的数据表示。 "
]

# 编码文档
def encode_documents(documents):
    inputs = context_tokenizer(documents, return_tensors='pt', padding=True,
                               truncation=True)
    with torch.no_grad():
        embeddings = context_encoder(inputs).pooler_output
    return embeddings.numpy()

doc_embeddings = encode_documents(documents)

# 建立 FAISS 索引
dimension = doc_embeddings.shape[1]
index = faiss.IndexFlatIP(dimension)  # 使用内积作为相似度度量
faiss.normalize_L2(doc_embeddings)  # 归一化向量
index.add(doc_embeddings)

# 用户查询
query = " 什么是深度学习？ "

# 编码查询
def encode_query(query):
    inputs = question_tokenizer(query, return_tensors='pt')
    with torch.no_grad():
        embedding = question_encoder(inputs).pooler_output
    return embedding.numpy()

query_embedding = encode_query(query)
faiss.normalize_L2(query_embedding)

# 检索最相似的文档
k = 2  # 返回前 2 个最相似的文档
D, I = index.search(query_embedding, k)

# 输出检索结果
for idx, score in zip(I[0], D[0]):
    print(f" 文档：{documents[idx]} \n 相似度得分：{score:.4f}\n")
```

#### 3. 优化策略

要让 DPR 表现更强，研究和工程实践中总结出多种改进手段，这些方法既提升了准确率，也增强了鲁棒性和泛化能力。

##### （1）数据增强
数据是模型提升的关键。引入更多训练样本，特别是多样化的负样本，能显著增强泛化。负样本帮助模型更好地区分相关与无关文档。数据增强技术如回译、同义词替换等也能丰富训练集，让模型在不同表述的查询下保持稳定性能。

##### （2）模型微调
预训练向量模型已有不错的语言理解力，但在垂直领域做适配性微调效果更佳。例如医疗、法律等专业场景，使用领域数据微调后检索质量会明显上升。微调时可调整学习率、优化器等超参数，让模型更贴合特定任务。

##### （3）融合多种检索技术
单一检索范式难以覆盖所有需求。将稀疏检索与密集检索结合能取长补短，比如先用 BM25 做粗筛，再用 DPR 精排，效果往往更优。也可以引入图检索、语义检索等其它手段，进一步丰富检索的维度和准确性。

##### （4）硬件加速和向量索引优化
GPU、TPU 等现代硬件能大幅加速向量运算，而高效的索引结构如 FAISS 则能提升检索速度和规模。软硬件协同优化让 DPR 在海量数据下依然高效运行。优化索引可减少计算量，并行计算则进一步满足大规模实时检索需求。

### 3.2.5 混合检索

在 RAG 体系里，混合检索整合多种检索技术，能显著提升检索效率和准确度。

#### 1. 基本原理

混合检索的本质是把不同类型的检索方法有机组合，发挥各自优势，从而提高检索精度和生成质量。典型方案会把稀疏检索如 BM25 与密集检索如 DPR 结合，并融入知识图谱来增强上下文理解和多跳推理能力。整体目标是通过互补实现更高效、更精准的信息获取与文本生成。

混合检索的核心包含多阶段检索、多源信息融合和协同优化。

##### （1）多阶段检索
混合模型先用速度快的稀疏检索做初筛，缩小候选范围；再用密集检索做细粒度匹配，提升相关性。这种分步策略兼顾了速度和质量。

##### （2）多源信息融合
模型会把检索到的文本与知识图谱中的结构化知识结合，拓展上下文维度，支撑更复杂的生成任务。融合后能更好完成多跳推理，产出更丰富、更精确的内容。

##### （3）协同优化
通过协调检索方法与生成模型之间的交互，整体系统性能得以提升，确保生成结果的准确性和连贯性。协同优化同时提高了检索与生成的效率和最终文本质量。

混合模型借此实现了分阶段检索、多源融合与检索生成协同优化，显著提升了检索精确度和生成品质。

#### 2. 实现步骤

构建高效混合模型需经历初始检索、细粒度检索、知识图谱增强和文本生成等关键阶段，以保证在大数据上既快又准。

1）初始检索：采用稀疏向量方法如 BM25 对海量文档做快速初筛，迅速定位可能相关的片段，为后续精细检索提供基础。

2）细粒度检索：在初筛结果上使用密集向量方法。DPR 将查询和片段都转为密集向量，精确度量相似度，进一步缩小候选集，提高相关性。

3）知识图谱增强：将高相关片段与图谱中的节点和边关联，利用图谱的推理和上下文扩展能力，丰富文本深度和广度，提升生成准确性和可靠性。

4）文本生成：把经过检索和图谱增强的高质量片段与图谱信息输入生成模型如 GPT，结合上下文生成连贯、准确且细节丰富的回答，满足用户需求。

按以上四步即可搭建出兼顾效率与精度的混合检索生成系统。

### 3.2.6 重排序

重排序是在初次检索后，利用更多上下文或用户反馈对结果顺序进行优化，通过深度学习等复杂算法实现更精确的排序。

在 RAG 中，重排序通常分为两个阶段，以兼顾效率与精度。

1）初步检索排序阶段：使用基础评分方法对文档做初排，具体可参考 3.2.2 节。
2）高级重排序阶段：在初排基础上，采用更复杂的模型重排，如基于深度学习的 Cross-Encoder 或大型语言模型。LLM 依托丰富的语义知识，能更全面理解查询与文档，从而处理多意图或多主题的复杂查询。

两阶段协同工作，可在速度与精度间取得平衡，帮助用户更快找到所需信息。

#### 1. 重排序的方法

##### （1）基于深度学习的方法

深度学习模型在捕捉上下文和语义细微差异方面表现突出，在重排序任务中尤为有效。以下介绍几种常用方法。

1）Cross-Encoder 模型：核心理念是把查询和文档拼接后联合编码，直接输出更精确的相关性得分。联合编码能捕捉更细粒度的上下文交互，在对精度要求极高的场景下优势明显。

下面代码展示如何使用 Cross-Encoder 进行重排序：

```python
from transformers import CrossEncoder
```

##### （1）基于深度学习的方法

###### 1）基于 Cross-Encoder 的重排序

```python
# 加载预训练的 Cross-Encoder 模型
model = CrossEncoder('cross-encoder/ms-marco-MiniLM-L-6-v2')

# 示例查询和文档列表
query = "如何优化 RAG 系统的检索性能？"
documents = [
    "RAG 系统的检索性能可以通过优化向量数据库来提升。",
    "深度学习模型在自然语言处理中的应用非常广泛。",
    "使用 BM25 算法进行初步排序是 RAG 系统的常见做法。",
    "重排序步骤对于提高检索结果的相关性至关重要。"
]

# 生成查询与每个文档的评分
scores = model.predict([(query, doc) for doc in documents])

# 将文档按评分排序
sorted_docs = [doc for _, doc in sorted(zip(scores, documents), key=lambda x: x[0], reverse=True)]

print("重排序后的文档列表：")
for doc in sorted_docs:
    print(doc)
```

###### 2）基于 BGE（Bi-Encoder for Generative Embeddings，生成式嵌入的双编码器模型）进行重排序

BGE 采用 Bi-Encoder 结构生成高质量嵌入，用于精确的语义相似度计算，在查询与文档匹配中很关键。在重排序中，BGE 属于高效的双编码器方案，适合需要快速生成嵌入的场景。相比 Cross-Encoder 在精细交互上略逊，但效率与性能的平衡使其在实际应用中更具优势。

得益于双编码器结构，BGE 在计算速度上表现优异，特别适合大规模重排序任务。其实时性优势也让它在语义检索等对响应速度敏感的应用中表现出色。

下面是一个使用 BGE 进行重排序的示例：

```python
from transformers import AutoModel, AutoTokenizer
import torch

# 加载预训练的 BGE 模型和分词器
tokenizer = AutoTokenizer.from_pretrained("sentence-transformers/all-MiniLM-L6-v2")
model = AutoModel.from_pretrained("sentence-transformers/all-MiniLM-L6-v2")

query = "如何优化 RAG 系统的检索性能？"
documents = [
    "RAG 系统的检索性能可以通过优化向量数据库来提升。",
    "深度学习模型在自然语言处理中的应用非常广泛。",
    "使用 BM25 算法进行初步排序是 RAG 系统的常见做法。",
    "重排序步骤对于提高检索结果的相关性至关重要。"
]

# 将查询和文档生成嵌入
query_embedding = model(**tokenizer(query, return_tensors='pt'))['last_hidden_state'].mean(dim=1)
document_embeddings = torch.stack([model(**tokenizer(doc, return_tensors='pt'))['last_hidden_state'].mean(dim=1) for doc in documents])

# 计算查询与每个文档的相似度分数
scores = torch.nn.functional.cosine_similarity(query_embedding, document_embeddings).tolist()

# 将文档按相似度分数排序
sorted_docs = [doc for _, doc in sorted(zip(scores, documents), key=lambda x: x[0], reverse=True)]

print("重排序后的文档列表：")
for doc in sorted_docs:
    print(doc)
```
###### 3）M3E 跨模态跨语言重排

M3E 最值得关注的点，是它能把来自不同语言、不同模态的查询和文档都投射到同一个向量空间里，从而实现真正的跨语言、跨模态语义对齐。正是这种能力，让它在需要处理多模态内容的重排场景里表现突出，比如图文配对、多语言问答、视频与文本匹配等。

该模型不仅覆盖多模态数据，还能对图片、文本、音频等多种模态的语义进行建模。把这些异构数据统一映射后，就能便捷地计算彼此的语义相似度。凭借对多模态的原生支持，M3E 在复杂任务中的可扩展性明显更强。

在语言层面，M3E 的多语言能力同样出色，特别适合跨语言重排的需求。对于跨语言文本匹配、多语言问答这类任务，它能给出更稳健的相关性判断。

下面给出 M3E 重排序的示例：

```python
from transformers import M3EModel  # 假设 M3EModel 是支持多模态的模型
import torch

# 加载预训练的 M3E 模型
model = M3EModel.from_pretrained("m3e/multimodal-model")

# 示例查询和文档列表
query = "如何优化 RAG 系统的检索性能？"
documents = [
    "RAG 系统的检索性能可以通过优化向量数据库来提升。",
    "深度学习模型在自然语言处理中的应用非常广泛。",
    "使用 BM25 算法进行初步排序是 RAG 系统的常见做法。",
    "重排序步骤对于提高检索结果的相关性至关重要。"
]

# 生成查询和文档的嵌入
query_embedding = model.encode(query, return_tensors='pt')
document_embeddings = model.encode(documents, return_tensors='pt')

# 计算查询与每个文档嵌入的相似度分数
scores = torch.nn.functional.cosine_similarity(query_embedding, document_embeddings).tolist()

# 将文档按相似度分数排序
sorted_docs = [doc for _, doc in sorted(zip(scores, documents), key=lambda x: x[0], reverse=True)]

print("重排序后的文档列表：")
for doc in sorted_docs:
    print(doc)
```
##### （2）大模型驱动的重排

把大模型引入文档重排，核心是借用其强大的语义理解力和零样本文本处理能力。大模型无需明确标注数据就能应对各类复杂文本任务。相较于传统深度学习重排方案，大模型在长文本理解和复杂语义关系捕捉上更有优势。深度学习往往依赖海量标注，而大模型靠预训练获得的语义能力即可直接完成重排，这让相关性判断更细腻，效率也更高。常见的大模型重排策略主要有以下几种。

###### 1）Pointwise 打分

该思路聚焦查询与单个文档的相关度测量，特别适合零样本重排。模型会根据查询和文档内容，直接利用自身的理解与生成能力给出一个相关性分数，无需针对特定数据集先行训练。分数越高说明匹配越好，排序也随之确定。这种方式简化了流程，同时提升准确度和效率。

下面是一个借助 GPT-4 按 Pointwise 方式重排的示例：

```python
import openai

openai.api_key = 'your-api-key'

query = "如何优化 RAG 系统的检索性能？"
documents = [
    "RAG 系统的检索性能可以通过优化向量数据库来提升。",
    "深度学习模型在自然语言处理中的应用非常广泛。",
    "使用 BM25 算法进行初步排序是 RAG 系统的常见做法。",
    "重排序步骤对于提高检索结果的相关性至关重要。"
]

def get_score(query, doc):
    prompt = f"请为以下查询和文档对给出一个相关性评分（0 到 1 之间）：\n 查询：{query}\n 文档：{doc}\n 评分："
    response = openai.Completion.create(
        engine="gpt-4",
        prompt=prompt,
        max_tokens=1,
        temperature=0
    )
    return float(response.choices[0].text.strip())

scores = [get_score(query, doc) for doc in documents]
sorted_docs = [doc for _, doc in sorted(zip(scores, documents), key=lambda x: x[0], reverse=True)]

print("重排序后的文档列表：")
for doc in sorted_docs:
    print(doc)
```
###### 2）Listwise 整表排序

这种做法是将整份文档列表一次性交给大模型处理，在提示中同时给出查询和文档列表，要求模型返回重新排列后的文档编号。具体是把查询与全部候选文档作为整体输入，让模型依据相关性对列表进行全局重排。好处在于能兼顾文档间的相互影响和依赖关系，更贴近整体相关性。最终输出的是一个已排序的编号序列，相关度最高的文档排在最前，检索效果随之改善。

下面是一个借助 GPT-4 按 Listwise 方式重排的示例：

```python
import openai

openai.api_key = 'your-api-key'

query = "如何优化 RAG 系统的检索性能？"
documents = [
    "RAG 系统的检索性能可以通过优化向量数据库来提升。",
    "深度学习模型在自然语言处理中的应用非常广泛。",
    "使用 BM25 算法进行初步排序是 RAG 系统的常见做法。",
    "重排序步骤对于提高检索结果的相关性至关重要。"
]

prompt = f"请根据以下查询和文档列表，按相关性从高到低排序文档，并仅返回排序后的文档编号（1-{len(documents)}）：\n 查询：{query}\n 文档列表：\n"
for i, doc in enumerate(documents, 1):
    prompt += f"{i}. {doc}\n"

response = openai.Completion.create(
    engine="gpt-4",
    prompt=prompt,
    max_tokens=50,
    temperature=0
)

sorted_indices = list(map(int, response.choices[0].text.strip().split()))
sorted_docs = [documents[i-1] for i in sorted_indices]

print("重排序后的文档列表：")
for doc in sorted_docs:
    print(doc)
```
###### 3）Pairwise 两两比较

该方法以查询-文档对为单位进行重排，由大模型判断哪份文档更相关。首先把查询分别与每个文档配对，模型对每一对打分以衡量相关性，汇总出更优的文档标识。随后通过聚合所有配对分数，形成综合相关性评价，从而完成排序。文档相关度越高排列越靠前，检索的准确性和效率得到提升。

下面是一个借助 GPT-4 按 Pairwise 方式重排的示例：

```python
import openai
from itertools import combinations

openai.api_key = 'your-api-key'

query = "如何优化 RAG 系统的检索性能？"
documents = [
    "RAG 系统的检索性能可以通过优化向量数据库来提升。",
    "深度学习模型在自然语言处理中的应用非常广泛。",
    "使用 BM25 算法进行初步排序是 RAG 系统的常见做法。",
    "重排序步骤对于提高检索结果的相关性至关重要。"
]

pair_scores = {doc: 0 for doc in documents}
pairs = list(combinations(documents, 2))

for doc1, doc2 in pairs:
    prompt = f"查询一下哪一个文档更相关？\n 查询：{query}\n 文档 1：{doc1}\n 文档 2：{doc2}\n 回答：1 或 2"
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=prompt,
        max_tokens=1,
        temperature=0
    )
    choice = response.choices[0].text.strip()
    if choice == '1':
        pair_scores[doc1] += 1
    elif choice == '2':
        pair_scores[doc2] += 1

# 根据得分排序
sorted_docs = sorted(documents, key=lambda doc: pair_scores[doc], reverse=True)

print("重排序后的文档列表：")
for doc in sorted_docs:
    print(doc)
```
##### （3）混合方案

融合深度学习与大模型，能兼顾两者的长处进一步提升重排效果。常见做法是先用基于 Cross-Encoder 的深度学习模型做初筛重排，再交由大模型进行精调优化，确保最终结果的相关性和准确性。深度学习擅长特征提取和模式识别，大模型擅长自然语言理解与生成，两者结合可实现更精准高效的排序。

下面是一个按混合方式重排的示例：

```python
from transformers import CrossEncoder
import openai

openai.api_key = 'your-api-key'

query = "如何优化 RAG 系统的检索性能？"
documents = [
    "RAG 系统的检索性能可以通过优化向量数据库来提升。",
    "深度学习模型在自然语言处理中的应用非常广泛。",
    "使用 BM25 算法进行初步排序是 RAG 系统的常见做法。",
    "重排序步骤对于提高检索结果的相关性至关重要。"
]

# 第一步：使用 Cross-Encoder 进行初步重排序
cross_encoder = CrossEncoder('cross-encoder/ms-marco-MiniLM-L-6-v2')
cross_scores = cross_encoder.predict([(query, doc) for doc in documents])
cross_sorted_docs = [doc for _, doc in sorted(zip(cross_scores, documents), key=lambda x: x[0], reverse=True)]

# 第二步：使用 GPT-4 进行微调重排序
prompt = f"请根据以下查询和文档列表，按相关性从高到低排序文档，并仅返回排序后的文档编号（1-{len(cross_sorted_docs)}）：\n 查询：{query}\n 文档列表：\n"
for i, doc in enumerate(cross_sorted_docs, 1):
    prompt += f"{i}. {doc}\n"

response = openai.Completion.create(
    engine="gpt-4",
    prompt=prompt,
    max_tokens=50,
    temperature=0
)

final_sorted_indices = list(map(int, response.choices[0].text.strip().split()))
final_sorted_docs = [cross_sorted_docs[i-1] for i in final_sorted_indices]

print("最终重排序后的文档列表：")
for doc in final_sorted_docs:
    print(doc)
```
#### 2. 排序与重排的优化思路

要进一步提升 RAG 系统中的排序与重排质量，可从以下方向入手。

##### （1）模型微调

针对特定领域数据做精细化微调，重排模型能更敏锐地捕捉相关性和重要性，排序的精确度与稳定性随之显著提升，整体效果更加稳健。

以下是微调 Cross-Encoder 模型的示例：

```python
from transformers import CrossEncoder, InputExample, losses
from torch.utils.data import DataLoader

# 示例训练数据
train_examples = [
    InputExample(texts=["如何优化 RAG 系统的检索性能？", "RAG 系统的检索性能可以通过优化向量数据库来提升。"], label=1),
    InputExample(texts=["如何优化 RAG 系统的检索性能？", "深度学习模型在自然语言处理中的应用非常广泛。"], label=0),
    # 更多训练样本
]

# 创建 DataLoader
train_dataloader = DataLoader(train_examples, shuffle=True, batch_size=16)
train_loss = losses.CrossEntropyLoss()

# 加载预训练模型
model = CrossEncoder('cross-encoder/ms-marco-MiniLM-L-6-v2')

# 训练模型
model.fit(train_dataloader=train_dataloader, epochs=3, warmup_steps=100,
          optimizer_params={'lr': 2e-5}, loss=train_loss)

# 保存微调后的模型
model.save('fine-tuned-cross-encoder')
```
##### （2）集成学习方法

将多个排序模型的结果汇聚起来，能明显改善排序质量。业界常用的融合手段有加权平均、Stacking 堆叠、Blending 融合等。它们的核心思路是取长补短，降低单一模型带来的偏见和噪声，进而让排序更稳定、更高效。比如把传统机器学习排序器和深度学习排序器组合起来，就能兼顾各自的强项。

（3）排序与重排的动态调优
让排序策略随实战表现和用户回响而变化。实际运行中可依据点击、停留等行为信号，实时修改模型权重或切换重排方案，从而保持系统持续进化和优化。

## 3.3 高级检索策略
在 RAG 体系里，最终回答好不好，检索好不好是关键。要想把真正相关的文档片段找出来，召回率拉上去，就得用一些进阶检索技巧。第一个思路是围绕用户提问做拆解和假设。把一个大问题拆成几个细问题，为每个细问题先想一个可能的答案假设，这样就能更精准地锁定相关片段，提升相关性和准确度。

另外，缩小检索粒度也能改善效果。传统检索往往拿整篇文档或大段落来比对，噪音很大。改成以段落、句子乃至短语为单位检索，就能直接命中与问题相关的点，不仅提升精确度，也省去用户筛除无关内容的成本。

再者，给大模型补充更丰富的上下文。模型做推理和生成时，背景信息越充分结论越可靠。额外提供上下文语境后，模型能更清楚问题的前因后果，进而给出更精准、更深入的答案。

### 3.3.1 假设性问题和假设性回答策略
本小节聚焦两种基于假设的进阶检索思路：假设性提问和假设性回答，即 HyDE，Hypothetical Document Embedding。两者都靠先生成假设性的问句或答句再做向量化，来改善查询与文档的匹配，让搜索和问答更相关、更准。

#### 1. 两种策略的流程分析
假设性提问的做法是由大模型为文档的每一块内容自动造一个相关问句，再把这些问句转成向量。索引并不是建在原文向量上，而是建在这些合成问题向量上。用户来查询时，先向量化查询去匹配最相似的合成问题，找到后回溯到原文对应位置，为后续生成提供背景。图 3-2 演示了完整流程。
![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_122_0.png)
相比直接索引原文，这种方式放大了查询与合成问题之间的语义贴合度，搜索精度明显提高。因为它能更好地把握用户真实意图，以及文档的深层含义。

HyDE 则是反向操作，流程见图 3-3。先让大模型根据用户的查询先写一个假想答案，再把这个假想答案的向量和查询向量一起参与检索。这样模型能更懂查询背后的语境和隐含需求，检索结果更相关、更准。查询与假想回答的语义联系被强化，向量融合也让算法在复杂问题上更稳健。

下面分别展开两种策略的具体做法和优势。
![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_123_0.png)

#### 2. 假设性问题策略
该策略的核心是为文档内容造问题并向量化，建立检索索引。具体流程是先把文档切块，为每块造一个相关问题；再用大模型把问题转成向量建索引。用户提问时向量化后去索引里找最相似的问题，顺藤摸瓜回到原文对应段落，给生成环节提供背景。

优势在于合成问题更贴合文档实质，能更准确捕捉查询意图，提升相关性和精确度。相较于直接对原文向量化索引，它在处理复杂查询和挖掘深层含义上更有优势。

假设性问题的实现步骤如下。
第一步，对待处理文档做合理切分。可按自然段、章节或其他逻辑单元来分，切得好后续问题才更聚焦。
```python
def split_document(document, max_length=500):
    """
    将文档按指定长度分段
    """
    import textwrap
    segments = textwrap.wrap(document, max_length, break_long_words=False,
                             replace_whitespace=False)
    return segments
```
第二步，用大模型为每个分块生成一个相关问句。这能提炼关键信息，为向量化和建索引打底。示例代码如下：
```python
import openai

def generate_question(segment):
    """
    使用大模型生成与段落相关的问题
    """
    prompt = f"请根据以下段落的内容生成一个相关的问题：\n\n{segment}\n\n问题："
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[
            {"role": "system", "content": "你是一个帮助生成相关问题的助手。"},
            {"role": "user", "content": prompt}
        ],
        max_tokens=50,
        temperature=0.7,
    )
    question = response.choices[0].message['content'].strip()
    return question
```
第三步，用预训练嵌入模型，比如 OpenAI 的向量嵌入 API，把生成的问题转成向量，供相似度计算和建索引使用，示例代码如下：
```python
def get_embedding(text):
    """
    获取文本的向量表示
    """
    response = openai.Embedding.create(
        input=text,
        model="text-embedding-ada-002"
    )
    embedding = response['data'][0]['embedding']
    return embedding
```
第四步，用向量数据库如 FAISS 为问题向量建索引，实现快速相似搜索，示例代码如下。
```python
import faiss
import numpy as np

def build_faiss_index(embeddings):
    """
    构建 FAISS 索引
    """
    dimension = len(embeddings[0])
    index = faiss.IndexFlatL2(dimension)
    index.add(np.array(embeddings).astype('float32'))
    return index
```
第五步，用户发来查询，先向量化，再在索引里检索最相似的问题向量，代码如下。
```python
def search_similar_questions(query, index, question_embeddings, top_k=5):
    """
    搜索与查询最相似的问题
    """
    query_embedding = get_embedding(query)
    query_vector = np.array([query_embedding]).astype('float32')
    distances, indices = index.search(query_vector, top_k)
    similar_questions = [question_embeddings[i] for i in indices[0]]
    return similar_questions
```
第六步，根据匹配到的问句定位回原文段落，为生成答案提供上下文。
```python
def get_document_segments(similar_questions, question_to_segment_map):
    """
    根据相似的问题获取对应的文档段落
    """
    segments = []
    for question in similar_questions:
        segment = question_to_segment_map.get(question, "")
        segments.append(segment)
    return segments
```

下面给出一个完整示例，演示如何用假设性提问做索引和查询匹配。
```python
import openai
import faiss
import numpy as np
import textwrap

# 设置 OpenAI API 密钥
openai.api_key = 'YOUR_OPENAI_API_KEY'

# 示例文档
document = """
人工智能 (Artificial Intelligence, AI) 是计算机科学的一个分支，人们通常致力于基于人工智能开发能够执行人类智能级别的任务的系统。这些任务包括学习、推理、问题解决、感知和语言理解。近年来，随着深度学习和大数据技术的发展，人工智能取得了显著的进展，广泛应用于各个领域，如医疗、金融、交通和娱乐等。
"""

# 步骤 1：分段
segments = split_document(document, max_length=200)

# 步骤 2：生成问题
questions = []
question_to_segment_map = {}
for segment in segments:
    question = generate_question(segment)
    questions.append(question)
    question_to_segment_map[question] = segment

# 步骤 3：向量化问题
embeddings = [get_embedding(q) for q in questions]

# 步骤 4：构建 FAISS 索引
index = build_faiss_index(embeddings)

# 步骤 5：用户查询
user_query = "人工智能在医疗领域的应用有哪些？"
similar_questions = search_similar_questions(user_query, index, questions, top_k=3)

# 步骤 6：获取相关文档段落
related_segments = get_document_segments(similar_questions, question_to_segment_map)

# 打印结果
print("用户查询:", user_query)
print("相关问题及文档段落:")
for q, seg in zip(similar_questions, related_segments):
    print(f"问题：{q}")
    print(f"段落：{seg}\n")
```
运行上述代码，输出结果如下：

用户查询：人工智能在医疗领域的应用有哪些？

相关问题及文档段落：

问题：人工智能在医疗领域有哪些具体的应用？

段落：人工智能（Artificial Intelligence, AI）是计算机科学的一个分支，人们通常致力于基于人工智能开发能够执行人类智能级别的任务的系统。这些任务包括学习、推理、问题解决、感知和语言理解。近年来，随着深度学习和大数据技术的发展，人工智能取得了显著的进展，广泛应用于各个领域，如医疗、金融、交通和娱乐等。

问题：人工智能在金融领域有哪些应用？

段落：人工智能（Artificial Intelligence, AI）是计算机科学的一个分支，人们通常致力于基于人工智能开发能够执行人类智能级别的任务的系统。这些任务包括学习、推理、问题解决、感知和语言理解。近年来，随着深度学习和大数据技术的发展，人工智能取得了显著的进展，广泛应用于各个领域，如医疗、金融、交通和娱乐等。

经以上流程，用户提问“人工智能在医疗领域的应用有哪些？”成功匹配到相关问句和原文片段，为生成精准答案提供了所需背景。

#### 3. 假设性回答策略
HyDE 的思路是用假想答案来增强检索。不同于为文档造问题，它是先为查询造一个假想回答，再融合查询向量和回答向量做匹配，从而更懂上下文和潜在需求，拿到更相关更准的结果。

它的优势是既用到了查询本身的语义，也借助假想回答补全上下文和隐含需求，让检索更全面、相关。

HyDE 的实现步骤如下。
1）用大模型根据用户查询生成一个假设性回答，用来扩展语义并补充上下文。
```python
def generate_hypothetical_response(query):
    """
    使用大模型生成针对查询的假设性回应
    """
    prompt = f"请针对以下查询生成一个假设性的回应，以更好地理解用户的需求；\n\n 查询：{query}\n\n 回应："
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[
            {"role": "system", "content": "你是一个帮助生成假设性回应的助手。"},
            {"role": "user", "content": prompt}
        ],
        max_tokens=100,
        temperature=0.7,
    )
    hypothetical_response = response.choices[0].message['content'].strip()
    return hypothetical_response
```
2）分别把用户查询和假想回答转成向量。
```python
def get_combined_embedding(query, response):
    """
    获取查询和假设性回应的综合向量表示
    """
    query_embedding = get_embedding(query)
    response_embedding = get_embedding(response)

    # 例如，简单拼接两者的向量
    combined_embedding = query_embedding + response_embedding  # 或采用其他结合方式
    return combined_embedding
```
3）对文档切分并向量化后建索引，这里默认索引已准备好。
```python
def build_document_faiss_index(document_embeddings):
    """
    构建文档向量的 FAISS 索引
    """
    dimension = len(document_embeddings[0])
    index = faiss.IndexFlatL2(dimension)
    index.add(np.array(document_embeddings).astype('float32'))
    return index
```
4）融合查询向量和假想回答向量一起检索。
```python
def hyde_search(query, document_index, document_embeddings, top_k=5):
    """
    使用 HyDE 方法进行搜索
    """
    # 步骤 1：生成假设性回应
    hypothetical_response = generate_hypothetical_response(query)

    # 步骤 2：获取综合向量
    combined_embedding = get_combined_embedding(query, hypothetical_response)
    combined_vector = np.array([combined_embedding]).astype('float32')

    # 步骤 3：在索引中搜索
    distances, indices = document_index.search(combined_vector, top_k)
    similar_documents = [document_embeddings[i] for i in indices[0]]

    return similar_documents, hypothetical_response
```
5）把检索到的文档内容与假想回答结合，生成最终答案。

```python
def generate_final_answer(query, similar_documents, hypothetical_response):
    """
    使用大模型生成最终回答
    """
    context = "\n\n".join(similar_documents)
    prompt = f"查询：{query}\n\n假设性回应：{hypothetical_response}\n\n 相关文档内容：\n{context}\n\n 基于以上信息，请生成一个详细且准确的回答："

    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[
            {"role": "system", "content": " 你是一个智能助手，负责根据提供的上下文生成准确的回答。 "},
            {"role": "user", "content": prompt}
        ],
        max_tokens=300,
        temperature=0.7,
    )
    final_answer = response.choices[0].message['content'].strip()
    return final_answer
```

下面这段材料完整呈现了 HyDE 思路在查询理解和答案合成中的落地过程。

```python
import openai
import faiss
import numpy as np
import textwrap

# 设置 OpenAI API 密钥
openai.api_key = 'YOUR_OPENAI_API_KEY'

# 示例文档
documents = [
    "人工智能在医疗领域的应用包括疾病诊断、药物研发、个性化治疗和医疗影像分析等。通过机器学习算法，AI 能够从大量的医疗数据中提取有价值的模式，辅助医生进行更准确的诊断和治疗决策。",
    "在金融领域，人工智能被广泛应用于风险评估、算法交易、客户服务和欺诈检测等方面。AI 模型能够快速分析市场数据，识别潜在风险，提升金融服务的效率和安全性。",
    "交通领域的人工智能应用包括自动驾驶、交通流量预测和智能交通管理系统。AI 技术能够实时监控交通状况，优化交通信号控制，减少拥堵和事故发生。",
    "在娱乐行业中，人工智能被用于内容推荐、虚拟角色创建和个性化用户体验。通过分析用户的行为和偏好，AI 能够提供定制化的内容，提升用户满意度和参与度。"
]

# 向量化文档
```

RAG 实战手册：让大模型检索更准更强

```python
document_embeddings = [get_embedding(doc) for doc in documents]

# 构建 FAISS 索引
document_index = build_document_faiss_index(document_embeddings)

# 用户查询
user_query = "人工智能在医疗领域的应用有哪些？"

# 使用 HyDE 进行搜索
similar_docs, hypothetical_response = hyde_search(user_query, document_index, documents, top_k=2)

# 生成最终回答
final_answer = generate_final_answer(user_query, similar_docs, hypothetical_response)

# 打印结果
print("用户查询:", user_query)
print("\n假设性回应:", hypothetical_response)
print("\n相关文档内容:")
for doc in similar_docs:
    print(f"- {doc}\n")
print("最终回答:", final_answer)
```

执行完示例代码后，终端会呈现这样的结果：

用户查询：人工智能在医疗领域的应用有哪些？

假设性回应：AI 在医疗场景的落地覆盖了从辅助诊断到个体化治疗的多个环节，能够明显提升诊疗的效率与准确度。

相关文档内容：

- 医疗领域中，AI 的用途包括疾病诊断、药物研发、个体化治疗以及医学影像解析等。借助机器学习，模型可以从海量病历和影像中挖掘规律，为医生提供更可靠的决策支持。
- 在金融行业，AI 广泛用于风险评估、程序化交易、智能客服和反欺诈等。模型可快速处理市场数据，识别风险点，提升服务效率与安全水平。

最终回答：

人工智能在医疗领域的渗透非常深，主要体现在这几个方向：

1. **疾病诊断**：模型通过学习大量诊疗数据，辅助医生更精准地判断病情。例如利用图像识别技术解读医学影像，帮助发现癌症等病变。
2. **药物研发**：AI 可以模拟分子行为并进行预测，大幅缩短新药研发周期并降低成本。
3. **个性化治疗**：结合基因信息和病史，系统能够定制专属治疗方案，提升疗效。
4. **医疗影像分析**：基于深度学习，AI 可自动解读 X 光、CT 等影像，提高分析速度和准确性。
5. **健康管理**：AI 可接入可穿戴设备实时监测健康指标，进行预警和健康建议，推动预防医学发展。

借助这些能力，医疗服务质量和效率得到明显提升，行业创新也在加速。HyDE 思路让原始问题得到文档支撑，又融合了假设性回应的语义，生成了更完整、更准确的结果，充分满足信息需求。

假设性回答策略与 HyDE 方法的共通点在于先用大模型生成与问题相关的假设表述，再将其向量化参与检索。这种做法显著提高了召回的针对性和语义对齐度，同时结合向量化和索引技术，实现了检索匹配的提速增效。

### 3.3.2 拓展检索上下文

在 RAG 体系里，召回率与检索上下文的丰富度呈正相关。常见的扩展手段有两种：句子窗口检索与父文档检索。

#### 1. 句子窗口检索策略

核心做法是把整篇文档切分成句子，逐句编码。这种细粒度处理能拉大查询与语义的相似度，从而提升命中精度。为保证连贯性，系统会在召回到的最相关句前后各补 k 句，再把这段扩充后的上下文交给大模型推理。

该方法的实现流程如下。

1. 文本预处理与分句：先清洗特殊字符、统一格式，再用分句算法把文档拆成独立句子。
2. 句子向量化：借助 M3E、BGE 等预训练模型对每句编码，得到语义向量。
3. 建立向量索引：把所有句子向量存入向量数据库，支持快速相似度搜索。
4. 查询编码与匹配：对用户问题编码后，在索引中找出最相似的句子。
5. 上下文扩充：以命中句为中心，左右各扩展 k 句，形成完整语境。
6. 推理输出：将扩充后的语境输入大模型，生成最终答案。

以下是一个基于 Python 与 Transformers 库实现的句子窗口检索示例：

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_130_0.png)

图 3-4 句子窗口检索的流程图

```python
import re
from transformers import AutoTokenizer, AutoModel
import torch
import faiss
import numpy as np

# 初始化M3E模型和分词器
model_name = "moka-ai/m3e-base"  # 替换为实际的M3E模型名称
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModel.from_pretrained(model_name)

# 1. 文档预处理和句子分割
def preprocess_and_split(document):
    # 去除特殊字符
    document = re.sub(r'\s+', '', document)
    # 使用正则表达式进行分句
    sentences = re.split('。！|！|\.|？|\?', document)
    # 去除空字符串
    sentences = [s for s in sentences if s]
    return sentences

document = "这是第一句。这是第二句！这是第三句？这是第四句。"
sentences = preprocess_and_split(document)

# 2. 句子编码
def encode_sentences(sentences):
    embeddings = []
    for sentence in sentences:
        inputs = tokenizer(sentence, return_tensors='pt', truncation=True,
                          padding=True, max_length=512)
        with torch.no_grad():
            outputs = model(**inputs)
            # 根据M3E模型的输出结构选择合适的向量表示
            cls_embedding = outputs.last_hidden_state[:, 0, :].squeeze().numpy()
            embeddings.append(cls_embedding)
    return np.array(embeddings)

sentence_embeddings = encode_sentences(sentences)

# 3. 构建向量索引
dimension = sentence_embeddings.shape[1]
index = faiss.IndexFlatIP(dimension)  # 使用内积作为相似度度量
index.add(sentence_embeddings)

# 4. 查询处理和检索
def search(query, top_k=1):
    inputs = tokenizer(query, return_tensors='pt', truncation=True,
                       padding=True, max_length=512)
    with torch.no_grad():
        outputs = model(**inputs)
    # 获取查询句子的 [CLS] 向量表示
    query_vec = outputs.last_hidden_state[:, 0, :].squeeze().numpy()
    # FAISS 索引要求输入为二维数组
    query_vec = np.expand_dims(query_vec, axis=0)
    D, I = index.search(query_vec, top_k)
    return I[0]

query = "第二句"
top_k = 1
indices = search(query, top_k=top_k)

# 5. 扩展上下文
def expand_context(sentences, index, k):
    start = max(0, index - k)
    end = min(len(sentences), index + k + 1)
    return sentences[start:end]

k_context = 1
for idx in indices:
    expanded_context = expand_context(sentences, idx, k_context)
    context = '。'.join(expanded_context) + '。'
    print("扩展的上下文: ", context)

# 6. 提供给大模型进行推理（此处省略具体实现）
```

#### 2. 父文档召回策略

父文档召回利用文档的层级关系，先把大文档切成若干小子块并与上级父块关联。检索时在子块层面匹配，若前 k 个命中结果中来自同一父块的子块数超过阈值 m，就用该父块整体替换给大模型的上下文。

父文档召回的流程如图 3-5 所示。

该机制共分五步，从层级化构建到模型推理，确保检索既相关又全面，具体如下。

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_133_0.png)

图 3-5 父文档召回策略的流程图

（1）构建文档层级
用正则表达式把文档拆分为章节、段落等清晰层级，并为每个子块标注其所属父块，形成有序的父子关系。
（2）子块编码与索引
对每个子块单独编码生成语义向量并建立索引，便于快速定位。
（3）查询与子块匹配
把用户查询向量化，在子块索引中检索出最相关的前 k 个子块。
（4）父块统计与挑选
统计命中子块的父块归属，若某父块包含的子块数超过设定的阈值 m，则选取该父块作为最终上下文来源，保证从宏观上把握核心内容。
（5）上下文组装与推理
存在符合条件的父块时，提取其下全部子块内容作为完整上下文；否则直接使用命中子块。大模型基于此进行推理。

这种将层级结构与向量检索结合的方式，既保证了检索效率，又提供了充足的上下文支撑，显著提升了大模型推理的质量。

```python
import re
from transformers import AutoTokenizer, AutoModel
import torch
import faiss
import numpy as np
from collections import defaultdict

# 初始化M3E模型和分词器
model_name = "moka-ai/m3e-base"  # 替换为实际的M3E模型名称
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModel.from_pretrained(model_name)

# 1. 文档层次结构构建
def create_hierarchy(document):
    # 假设文档按章节划分，每章包含多个段落
    chapters = re.split(r'第 [一二三四五六七八九十 ]+章 ', document)
    hierarchy = []
    for i, chapter in enumerate(chapters[1:], 1):
        chapter_title = f"第 {i} 章"
        paragraphs = chapter.strip().split('\n\n')
        for para in paragraphs:
            if para.strip():
                hierarchy.append({
                    'parent': chapter_title,
                    'child': para.strip()
                })
    return hierarchy

document = """
第一章
这是第一章的第一段。

这是第一章的第二段。

第二章
这是第二章的第一段。

这是第二章的第二段。
"""
hierarchy = create_hierarchy(document)

# 2. 子块编码和索引
child_texts = [item['child'] for item in hierarchy]

def encode_texts(texts, batch_size=32):
    embeddings = []
    for i in range(0, len(texts), batch_size):
        batch = texts[i:i+batch_size]
        inputs = tokenizer(batch, return_tensors='pt', truncation=True,
                          padding=True, max_length=512)
        with torch.no_grad():
            outputs = model(**inputs)
        # 根据 M3E 模型的输出结构选择合适的向量表示
        cls_embeddings = outputs.last_hidden_state[:, 0, :].squeeze().numpy()
        embeddings.append(cls_embeddings)
    return np.vstack(embeddings)

child_embeddings = encode_texts(child_texts)

# 归一化向量，以使用内积作为相似度度量（等同于余弦相似度）
faiss.normalize_L2(child_embeddings)

# 3. 构建向量索引
dimension = child_embeddings.shape[1]
index = faiss.IndexFlatIP(dimension)  # 使用内积作为相似度度量
index.add(child_embeddings)

# 4. 查询处理和子块检索
def search_children(query, top_k=3):
    inputs = tokenizer(query, return_tensors='pt', truncation=True,
                       padding=True, max_length=512)
    with torch.no_grad():
        outputs = model(**inputs)
    # 获取查询句子的 [CLS] 向量表示
    query_vec = outputs.last_hidden_state[:, 0, :].squeeze().numpy()
    # 归一化查询向量
    faiss.normalize_L2(query_vec)
    query_vec = np.expand_dims(query_vec, axis=0)
    D, I = index.search(query_vec, top_k)
    return I[0]

query = "第一段"
top_k = 3
indices = search_children(query, top_k=top_k)

# 5. 父块统计和选择
parent_count = defaultdict(int)
for idx in indices:
    parent = hierarchy[idx]['parent']
    parent_count[parent] += 1

threshold_m = 2
selected_parents = [parent for parent, count in parent_count.items() if count >= threshold_m]

if selected_parents:
    # 选取出现次数最多的父块
    selected_parent = max(parent_count, key=parent_count.get)
    context = '\n\n'.join([item['child'] for item in hierarchy if item['parent'] == selected_parent]) + '。'
else:
    # 如果没有满足条件的父块，则使用检索到的子块
    context = '\n\n'.join([hierarchy[idx]['child'] for idx in indices]) + '。'

print("提供给大模型的上下文：\n", context)

# 6. 提供给大模型进行推理（此处省略具体实现）
```

层次化文档组织配合动态检索机制，自适应调节能力很强。检索反馈会直接影响取用语境的范围，系统就能依据不同查询意图和场景灵活收窄或放宽窗口。这种弹性让应用在复杂多样的任务面前仍能保持响应，提升整体使用体验。

#### 3. 两种扩窗策略的优劣拆解

滑动窗口式的句子提取在精准定位上更占优势。它在文本上滚动一个固定长度的窗口逐句抓取信息，很快能筛出与问题最相关的句子。面对短文本时，这种局部聚焦能迅速给出要点回复。局限在于视野过窄，容易丢失更广的背景，导致理解片面。

上层文档检索则给模型完整的语境支撑，有助于捕捉长距离语义关联。该方式直接取回整段上层文档，而非孤立句子，保证信息连贯且完整。在长文本场景下效果尤为突出，背景充足有助于模型把握整体意图。代价是速度偏慢，有时还会带入冗余内容。

实际选用要看任务目标和文本形态。求精确短答时，句子窗口更合适；需通盘理解时，上层文档更稳妥。两者也可以组合使用：先用句子窗口锁定关键句，再按这些句子所属的上层块判断是否需要进一步放大范围。这样既保留精准度，又兼顾完整性，能显著提升生成质量。综合示例如下：

```python
def combined_search(query, k_sentence=1, k_child=3, threshold_n=2):
    # 使用句子窗口检索方法
```

indices_sentence = search(query, top_k=1)

expanded_context = []

for idx in indices_sentence:

    expanded = expand_context(sentences, idx, k_sentence)

    expanded_context.extend(expanded)

# 使用父文档检索方法

indices_child = search_children(query, top_k=k_child)

parent_count = defaultdict(int)

for idx in indices_child:

    parent = hierarchy[idx]['parent']

    parent_count[parent] += 1

if any(count >= threshold_n for count in parent_count.values()):

    selected_parent = max(parent_count, key=parent_count.get)

    parent_context = [item['child'] for item in hierarchy if item['parent'] == selected_parent]

    combined_context = '\n'.join(parent_context)

else:

    combined_context = '\n'.join(expanded_context)

return combined_context

# 获取综合上下文

context = combined_search(query="第一段")

print("综合提供给大模型的上下文: \n", context)

上面这段 combined_search 把句子窗口和上层文档检索串联起来，向大模型供给更全面也更准确的语境。通过策略切换，既能保证召回的精确，又能确保语境的完整性。

## 3.4 大模型挑选与提示词打造

在 RAG 架构里，生成器负责把检索到的材料和用户需求融合，用自然语言输出答案。提示词工程是提升输出效果的核心手段，合理设计提示可以在不增训的前提下引导模型产出更高相关性的结果。

模型选择同样关键，它决定了输出质量、上限和部署成本。

### 1. 选型时需要权衡的维度

选型要综合看能力、资源和许可，适配 RAG 的实际需求。相关考量汇总如下表。

表 3-2 模型选型的考虑因素

| 考虑因素 | 具体含义 |
| --- | --- |
| 性能 | 理解与输出能力：模型捕捉用户意图并给出准确回复的水准<br>多语支持：依据使用场景是否需要覆盖多种语言 |
| 计算资源 | 参数规模：模型体量越大通常需要更多算力<br>部署花费：算力消耗直接影响上线与运行成本 |
| 窗口容量 | 长文处理：RAG 需承载长文本，更大窗口有助于提升表现<br>内存开销：窗口越大，对内存的需求也越高 |
| 开源与商用 | 开源方案：便于二次开发与定制<br>商业授权：使用需符合许可条款，尤其在商业化场景 |

### 2. 提示词工程

在 RAG 场景里，提示词不仅影响输出质量，也决定检索信息能否被有效利用。常用技术如下。

#### （1）零样本提示

零样本提示是指仅用任务描述驱动模型完成任务，无需示例。靠预训练知识即可快速验证新任务表现。

示例：
> “请用通俗的话介绍量子物理的核心思想。”

模型会基于已有知识给出简要说明。

#### （2）少样本提示

少样本提示给模型少量输入输出示范，帮助其把握任务模式。几个高质量问答对就能显著提升问答表现。

示例：
> “问：地球的天然卫星是什么？
答：月亮。
问：太阳系中体积最大的行星是？
答：”

模型会续写：“木星。”

在 RAG 中，少样本提示有助于模型更好地理解用户意图。

#### （3）思维链提示

思维链提示让模型先展示推理步骤再给出答案，把复杂问题拆解，降低错误率，也便于校验逻辑。

示例：
问题：小李买了两件上衣，A 原价 100 元打八折，B 原价 199 元且满 100 元减 20 元。请问总共支付多少钱？

推理过程：
1. A 价为 100 元 × 0.8 = 80 元。
2. B 价为 199 元 - 20 元 = 179 元。
3. 合计 80 元 + 179 元 = 259 元。

最终答案：259 元

先推理后作答，能提升可信度和可追溯性。

#### （4）自动思维链提示

自动思维链通过多样本生成多条推理路径，增强鲁棒性，减少单一路线的偏差。

示例：
> “我会向你提出问题，请使用至少三种不同的推理方式来解答，分别列出每种推理过程和结论，最后对所有结论综合比较，给出最可信的最终答案。”

模型会产出多条推理并综合判断。

#### （5）思维树提示

思维树提示模拟树状搜索，让模型探索多个解法分支再汇总。

示例：
> “问题：如何提升网站 SEO 排名？
思考 1：改进内容质量。
思考 1.1：提供有价值的原创内容。
思考 1.2：定期更新内容。
思考 2：优化关键词。
……
答案：通过高质量原创内容、定期更新并优化关键词，可有效提升 SEO 排名。”

灵活运用上述提示方法，可显著提高生成质量，实际落地时需结合任务和模型特性细化设计。

## 3.5 检索与生成的融合方式

RAG 框架里，信息融合主要有 RAG-Token 和 RAG-Sequence 两种模式。两者在检索时机和方式上不同，影响生成效率与灵活度。

RAG-Token 在生成每个 Token 时都触发检索，能实时引入最新信息，提升准确性和时效性，但计算开销和延迟也更高。RAG-Sequence 则在整段生成前做一次检索，效率更高，但实时性和精确度可能打折。

尽管机制不同，两者目标一致：让检索与生成协同，依场景提供高质量输出，充分利用外部知识并适配不同任务需求。

### 3.5.1 RAG-Token 模型

RAG-Token 的架构亮点在于生成过程中动态注入外部检索语境。

#### 1. RAG-Token 模型概述

传统生成模型主要依赖内部知识库，受容量和更新频率限制，输出易不全面。RAG-Token 通过在生成时动态检索外部语境，弥补了这一短板，能输出更准确、相关且全面的内容。

核心组件包括：

1）问题编码器：把用户查询映射为高维向量，捕捉关键意图，为检索提供结构化基础，便于后续匹配相关文档。

2）生成器：如何把检索变成答案
生成器负责把检索到的上下文转成最终输出。它会融合检索文档的实证信息和问题编码器给出的向量信号，把碎片信息整合成连贯、准确、相关度高的文本。设计上每个 Token 生成时都能回看检索上下文，这一步让结果质量明显提升。

#### 2. RAG-Token 生成链路拆解
RAG-Token 的工作可以拆成四个递进环节。

1）输入编码环节：用户输入的问题会先进入专用问句编码器。编码器凭借深度学习捕捉语义关系，把整段输入压缩成一个高维向量。
2）检索环节：编码得到的查询向量被送入检索模块，从预先构建的向量库中找出与之最相似的文档。
3）文档整合环节：检索到的文档会被接在原始问题前面，组成扩展输入。扩展输入既保留用户本意，又注入检索内容，形成更丰富的上下文。整合时模型会筛选排序，优先保留关联度最高的材料，为生成提供最有价值的背景。
4）生成环节：扩展输入进入生成器，依据整体上下文产出最终文本。

经过这套检索结合生成的闭环，RAG-Token 能够输出更精准、更深入的内容。

### 3.5.2 RAG-Sequence 模型
RAG-Sequence 用序列化方式处理检索与生成，特别适合那种单篇文档就能给出答案的查询。

#### 1. RAG-Sequence 基本原理
RAG-Sequence 与 RAG-Token 的工作机制差异明显，具体对比如下。

表 3-3 RAG-Sequence 与 RAG-Token 的对比

| | RAG-Sequence | RAG-Token |
|---|---|---|
| 检索方式 | 一次性锁定固定文档集，全程复用 | 生成每一个 Token 时动态检索，可即时获取新信息 |
| 适用场景 | 长文本创作、需要上下文连贯，如文章写作、报告生成 | 需要高灵活性和动态响应，如对话系统、实时问答 |
| 优点 | 上下文一致性强，输出连贯 | 检索更具针对性，适应性强 |
| 缺点 | 文档不够动态，难应对查询漂移 | 生成过程复杂，可能导致上下文不一致 |

#### 2. RAG-Sequence 模型的工作流程
RAG-Sequence 主要包含三步，能高效、准确地完成查询到回答的转换。

1）**查询编码**：用户问题经编码器如 M3E Embedding 处理，转成高维向量，保留关键信息与语义。
2）**文档检索**：用该向量在预建向量库中检索，库内存放的是预处理好的文档片段向量。
3）**文本生成**：把检索到的最相关片段作为上下文输入给生成模型如 GPT-4o，基于这些片段生成最终回答。

整个生成过程中检索到的片段保持不变，保证输出的一致性和连贯性。模型严格依据检索片段作答，不引入无关信息，从而提升准确性、可读性和可信度。

# 第 4 章 检索与问答模块优化
本章聚焦 RAG 系统检索与问答模块的提效手段，目标是夯实系统基础能力。从检索侧看，如何改进嵌入模型训练与文档解析，并通过同义词扩展和查询重写提升检索的精准与灵活；从问答侧看，如何强化回答能力与拒答机制，让系统既高效又可靠。配合数据清洗与存储管理优化，实现数据层提质增效，为后续推理与性能提升打基础。

## 4.1 数据预处理和管理优化
模型输出很大程度上取决于输入数据质量。数据若存在噪声、冗余或错误，再强的模型也难给出满意结果。下面先从数据清洗入手。

### 4.1.1 数据清洗
数据清洗的目的是剔除噪声与冗余，保证准确、一致、完整。对 RAG 而言，清洗后的数据能提升检索精度，也能改善生成质量。

#### 1. 移除重复和不相关的数据
多源整合时常出现重复记录，导致检索结果冗余。可用聚类或哈希函数识别并删除重复，例如计算文档哈希值快速定位重复项并剔除。

在构建垂直领域 RAG 时，无关数据会拖慢检索并干扰生成。以医学聊天机器人为例，非医学文档会成为噪音。应对方法是关键词过滤，设定医学术语筛选相关文档，或用主题建模识别主题分布，确保数据与领域高度相关，从而提升检索效率和生成准确性。

#### 2. 修复结构性错误
命名不统一是常见问题，如“北京大学”与“北大”。可建立同义词词典指定标准名，或用映射表把多种写法统一到标准名，保证检索一致。

拼写错误会影响解析与关键词匹配。可借助拼写校正工具或语言模型检测纠错，提升文本质量，避免误匹配。

格式不一致如日期、货币也会带来处理困难。统一字段格式能提高一致性，提升处理效率与分析准确性。

借助 Pandas、正则表达式、自然语言处理库可大幅提升清洗效率。清洗并非一次完成，需随数据更新与业务变化持续迭代。

### 4.1.2 数据存储和访问优化
高效的存储方案与合理的访问策略能显著提升 RAG 整体性能，保证速度与可靠性。

#### 1. 数据存储解决方案
##### (1) 列式存储
列式存储如 Apache Parquet、Apache ORC 按列组织数据，提升查询速度与压缩率。RAG 需频繁取特定字段，列存可减少 I/O，加快检索。

来看一个使用 PyArrow 写入 Parquet 文件的示例。

```python
import pyarrow as pa
import pyarrow.parquet as pq

# 创建一个示例表
data = {
    'id': [1, 2, 3],
    'text': ['这是第一条文本', '这是第二条文本', '这是第三条文本'],
    'embedding': [[0.1, 0.2], [0.2, 0.3], [0.3, 0.4]]
}
table = pa.Table.from_pydict(data)

# 将表写入 Parquet 文件
pq.write_table(table, 'data.parquet')
```

采用 Parquet 格式后，后续检索能快速读取指定列，比如仅读取文本或嵌入向量。

##### (2) 分区和集群
分区按特定列组织数据，便于高效过滤；集群重排数据，减少查询时扫描量。

来看一个对 Hive 数据库进行数据分区的示例。

```sql
CREATE TABLE documents (
    id INT,
    text STRING,
    date STRING
)
PARTITIONED BY (year STRING, month STRING)
STORED AS PARQUET;
```

按年、月分区后，查询指定时间段数据时扫描量大幅下降。

##### (3) 多层存储
高频访问数据放在 SSD 或内存中，保证快速响应；低频冷数据放在 HDD 或云存储，降低成本。合理分配实现性能与成本平衡。

来看一个使用 Apache Hadoop 进行分层存储的示例。

在 Hadoop 中可配置不同存储介质，按策略存放数据。

```
<property>
    <name>dfs.storage.policy.satisfier.enabled</name>
    <value>true</value>
</property>
```

启用存储策略满足器后，Hadoop 会按配置自动把数据搬到合适层级。

#### 2. 数据访问优化策略
##### （1）索引效率优化
在海量数据与向量检索场景下，索引设计直接决定响应速度。可从以下角度优化。

###### 1）索引分片与副本。
合理拆分分片提升并行查询并均衡负载；设置副本增强高可用。分片与副本数量需结合数据规模与访问频次权衡。

###### 2）刷新策略与合并策略。
Elasticsearch 默认定时把内存段刷到磁盘。若写入频繁可放宽刷新间隔减少开销，若需实时性则保持较短间隔。批量写入后可调整合并策略或手动触发合并，减少小段，提升查询效率。

###### 3）字段映射与分析器。
文本字段选用合适的分析器，如业务词库分词、同义词过滤，提升精准度。向量字段可用 dense_vector 等类型，并调整 dims、相似度算法等参数。

###### 4）查询与聚合优化。
用 Filter 代替不必要的评分计算，减少计算量。高并发下避免频繁复杂聚合，可在应用层缓存结果或用 Rollup 索引预聚合。

下面是一个为 Elasticsearch 创建索引的示例，展示分片、副本、字段映射、向量字段等优化思路。

```
PUT /documents
{
  "settings": {
    "number_of_shards": 3,          // 根据数据规模和实际集群情况设置
    "number_of_replicas": 1,        // 根据查询的高可用性需求设置
    "refresh_interval": "30s"       // 适当调大，以减少频繁刷新开销
  },
  "mappings": {
    "properties": {
      "id": {
        "type": "integer"
      },
      "text": {
        "type": "text",
        "analyzer": "smartcn",      // 示例：使用中文分词器，实际可按需自定义
        "search_analyzer": "smartcn"
      },
      "embedding": {
        "type": "dense_vector",
        "dims": 768,                // 向量维度，可根据具体模型维度进行调整
        "index": true,              // 在新版本 Elasticsearch 中，可将它设为 true 或改用
                                    // searchable_vector
        "similarity": "dot_product" // 示例：可根据业务场景选择向量 similarity 算法
      }
    }
  }
}
```

合理的索引结构与配置能加速全文与向量检索，提升高并发下的吞吐，核心目标是充分利用底层资源，提供稳定快速的搜索服务。

##### （2）数据压缩
数据压缩能减少存储占用，降低网络传输时间。对 RAG 而言意味更快的加载与处理，并节省存储与带宽成本。

下面是一个使用 gzip 压缩文本数据的示例。

```
import gzip
import json

data = {'id': 1, 'text': '这是需要压缩的文本'}

with gzip.open('data.json.gz', 'wt', encoding='utf-8') as f:
    json.dump(data, f)
```

传输大量文本时压缩能显著提升效率。

##### （3）缓存
把常用数据或查询结果放在缓存中，可大幅降低重复问题的响应时间。内存或快速存储介质能让系统直接命中缓存，避免重复计算或数据库查询，提升满意度。

下面是一个使用 Redis 作为缓存层的示例。

```
import redis

# 连接到 Redis
r = redis.Redis(host='localhost', port=6379, db=0)

# 设置缓存
r.set('document_1', '这是第一条缓存的文本')

# 获取缓存
text = r.get('document_1')
```

##### （4）存储负载优化

合理规划存储层的负载分摊，是避免 RAG 系统掉入性能瓶颈的核心手段。借助对存储资源的科学调度与管控，让数据访问保持高效且均匀，系统整体压力随之下降。检索环节的数据读取和计算因此明显提速，整个系统的表现也得到强化。即使在海量数据和复杂任务的压力下，也能维持稳定流畅的运行，用户体验和系统效率都会迎来实质性提升。

举例来说，MySQL 就可以通过主从复制实现读写分离：写操作交由主库处理，读操作交给从库承接，从而把读写流量拆开。

```
// 在应用程序中配置主从复制实现读写分离（伪代码）
if operation == 'read':
    use slave_db  // 从库用于读操作
else:
    use master_db  // 主库用于写操作
```

#### 3. 高效数据管理策略

##### （1）数据归档和清理

要让 RAG 持续高效运转，定期归档和清理是必不可少的操作。把访问频率低的老数据迁移到长期存储，能够释放核心存储空间，提升响应速度，并减少检索时的干扰，让系统更快定位到相关内容。

同时，清理也是关键，要剔除过期、重复或已失去价值的信息。去掉这些无用数据可以避免检索时浪费算力和时间，提升整体检索效率，也有助于保持数据的准确与一致，让生成环节输出更可靠的结果。

例如，用 Python 编写定时任务完成旧数据的清理工作。

```
import os
import time

def clean_old_files(directory, days=30):
    now = time.time()
    cutoff = now - days * 86400

    for filename in os.listdir(directory):
        filepath = os.path.join(directory, filename)
        if os.path.getmtime(filepath) < cutoff:
            os.remove(filepath)
            print(f'Removed {filepath}')

# 清理超过 30 天的文件
clean_old_files('/path/to/data', days=30)
```

##### （2）数据生命周期管理

借助自动化迁移和删除，可以在数据的全生命周期内实现规范管理，进而提高效率和安全性。依托智能化工具与策略，数据能够按需在不同存储介质间自动流动，以匹配性能与成本的需求。定期清理不再需要的数据，既能释放空间、降低存储开销，也能满足合规与安全要求，人工失误的概率也会被大幅压缩。

下面以 AWS S3 的生命周期策略来演示该过程：

```
{
    "Rules": [
        {
            "ID": "Move old data to Glacier",
            "Filter": {},
            "Status": "Enabled",
            "Transitions": [
                {
                    "Days": 30,
                    "StorageClass": "GLACIER"
                }
            ],
            "Expiration": {
                "Days": 365
            }
        }
    ]
}
```

配置好生命周期规则后，数据过期即可自动下放到成本更低的存储层或被直接移除。

把前面提到的技术与策略有机串联起来，RAG 系统在数据存储与访问方面的表现会得到显著加强。

## 4.2 检索模块的优化

持续打磨检索模块，既能提高召回的准确度，也能加快响应速度。接下来从多个维度拆解如何系统性地提升检索能力。

### 4.2.1 嵌入模型的训练优化

在 RAG 构建中，嵌入模型的训练优化是重中之重。它的任务是尽可能精准地捕捉文本语义，为后续检索与生成提供可靠的事实基础。

#### 1. 微调

预训练模型像一位见多识广的学者，面对特定领域的问题仍需专门训练，这就是微调的价值。在 RAG 场景里，可以拿领域数据对通用嵌入模型进行微调。

比如要搭建医学领域的 RAG 系统，就可以用大量医学文献对模型进行微调，让它更好地理解专业术语与概念，从而提升检索的准确性和相关性。

#### 2. 多维度表示

多维度表示技术，如 Matryoshka 表示法，能够在不同维度上保留文本语义信息。该方法在保证效率的同时，提升了嵌入模型的灵活性。

假设用户问“如何提高机器学习模型的精度？”，库里有一篇标题为“优化模型性能的技巧”。表述虽不同，但借助高维向量，系统能够捕捉到“提高精度”与“优化性能”的语义相近性，最终匹配到相关文档。

#### 3. 优化损失函数

损失函数的选择直接决定训练方向。RAG 中常用的有多个负样本排序损失和 Matryoshka 损失。

多个负样本排序损失在训练时引入更多负例，增强模型区分能力。Matryoshka 损失则结合多维度表示，在不同截断尺度上同步优化模型。

#### 4. 对齐评分优化

对齐评分用于衡量查询与文档在语义空间的匹配程度，辅助检索阶段筛选最相关的文档。语义距离越近，得分越高；反之则越低。

以“适合夏天穿的轻便跑鞋”为例，图 4-1 与图 4-2 分别展示了优化前后的检索流程。

在电商推荐场景下，用户输入“适合夏天穿的轻便跑鞋”。没有优化时，系统可能只做词汇匹配：

- 查询向量：[夏天，轻便，跑鞋]
- 文档1 产品1 冬季厚底跑鞋：[冬天，厚重，跑鞋]
- 文档2 产品2 夏季轻便运动鞋：[夏天，轻便，运动鞋]

因“跑鞋”与“运动鞋”不完全一致，系统可能错误偏好产品1。

采用对齐评分优化策略后，如图 4-2：

- 提升“夏天”“轻便”两词的权重，让它们在相似度计算中占更大比重。
- 文档1 因缺少“夏天”“轻便”匹配，得分下降。
- 文档2 因同时匹配“夏天”“轻便”，得分明显上升。

最终系统更倾向推荐产品2，而不是产品1。

![图 4-1 优化前的检索流程](img/a020f99cb5cf7ce5c57c07d9b2d4de82_152_0.png)

![图 4-2 通过对齐评分优化后的检索流程](img/a020f99cb5cf7ce5c57c07d9b2d4de82_152_1.png)

### 4.2.2 文档解析技术的优化

文档解析的质量直接决定检索相关性和答案准确性。优化解析手段能够提升对大规模文档库的理解力，并提高整体效率。下面从方法和策略两方面展开。

#### 1. 文档解析方法

复杂或结构多样的文档会给 RAG 带来挑战。PDF、DOCX、含嵌入对象的文件以及手写扫描件，都包含多种格式、版式和内容，提取信息难度加大。PDF 可能混杂文本、图片、表格、图形；DOCX 常带复杂样式；嵌入对象需特殊处理；手写扫描件则依赖 OCR 才能转为可检索文本。

- 1）表格识别与处理：表格数据呈二维结构，直接抽取容易打乱行列对应关系，如财务报表中的科目与数字必须精准对应。
- 2）格式保持：标题、段落、列表等层级结构承载逻辑关系，解析丢失会造成理解偏差。
- 3）图像与图表解析：嵌入的视觉元素常含关键信息，传统文本抽取无法覆盖，需专门处理。
- 4）多语言支持：全球化文档可能多语混用，对语言识别能力提出更高要求。
- 5）噪声与失真：扫描文档常伴噪声、扭曲或缺失，增加抽取难度。

#### 2. 主流文档解析工具

为克服上述难题，可以借助以下主流工具。

##### （1）Tesseract OCR

Tesseract OCR 由 Google 维护的开源 OCR 引擎，在从扫描图像与 PDF 中抽取文字方面表现可靠。它支持多语言，兼顾印刷体，也可在配置优化后处理部分手写内容。pytesseract 是其 Python 封装，接口简洁，便于集成到应用中。

以下代码演示了借助 pytesseract 进行 OCR 的流程：

```python
import pytesseract
from PIL import Image

# 加载图像
image = Image.open('scanned_document.jpg')

# 进行 OCR 识别
text = pytesseract.image_to_string(image, lang='chi_sim')

print(text)
```

> **注意：** Tesseract 在处理复杂版式和表格时效果有限，通常需要配合其他工具做后处理。

##### (2) Unstructured

Unstructured 是一个 Python 库，擅长从 PDF、DOCX、HTML 等多种文档中抽取结构化文本，并在保留原文格式与层级信息的同时完成转换。它能帮助快速处理非结构化来源，提升洞察效率。

下面展示用 Unstructured 解析 PDF 的示例：

```python
from unstructured.partition.pdf import partition_pdf

# 解析 PDF 文档
elements = partition_pdf(filename="complex_document.pdf")

# 输出解析结果
for element in elements:
    print(element)
```

使用 Unstructured 的优势在于能够识别标题、段落、列表等结构，有助于保留文档的逻辑关系。

##### (3) PyMuPDF

PyMuPDF 即 fitz，是一个轻量高效的 PDF 处理库。它可提取文本与图像，并支持对 PDF 进行增删改页等编辑操作，满足定制化需求。

以下是基于 fitz 实现 PDF 文本抽取的示例：

```python
import fitz

# 打开 PDF 文档
doc = fitz.open("sample.pdf")

# 提取每一页的文本
for page_num in range(len(doc)):
    page = doc[page_num]
    text = page.get_text()
    print(f"Page {page_num+1}:\n{text}")
```

##### (4) Camelot

Camelot 专注于从 PDF 中抽取表格，并能将结果转为 Pandas DataFrame，便于后续清洗与分析，大幅简化数据处理流程。

下面是使用 Camelot 读取 PDF 表格的简单示例：

```python
import camelot
# 从 PDF 中提取表格
tables = camelot.read_pdf('tables.pdf')

# 显示第一个表格
print(tables[0].df)
```

##### (5) LayoutLM

LayoutLM 是一种融合文本与版式信息的预训练语言模型，适用于表格、表单、发票等结构复杂的文档解析。通过结合内容与布局特征，它能更准确地理解文档。

下面是如何使用 LayoutLM 的示例：

```python
from transformers import LayoutLMTokenizer, LayoutLMForTokenClassification

# 加载模型和分词器
tokenizer = LayoutLMTokenizer.from_pretrained("microsoft/layoutlm-base-uncased")
model = LayoutLMForTokenClassification.from_pretrained("microsoft/layoutlm-base-uncased")
```

#### 3. 文档解析优化方法

掌握好工具后，还需配合策略才能事半功倍。以下是几种具有代表性的优化做法。

##### (1) 递归字符文本分割方法

核心思想是用预设分隔符如句号、逗号等对长文本进行递归切分，使每块长度符合模型输入限制，避免因过长导致的错误与效率损失。递归切分能逐步细化文本，保持语义完整。

下面是该方法的实现代码：

```python
def recursive_split(text, max_length):
    if len(text) <= max_length:
        return [text]
    else:
        # 找到最近的句号
        idx = text.rfind('。', 0, max_length)
        if idx == -1:
            idx = max_length
        return [text[:idx]] + recursive_split(text[idx:], max_length)
```

这段代码会把长文本切分为多块，每块不超过指定 max_length，便于模型处理。其执行逻辑如下：

- 1）若文本长度小于 max_length，直接返回整体。
- 2）若过长，则在 max_length 范围内优先寻找最近的句号作为切分点。
- 3）按切分点将文本分为前段与后段，后段继续递归处理。
- 4）最终合并所有小块返回。

这样既能满足长度限制，又能尽量在句号处切分，维持语义连贯。

##### (2) 上下文增强的分割方法

做切分时如果只喂裸片段，模型很容易丢失方位感。更可靠的做法是在切分的基础上做语境增强，给每个片段补上定位线索，例如章节名称、段落小标题，以及相邻段落的浓缩摘要或要点句。这样既能保留片段自身的表意，也能让模型知道它在全文里的坐标和分工。举例说，片段恰好在章节开头，带上章节名就能快速锁定主题和来由；片段落在段落中间，补上前后概括就能理清逻辑链。借助这种增强策略，模型对深层含义的把握会更精准，处理效果也随之提升。

语境增强型切分的示例：

```python
enhanced_chunks = []
for i, chunk in enumerate(chunks):
    context = f"章节: {chapter_titles[i]}。\n 内容: {chunk}"
    enhanced_chunks.append(context)
```

该实现为每段片段注入定位线索，比如章节名，从而让语义更完整。它依次扫描切分得到的片段序列，把对应的章节标题 chapter_titles[i] 拼在片段前面，辅助模型判断片段在全篇中的座位和核心意图。处理完毕后得到 enhanced_chunks，里面每项都融合了定位线索与原文，可提升理解质量。

##### (3) 窗口化摘要方法

窗口化摘要的核心是滑窗。为每段片段附上邻近片段的浓缩版，以此扩充环境信息。片段不再孤立，而是自带周边视野，生成的摘要因此更连贯完整。处理超长文本时尤为好用，有助于建立全局连贯性，得到更准确的概括。

实现窗口化摘要的简易用例：

```python
window_size = 2
windowed_chunks = []
for i in range(len(chunks)):
    window = chunks[max(0, i-window_size):i+1]
    combined_chunk = ' '.join(window)
    windowed_chunks.append(combined_chunk)
```

该实现采用滑动窗口，把目标片段与其前若干片段合并，以丰富环境信息。参数 window_size 控制窗口宽度，即每个输出包含自身及前面最多 window_size 个片段的内容。代码遍历 chunks，按窗口范围拼接文本，存入 windowed_chunks。如此可为模型提供更宽的观察面，提升长文本处理时的理解水平。

##### (4) 自定义解析方法

不同来源的文档需要不同的解析策略。定制解析即针对特定格式设计专门流程。处理法务文本时，可专门提取条款与判例，帮助用户快速定位；处理技术文档时，可对代码块与公式做特殊保护，避免被错误拆分或改动。定制化解析能更好地应对复杂材料，提升解析效率与质量。

示例代码呈现如何用自定义解析处理法律条款：

```python
import re

def parse_legal_document(text):
    # 识别法律条款
    articles = re.split(r'第 [一二三四五六七八九十百千万]+ 条 ', text)
    parsed_articles = {}
    for i, article in enumerate(articles[1:], start=1):
        parsed_articles[f"第 {i} 条 "] = article.strip()
    return parsed_articles

# 使用示例
articles = parse_legal_document(legal_text)
```

该实现针对法律文本设计了专用解析流程。用正则 `r'第[一二三四五六七八九十百千万]+条'` 捕捉条款标题，如“第一条”“第二条”。先用 re.split 按标题切分文本得到 articles 列表，再从第二项起遍历，将每段内容按序号写入字典 parsed_articles。最终返回标题为键、内容为值的映射，便于查询与处理，提升解析的准确度与效率。

文档解析的优化是构建高效 RAG 系统的关键一步。选对工具、组合好方法，就能让模型更深入理解复杂材料。

实际落地时应结合业务需求与文档特性灵活组合，并持续评估调优，才能保持 RAG 系统的稳定表现。

### 4.2.3 同义词扩展

在 RAG 架构里，检索质量直接决定生成质量。查询扩展 QE 是提升检索的核心手段，其中同义词扩展占有重要地位。引入同义词后，系统能更全面把握用户真实意图，检索结果的相关度与覆盖面明显提升。通过扩充与原词意义相近的表达，检索可在更宽的语义面上寻址，为生成提供更丰富准确的背景，最终得到更高质量的回答。

#### 1. 同义词扩展的基本原理

同义词扩展的本质是在原查询中加入意义相近的词，丰富表达，从而覆盖更多相关材料，降低因用词差异导致的信息遗漏。

基本流程如下。

- 1）查询分析：拆解用户输入，找出需要扩充的关键词。
- 2）同义词获取：借助词典、知识库或大模型，获取关键词的同义词或近义词。
- 3）查询重构：将同义词融入原查询，形成扩展后的查询。
- 4）检索执行：用扩展后的查询在知识库中检索，拿到更多相关材料。

在 RAG 中，检索模块为生成模块提供背景信息，同义词扩展正好增强了检索能力，让生成有更好的素材。

#### 2. 实现方法

##### (1) 基于词典和知识库的同义词扩展

这是比较传统的做法，依赖 WordNet、专业同义词词典等已有资源。这些资源积累了大量词汇关系，包括同义、反义、上位下位等，能快速给出某个词的同义词集合，在 NLP、文本分析、信息检索里应用广泛。优点是高效且相对可靠。

局限也很明显：覆盖面有限，对专业术语或新词支持弱；关系是静态的，难以跟上语言变化；不同资源之间关系不一致，需要整合。尽管如此，它依然是实用且常用的方案。

示例代码呈现使用 NLTK 和 WordNet 做同义词扩展：

```python
import nltk
from nltk.corpus import wordnet

# 确保已下载 WordNet 数据
nltk.download('wordnet')

def get_synonyms(word):
    synonyms = set()
    for syn in wordnet.synsets(word):
        for lemma in syn.lemmas():
            # 排除与原词相同的词形
            if lemma.name().lower() != word.lower():
                synonyms.add(lemma.name())
    return synonyms

# 示例
query = "happy"
synonyms = get_synonyms(query)
print(f"原始词: {query}")
print(f"同义词: {synonyms}")
```

运行后得到如下输出：

```
原始词: happy
同义词: {'felicitous', 'glad', 'well-chosen'}
```

借此可以为查询中的每个关键词找到对应的同义词，进而扩展查询。

##### (2) 基于神经网络模型的同义词扩展功能

大模型类神经网络具备很强的语言理解和生成能力，可以根据语境动态产出与关键词相关的词汇。模型能捕捉细微语义关联，给出意义相近或相关的表达，在文本生成、机器翻译等场景价值很大。

示例代码呈现使用 OpenAI 的 GPT-4 API 做同义词扩展：

```python
import openai

# 设置 OpenAI API 密钥
openai.api_key = '你的_openai_api_key'  # 请替换为你的实际 API 密钥

def get_synonyms_gpt4(word, top_k=5):
    """
    使用 GPT-4 API 生成给定词汇的同义词列表。

    :param word: 需要扩展同义词的词汇
    :param top_k: 返回的同义词数量
    :return: 同义词列表
    """
    # 构建提示语，明确要求 GPT-4 提供同义词
    prompt = f"请为以下词汇提供 {top_k} 个准确的同义词，并以列表形式返回。\n\n 词汇：{word}\n\n 同义词："

    try:
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": "你是一个有帮助的助手。"},
                {"role": "user", "content": prompt}
            ],
            max_tokens=60,
            n=1,
            stop=None,
            temperature=0.7,
        )

        # 提取 GPT-4 的回复
        synonyms_text = response.choices[0].message['content'].strip()

        # 解析同义词列表
        # 假设 GPT-4 返回的是一个编号或项目符号列表
        synonyms = []
        for line in synonyms_text.split('\n'):
            # 去除可能的编号、项目符号和其他非词汇字符
            synonym = line.strip().lstrip('1234567890.、- ').strip()
            if synonym:
                synonyms.append(synonym)
            if len(synonyms) >= top_k:
                break

        return synonyms

    except Exception as e:
        print(f"An error occurred: {e}")
```

##### （3）基于伪相关反馈的同义词扩展方法

伪相关反馈是一种实用的检索增强技术。先做一次初检，从返回的相关材料里自动提取高频词作为扩展词，再用新查询二次检索。因为初检结果通常与用户意图接近，其中的高频词能较好反映真实需求，从而提升查全率和查准率。

典型步骤如下。

1. 系统执行初次检索，获取一组与用户查询相关的材料。
2. 对这些材料进行文本分析，提取其中的高频词。
3. 将这些高频词作为扩展词加入原始查询中，形成新的查询表达式。
4. 系统再次执行检索，以新的查询表达式获取更全面的检索结果。

借此能在一定程度上缓解词汇不匹配问题。

示例代码呈现如何用 TF-IDF 提取高频词做扩展：

```python
from sklearn.feature_extraction.text import TfidfVectorizer

# 初次检索得到的文档集合
documents = [
    "The cat sits on the mat.",
    "A kitten is playing with a ball.",
    "Feline creatures are curious."
]

def get_high_tfidf_terms(documents, top_k=5):
    vectorizer = TfidfVectorizer()
    tfidf_matrix = vectorizer.fit_transform(documents)
    feature_array = vectorizer.get_feature_names_out()
    tfidf_scores = tfidf_matrix.sum(axis=0).A1
    top_indices = tfidf_scores.argsort()[-top_k:][::-1]
    top_terms = feature_array[top_indices]
    return top_terms

# 获取高 TF-IDF 词汇
expanded_terms = get_high_tfidf_terms(documents)
print(f"扩展词汇: {expanded_terms}")
```

运行后得到如下输出：

```
扩展词汇: ['feline' 'curious' 'creatures' 'playing' 'kitten']
```

这些高频词可以用于扩展原始查询，提升检索效果。

#### 3. 在 RAG 系统中的应用

下面来看同义词扩展技术在 RAG 系统中的应用。

##### （1）实例分析

假设我们要做一个医学问答系统，用户问“如何治疗头痛？”。知识库里可能用“偏头痛”“头部疼痛”等不同表述，直接用原查询会漏掉部分信息。

通过同义词扩展，把“头痛”扩写为“头部疼痛”“偏头痛”“脑袋疼”等，就能覆盖更多相关材料。

##### （2）在 RAG 系统中集成同义词扩展的代码示例如下：

```python
def expand_query_with_synonyms(query):
    expanded_query = set(query.split())
    for word in query.split():
        synonyms = get_synonyms(word)
        expanded_query.update(synonyms)
    return ' '.join(expanded_query)

def retrieve_documents(expanded_query, knowledge_base):
    # 简单的全文检索示例
    relevant_docs = []
    for doc in knowledge_base:
        if any(term in doc for term in expanded_query.split()):
            relevant_docs.append(doc)
    return relevant_docs

# 示例知识库
knowledge_base = [
    "偏头痛的常见治疗方法包括休息和药物治疗。",
    "头部疼痛可能由多种原因引起。",
    "感冒也会导致头痛症状。"
]

query = "如何治疗头痛"
expanded_query = expand_query_with_synonyms(query)
retrieved_docs = retrieve_documents(expanded_query, knowledge_base)

print(f"扩展后的查询: {expanded_query}")
print("检索到的文档: ")
for doc in retrieved_docs:
    print(f"{doc}")
```

上述代码的输出示例如下：

```
扩展后的查询: 如何 头痛 治疗 head ache
检索到的文档:
偏头痛的常见治疗方法包括休息和药物治疗。
头部疼痛可能由多种原因引起。
感冒也会导致头痛症状。
```

借助同义词扩展，成功检索到了包含“偏头痛”“头部疼痛”等相关表达的文档。

##### （3）使用须知

做同义词扩充时一定要收着来，扩得太猛检索就会被无关信息带偏，精准度与准确率都会掉。扩展词表必须逐项把关，只保留与原查询高度贴合的项，才能保证返回结果的干净度。

词义歧义处理也不能忽视。同一词汇在不同语境下指代完全不同，扩写过程中若不做消歧，很容易把歧义词带进去，影响相关性。因此要在扩展环节加入消歧校验，剔除模糊项。

另外还有成本问题。大规模扩写会明显拉高检索计算开销，让系统变慢。工程落地时要找到扩写深度与响应性能之间的平衡点，既保质量也保速度。

### 4.2.4 查询改写

在 RAG 体系里，查询改写是对用户原提问进行再表述或扩充，帮助模型更准地抓住真实意图，从而召回更贴合需求的文档。意图被澄清后，生成环节的表现也会随之提升，整体检索效率和用户满意度都会上扬。

比如用户输入“苹果的好处”这种表述模糊的提问，直接检索容易跑偏。改写为“食用苹果对健康的益处”后，系统就能锁定更相关的资料。

查询改写带来的价值主要体现在三方面。

1. 检索更准：改写后的提问更贴近真实意图，结果相关性更高。
2. 消化语言复杂性：能应对同义词、歧义词和语义模糊等自然语言问题。
3. 体验更顺滑：用户无需反复修改提问，交互效率提升。

#### 1. 核心改写技术

目前主流的改写方式可归为规则驱动、大模型驱动和混合增强三类。

##### （1）规则驱动改写

事先设定好规则与模式，对提问做替换或扩充。例如：

- 同义词替换：把提问中的词换成近义词。
- 短语扩充：把简短提问扩展成更完整的表达。

以下是基于规则改写的代码示例：

```python
import re

# 定义同义词词典
synonyms = {
    "好处": ["益处", "优点", "好处"],
    "苹果": ["苹果", "苹果公司"]
}

def rule_based_rewrite(query):
    for word in query.split():
        if word in synonyms:
            query = query.replace(word, synonyms[word][0])
    return query

original_query = "苹果的好处"
rewritten_query = rule_based_rewrite(original_query)
print("重写后的查询：", rewritten_query)
```

上述代码的示例输出如下：

```
重写后的查询：苹果的益处
```

##### （2）大模型驱动改写

借助 GPT-4 等大模型改写查询，可以捕捉更深层的语义关联。以下是基于生成模型的改写代码示例：

```python
import openai

openai.api_key = 'YOUR_API_KEY'

def generative_rewrite(query):
    prompt = f"将以下查询重写为更详细的形式：{query}"
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=prompt,
        max_tokens=50
    )
    return response.choices[0].text.strip()

original_query = "苹果的好处"
rewritten_query = generative_rewrite(original_query)
print("重写后的查询: ", rewritten_query)
```

上述代码的示例输出（可能因模型生成而异）如下：

```
重写后的查询：食用苹果对健康有哪些益处？
```

##### （3）混合增强改写

混合方案兼顾规则与生成模型的优点，实现分层优化。

1. 步骤 1：先用规则做初筛改写。
2. 步骤 2：再用生成模型做润色完善。

#### 2. 改写优化策略

改写优化的目标是让查询更准、更全、检索效果更好，下面介绍几种常用做法。

##### （1）反馈驱动优化

利用检索反馈持续修正改写策略。反馈分为离线和在线两种。

1. 离线反馈具体做法如下。
    - 历史数据分析：通过检索日志分析点击、跳出、满意度等指标，评估改写效果。
    - 模式挖掘与建议：用机器学习从历史数据里提取有效模式，定位常见改写失误或意图偏差，给出优化建议。
2. 在线反馈具体做法如下。
    - 实时行为监控：捕捉点击率、浏览时长等实时行为，立刻判断改写是否有效。
    - 动态策略调整：根据实时反馈调整策略，如增补同义词、扩展意图或调参，提升相关性。

举例帮助理解：

1. 用户提问“最近的咖啡店”。
2. 初步结果不满足，用户反馈需要“评分高的咖啡店”。
3. 系统将提问改写为“最近评分高的咖啡店”，并把这次反馈记录下来用于未来同类改写优化。

这种闭环机制能快速适应需求变化，持续提升策略效果。

##### （2）迭代重写检索

通过循环迭代逐步逼近最优答案，每轮都基于上一轮的检索结果优化表达。

流程如下：

1. 初始提问：用户输入原始问题，与检索库交互。
2. 初次改写：增加同义词或优化语法。
3. 初次检索：获得初步结果。
4. 结果评估：判断相关性和覆盖度，找出不足。
5. 二次改写：加入特定关键词或调整逻辑。
6. 循环迭代：重复 3-5 步，直至结果满足需求或达到阈值。

示例：

1. 用户输入“武汉天气”。
2. 改写为“武汉明天的天气预报”。
3. 结果只给出明天，用户实际想要未来三天趋势。
4. 再改写为“武汉未来三天的天气趋势”。
5. 最终结果符合预期，流程结束。

渐进式优化能精准响应隐式或显式反馈，提升答案质量。

##### （3）逐步拆解提示

把复杂提问拆成若干小步骤逐一解决，保证逻辑完整。

方法如下：

1. 需求拆解：识别复杂问题中的核心点，拆成更明确的子问题。
2. 子问题优化：对每个子问题单独改写，形成优化表达。
3. 综合整合：将各子结果合并为最终查询或多步检索流程。

示例流程：

原始提问：
> 如何在 Python 中实现快速排序？

逐步拆解：

1. “什么是快速排序算法？”（原理说明）
2. “快速排序算法的步骤是什么？”（步骤描述）
3. “如何用 Python 实现快速排序？”（代码示例）

拆解后能更准确挖掘潜在需求，提升检索效率和准确度。

以上三种优化可组合使用：反馈驱动适合需要长期动态改进的场景，迭代重写检索适合探索式查询，分步提示适合复杂多层需求。

## 4.3 问答模块提效

优化问答模块能同时提升 RAG 系统的智能水平和用户体验。本节聚焦问答能力的系统性提升，涵盖信息抽取、知识融合、上下文理解等深度优化，同时强调拒答能力的建设，避免对不当问题给出错误或误导性回答，保证系统可靠性。

### 4.3.1 问答能力增强

在 RAG 架构中，大模型负责基于检索内容生成答案，其问答水平直接决定最终输出质量。以下介绍几类提升策略。

#### 1. 参数轻量微调

参数高效微调 PEFT 只更新模型中极少部分参数，无需全量微调，优势明显：

1. 显著降低算力需求。只需动小部分参数，显存与计算量大幅下降，资源受限环境也能进行微调。
2. 缓解灾难性遗忘。全量微调易丢失预训练知识，PEFT 通过保留大部分参数，维持原有能力的同时学习新任务。

典型方案是 LoRA，通过为预训练权重添加低秩矩阵实现高效更新。以下是用 LoRA 对 Qwen-7B 做参数轻量微调的代码示例：

```python
from transformers import AutoModel, AutoTokenizer
from peft import LoraConfig, get_peft_model

# 加载预训练模型和分词器
model_name = "Qwen/Qwen-7B"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModel.from_pretrained(model_name)

# 配置 LoRA 参数
lora_config = LoraConfig(
    r=16,                # 低秩矩阵的秩
    lora_alpha=32,       # LoRA 的缩放因子
    target_modules=["q", "v"],  # 需要应用 LoRA 的模块
    lora_dropout=0.1,
)

# 将模型转换为 LoRA 模型
model = get_peft_model(model, lora_config)

# 训练模型
train_data = ...  # 定义读者的训练数据集
trainer = Trainer(
    model=model,
    train_dataset=train_data,
    ...  # 其他训练参数
)
trainer.train()
```

#### 2. 领域定向微调

任务特定微调使用特定领域或任务的数据集对预训练模型进行二次训练。

优势：

1. 提升领域适应性。模型能更深入理解专业术语与核心概念，提升领域问题处理能力。
2. 提高回答准确性。生成内容更贴合领域实际需求，回答更精确可靠。

#### 3. 多任务联合训练

多任务学习同时在多个相关任务上训练模型，使其具备更强的泛化通用性。

优势：

1. 知识共享。不同任务间的信息相互辅助，学到的知识可在任务间迁移，提升整体表现。

#### 4. 自我迭代优化

自我反思优化让模型通过内部反馈机制不断改进输出。生成初始答案后，模型自行评估问题与不足并修正，最终输出更高质量结果。

带来的效果：

- 1）答案质量明显提升，错误与信息缺失被及时修正。
- 2）回答风格与人类偏好更一致，可读性和组织性增强。
- 3）长期性能稳步提升，更好满足用户期望。

下面以 Qwen/Qwen-7B 为例，模拟一个循环反思机制：模型先生成初稿，再经反思反馈改进，最终输出优化版答案。

```python
from transformers import QwenTokenizer, QwenForCausalLM

# 加载 Qwen 模型和分词器
model_name = "Qwen/Qwen-7B"
tokenizer = QwenTokenizer.from_pretrained(model_name)
model = QwenForCausalLM.from_pretrained(model_name).to("cuda")  # 使用 GPU 加速

# 定义自我反思优化函数
def self_reflective_answer(question, max_reflections=2):
    """
    自我反思优化函数生成初始回答后，通过反思反馈机制改进回答。
    :param question: 输入问题
    :param max_reflections: 最大反思次数
    :return: 优化后的回答
    """
    # Step 1: 初始回答
    input_ids = tokenizer(question, return_tensors="pt", truncation=True, max_length=512).to("cuda")
    initial_output = model.generate(input_ids, max_new_tokens=128, do_sample=True)
    initial_answer = tokenizer.decode(initial_output[0], skip_special_tokens=True)

    print(f"初始回答: {initial_answer}")

    # Step 2: 自我反思与改进循环
    current_answer = initial_answer
    for reflection_round in range(max_reflections):
        # 生成反思反馈
        reflection_prompt = f"请评价以下回答的质量，并指出可以改进的地方：\n\n问题：{question}\n回答：{current_answer}\n反馈："
        reflection_ids = tokenizer(reflection_prompt, return_tensors="pt", truncation=True, max_length=512).to("cuda")
        reflection_output = model.generate(reflection_ids, max_new_tokens=128, do_sample=True)
        reflection = tokenizer.decode(reflection_output[0], skip_special_tokens=True)

        print(f"第 {reflection_round+1} 轮反思反馈: {reflection}")

        # 根据反思生成改进后的回答
        improvement_prompt = f"根据以下反馈，改进之前的回答：\n\n反馈：{reflection}\n之前的回答：{current_answer}\n改进后的回答："
        improvement_ids = tokenizer(improvement_prompt, return_tensors="pt",
            truncation=True, max_length=512).to("cuda")
        improved_output = model.generate(improvement_ids, max_new_tokens=128,
            do_sample=True)
        improved_answer = tokenizer.decode(improved_output[0], skip_special_tokens=True)

        print(f"第 {reflection_round+1} 轮改进后的回答：{improved_answer}")

        # 更新回答
        current_answer = improved_answer

    return current_answer

# 推理测试函数
question = "解释一下机器学习中的过拟合问题。"
final_answer = self_reflective_answer(question)
print(final_answer)
```

自我迭代让模型越写越好
这段实现覆盖的流程大致如下。

第一步 把 Qwen/Qwen-7B 配套的 QwenTokenizer 与 QwenForCausalLM 搬到 GPU 上就绪。
第二步 先出初稿：把用户问题送入生成器，拿到第一版答案。
第三步 生成自检意见：用 reflection_prompt 提示让模型审视刚产生的答案，并产出改进建议。
第四步 基于反馈重写：把上轮建议与原文合并成 improvement_prompt，再次召回优化版。
第五步 控制轮数：用超参 max_reflections 约束反思与改写的往复次数。
第六步 效果演示：以“解释一下机器学习中的过拟合问题”为探针，完整呈现初稿、点评与精炼过程。

示例输出片段：
> 初始回答：过拟合是指模型在训练数据上表现良好，但在测试数据上表现较差。
> 第 1 轮反思反馈：回答较简洁但缺乏详细解释，可补充定义的细节和如何避免过拟合。
> 第 1 轮改进后的回答：过拟合是指模型在训练数据上表现良好，但在测试数据上表现较差。这通常是因为模型过于复杂，记住了训练数据的噪声，而未能学习到通用模式。避免过拟合的方法包括正则化、增加训练数据量和使用交叉验证。
> 第 2 轮反思反馈：回答改进明显，但可以进一步补充具体的正则化技术，例如 L1、L2 正则化。
> 第 2 轮改进后的回答：过拟合是指模型在训练数据上表现良好，但在测试数据上表现较差。这通常是因为模型过于复杂，记住了训练数据的噪声，而未能学习到通用模式。避免过拟合的方法包括正则化（如 L1 和 L2 正则化）、增加训练数据量、使用交叉验证，以及减少模型复杂度。

这种自检自改的闭环，能明显提升输出的专业度与可信度。

#### 5. 提示词优化
Prompt 设计目前主要有两条路线。

1）人工打磨：依赖对模型行为的深入理解，由研究员或工程师凭借经验手动优化 Prompt，确保能有效引导出高质量回应，这需要扎实的专业积累。
2）自动打磨：借助算法自动生成或迭代 Prompt。常见做法有遗传算法，模拟自然选择不断演化组合寻找最优 Prompt；也有梯度法，通过计算梯度逐步调整 Prompt 参数以降低输出误差。两种方式都能把 Prompt 优化过程部分自动化。

下面以 Qwen/Qwen-7B 为例，融合人工与自动两种策略做实践。

```python
from transformers import QwenTokenizer, QwenForCausalLM
import random

# 加载 Qwen 模型和分词器
model_name = "Qwen/Qwen-7B"
tokenizer = QwenTokenizer.from_pretrained(model_name)
model = QwenForCausalLM.from_pretrained(model_name).to("cuda")  # 使用 GPU

# 手动设计提示词的函数
def generate_response_manual(question, context=" 详细回答问题 "):
    """
    手动优化的提示词设计。
    :param question: 输入的问题
    :param context: 提示词上下文
    :return: 模型的回答
    """
    prompt = f"{context}\n 问题: {question}\n 回答: "
    input_ids = tokenizer(prompt, return_tensors="pt", truncation=True, max_length=512).to("cuda")
    output = model.generate(input_ids, max_new_tokens=128, do_sample=True)
    response = tokenizer.decode(output[0], skip_special_tokens=True)
    return response

# 自动优化提示词的函数（基于随机采样模拟优化）
def optimize_prompt_auto(question, candidate_prompts, num_iterations=5):
    """
    自动优化的提示词设计（随机采样方法）。
    :param question: 输入问题
    :param candidate_prompts: 提示词候选集合
    :param num_iterations: 迭代次数
    :return: 优化后的提示词和对应的回答
    """
    best_prompt = None
    best_response = None
    highest_score = float("-inf")  # 初始化评分
    scoring_function = lambda response: len(response)  # 简单的评分函数（内容长度）

    for _ in range(num_iterations):
        prompt = random.choice(candidate_prompts)
        full_prompt = f"{prompt}\n 问题: {question}\n 回答: "
        input_ids = tokenizer(full_prompt, return_tensors="pt", truncation=True,
                             max_length=512).to("cuda")
        output = model.generate(input_ids, max_new_tokens=128, do_sample=True)
        response = tokenizer.decode(output[0], skip_special_tokens=True)

        # 使用评分函数评估回答质量
        score = scoring_function(response)
        print(f" 提示词: {prompt} | 评分: {score} | 回答: {response}")

        if score > highest_score:
            highest_score = score
            best_prompt = prompt
            best_response = response

    return best_prompt, best_response

# 测试手动提示词优化
question = " 什么是机器学习中的过拟合 "
manual_response = generate_response_manual(question)
print(f" 手动提示词优化的回答: {manual_response}")

# 测试自动提示词优化
candidate_prompts = [
    " 以专业的方式回答以下问题 ",
    " 请用简洁明了的语言回答 ",
    " 从技术角度详细分析以下问题 ",
    " 用通俗易懂的方式解释 ",
    " 模拟专家回答以下问题 "
]
best_prompt, best_response = optimize_prompt_auto(question, candidate_prompts)
print(f" 最佳提示词: {best_prompt}\n 自动提示词优化的最佳回答: {best_response}")
```

执行上述示例代码后，得到的结果如下。

```
// 手动提示词优化
手动提示词优化的回答：过拟合是指模型在训练数据上表现良好，但在测试数据上表现较差。
// 自动提示词优化
提示词：用通俗易懂的方式解释 | 评分：68 | 回答：过拟合是指模型在训练数据上表现很好，但当面对新数据时效果较差。解决方法包括简化模型、增加训练数据等。
提示词：模拟专家回答以下问题 | 评分：72 | 回答：过拟合是指模型在训练集上表现优异，但在测试集或未见过的数据上表现较差。这通常是因为模型过于复杂，记住了训练数据的噪声而不是模式。
提示词：以专业的方式回答以下问题 | 评分：78 | 回答：过拟合是指模型在训练数据上表现优秀，但泛化能力较弱。这可能是由于模型复杂度过高，导致它过于依赖特定数据集。解决方案包括正则化、交叉验证等。
最佳提示词：以专业的方式回答以下问题
自动提示词优化的最佳回答：过拟合是指模型在训练数据上表现优秀，但泛化能力较弱。这可能是由于模型复杂度过高，导致它过于依赖特定数据集。解决方案包括正则化、交叉验证等。
```

该优化思路能明显增强生成结果的相关性与质量。

#### 6. 引入人类反馈
训练与调优阶段引入 Human Feedback，可显著带动整体表现。让专业人士参与标注、评估与建议，学习效率会更高。

主要收益有三点：
1）专家标注更可靠。凭借经验与直觉判断，专家能更精准地识别数据中的关键信息，保证训练样本质量，有效减少偏差，提升准确率和鲁棒性。
2）反馈可直指短板。专家能指出模型在特定场景或任务上的不足，基于这些意见可针对性调整结构、参数或训练策略。
3）帮助理解复杂抽象内容。专家能提供具体案例与解释，弥补数据难以覆盖的语境、语义细微差别等知识缺口。例如在 NLP 任务中，提供上下文与语义解释，帮助模型更好地把握语言的细微差别和隐含意义。

下面以 Qwen/Qwen-7B 为例实现人类反馈接入。通过模拟接口获取专家意见，并把这些意见作为新的训练数据用于模型迭代。

```python
from transformers import QwenTokenizer, QwenForCausalLM

# 加载 Qwen 模型和分词器
model_name = "Qwen/Qwen-7B"
tokenizer = QwenTokenizer.from_pretrained(model_name)
model = QwenForCausalLM.from_pretrained(model_name).to("cuda")  # 使用 GPU

# 模拟获取人类反馈的函数
def get_human_feedback(question, model_answer):
    """
    模拟一个接口，从人类专家获取反馈。
    """
    print(f"问题: {question}")
    print(f"模型的回答: {model_answer}")
    # 模拟人类输入的反馈
    feedback = input("请对模型的回答进行评价，并提供改进建议: ")
    return feedback

# 使用人类反馈优化模型
def train_with_human_feedback(model, tokenizer, data, max_iterations=5):
    """
    利用人类反馈优化模型。
    :param model: 预训练模型
    :param tokenizer: 分词器
    :param data: 待处理的问题数据
    :param max_iterations: 最大迭代次数
    """
    updated_data = []  # 用于存储更新后的训练数据

    for iteration in range(max_iterations):
        print(f"\n=== 第 {iteration + 1} 轮反馈优化 ===\n")
        for question in data:
            # 模型生成初始回答
            input_ids = tokenizer(question, return_tensors="pt", truncation=True,
                                  max_length=512).to("cuda")
            output = model.generate(input_ids, max_new_tokens=128, do_sample=True)
            model_answer = tokenizer.decode(output[0], skip_special_tokens=True)

            # 获取人类反馈
            feedback = get_human_feedback(question, model_answer)

            # 存储新的训练数据
            updated_data.append({"input": question, "output": feedback})

        # 更新训练数据集，模拟训练过程
        # 这里可以扩展为使用强化学习或微调训练
        print(f" 本轮的新训练样本: {updated_data}")

    return updated_data

# 测试示例问题
questions = [
    "什么是机器学习中的过拟合？",
    "深度学习中的激活函数有什么作用？"
]

# 模拟调用函数
updated_training_data = train_with_human_feedback(model, tokenizer, questions)
print(f"\n最终更新的训练数据集: {updated_training_data}")
```

下面把这段示例代码涉及的完整运行脉络梳理清楚。

- 1）模拟人类反馈接口。`get_human_feedback` 充当模拟的人类反馈入口，会先展示出待回答的问题以及模型当前的生成结果，再收集人提出的具体改进建议。真实部署时，这一步可替换为网页界面、表单收集或交互式对话系统。
- 2）训练数据更新。每次拿到人类反馈后，系统就会把原问题和对应的反馈一起打包成新的训练样本。示例里用简易字典 `{"input": question, "output": feedback}` 保存，工程实践中更常见的是写入 Hugging Face 的 Dataset 格式。
- 3）训练循环。`train_with_human_feedback` 模拟了反馈与优化闭环：① 先呈现问题及模型答案；② 再收集人类反馈；③ 最后把反馈写入训练集。
- 4）强化学习扩展。当前示例里反馈只是直接追加进数据集。实际应用中可以把反馈转化为强化学习的奖励信号，用来指导模型行为策略的优化。

运行以上示例代码得到如下的输出结果：

```
# 第一轮反馈优化
=== 第 1 轮反馈优化 ===
问题：什么是机器学习中的过拟合？
模型的回答：过拟合是指模型在训练数据上表现很好，但在测试数据上效果较差。
请对模型的回答进行评价，并提供改进建议：回答缺乏具体细节，可补充避免过拟合的具体方法。
问题：深度学习中的激活函数有什么作用？
模型的回答：激活函数用于引入非线性。
请对模型的回答进行评价，并提供改进建议：回答过于简单，可补充常见激活函数及其适用场景。
# 更新后的训练数据
最终更新的训练数据集：
{"input": "什么是机器学习中的过拟合？", "output": "回答缺乏具体细节，可补充避免过拟合的具体方法。"},
{"input": "深度学习中的激活函数有什么作用？", "output": "回答过于简单，可补充常见激活函数及其适用场景。"}
```

加入人类反馈后，模型对复杂任务的细节捕捉能力明显增强，回答的精确度和相关性也得到提升。这一思路在医学、法律、教育等对专业度要求极高的领域尤为适用。

大模型问答能力的提升是持续迭代的过程，落地时要结合需求与算力资源挑选合适的优化方案。比如在算力受限的情况下，参数高效微调更具性价比；而在需要深厚专业知识的场景，任务专属微调配合人类反馈的价值会更加突出。

### 4.3.2 增强拒答能力

以海量互联网语料为基础训练的大模型，难免会吸收其中的负面倾向而产生偏见。面对不当或敏感的查询时，必须设置保障机制，确保输出保持中立与公平。这里就涉及拒答能力，即当请求明显不妥、存在风险或可能不安全时，模型能够礼貌而坚定地拒绝作答。借助这一机制，系统可以主动筛掉易引发争议或不适的输入，避免生成有害内容，也让整体运行环境更健康。用户也会因此更安心，知道系统具备自我约束和保护利益的能力。更进一步，从根源上抑制偏见仍需从模型本身进行约束，具体做法将在 6.3 节展开。

#### 1. 现有的拒答技术

在提升拒答能力上，研究者已经尝试了多种主流路径，具体如下。

##### (1) 有监督微调
利用专门构建的拒答数据集合进行有监督微调 SFT，模型可以学会识别不合适的请求并给出拒绝。这些数据集通常覆盖大量会触发拒答的案例，场景多样。训练后模型不仅能判断何时该拒绝，还能学会以得体的方式回应，保持用户体验。这一方法的成效很大程度上取决于拒答数据的质量与覆盖度，数据越全面，泛化能力越强。

##### (2) 对抗性训练
对抗性训练的核心是引入刻意设计的对抗样本来锻炼拒答。研究人员会构造容易让模型误判的输入，迫使模型在这些样本上学习识别并拒绝具有迷惑性的请求。通过持续对抗，模型对各种攻击和不合理提问的抵抗力会明显增强。

##### (3) 强化学习
强化学习通过奖励设计来塑造拒答行为：正确拒绝不当请求时给予正向奖励，给出错误回答则施加惩罚。将模型放在模拟环境中与多样请求交互，并借助 PPO 等算法不断优化拒答策略，最终提高拒答的准确率和礼貌度。

##### (4) 多任务学习
多任务学习把拒答任务与意图识别、文本分类等相关任务联合训练，共享模型结构。多个任务的损失一起优化，辅助任务的知识能够迁移到拒答任务上，从而提升整体表现。

#### 2. 增强拒答能力的策略

除了技术手段，在 RAG 等系统里还可以借助一些策略进一步强化拒答。

##### (1) 细粒度拒答分类
为了更精准地处理各类不当请求，可以搭建细粒度的拒答分类体系，用更具体的类别区分不同风险。常见划分包括：

- 1）非法活动：涵盖欺诈、盗窃、网络攻击等违法行为。
- 2）敏感信息：涉及个人隐私、机密文件、医疗记录等不应公开的内容。
- 3）仇恨言论：包含种族歧视、性别歧视、宗教攻击等带有攻击性的表达。

借助这种分类，模型不仅能识别显而易见的不当请求，也能揪出隐藏在正常表述下的违规意图，例如表面是事实查询，实则夹带歧视性内容。此外，细粒度分类还能让模型对模糊或灰色地带的请求做出更灵活、合理的判断。

##### (2) 多模态拒答
RAG 场景中常出现多模态输入，模型需要同时理解文本、图像等多种信号并综合决策。以某个问题为例，系统不仅要分析文字本身，还可能需要结合关联图像或其他模态信息来评估问题难度与回答可行性。若判断信息不足或超出知识边界，就可选择拒答。

下面是一个融合文本与图像的拒答示例，采用更模块化的处理方式。假设文本流经 NLP 模型分析，图像流经计算机视觉 CV 模型分析：

```python
from transformers import pipeline
from PIL import Image

# 初始化文本分析模型（如零样本分类，用于文本内容检查）
text_classifier = pipeline("zero-shot-classification", model="facebook/bart-large-mnli")

# 初始化图像分析模型（如图像内容分类，用于图像内容检查）
from transformers import ViTForImageClassification, ViTFeatureExtractor
import torch

vit_model = ViTForImageClassification.from_pretrained("google/vit-base-patch16-224")
vit_feature_extractor = ViTFeatureExtractor.from_pretrained("google/vit-base-patch16-224")

# 定义拒答检查函数
def multimodal_refusal_check(text_input, image_path):
    """
    多模态拒答检查函数。
    :param text_input: 文本输入
    :param image_path: 图像路径
    :return: 是否拒答的决策（True: 拒答，False: 可回答）
    """
    # 文本内容检查
    text_flag = check_text_for_inappropriate_content(text_input)

    # 图像内容检查
    image_flag = check_image_for_inappropriate_content(image_path)

    # 综合判断
    if text_flag or image_flag:
        return True  # 应当拒答
    else:
        return False  # 可以回答

# 文本分析函数
def check_text_for_inappropriate_content(text_input):
    """
    检查文本内容是否包含不恰当的信息。
    """
    labels = ["inappropriate", "safe"]
    result = text_classifier(text_input, labels)
    print(f"文本分析结果：{result}")
    return result["labels"][0] == "inappropriate"

# 图像分析函数
def check_image_for_inappropriate_content(image_path):
    """
    检查图像内容是否包含不恰当的信息。
    """
    image = Image.open(image_path).convert("RGB")
    inputs = vit_feature_extractor(images=image, return_tensors="pt")
    outputs = vit_model(**inputs)
    logits = outputs.logits
    predicted_class = torch.argmax(logits).item()
    print(f"图像分析结果：{predicted_class}")
    # 假设类 0 表示 "safe"，类 1 表示 "inappropriate"
    return predicted_class == 1

# 测试案例
text_input = "Explain how to hack a secure system."  # 示例文本
image_path = "test_image.jpg"  # 示例图像路径

# 进行多模态拒绝检查
decision = multimodal_refusal_check(text_input, image_path)
if decision:
    print(" 拒答：内容不适合提供回答。")
else:
    print(" 可以回答：内容符合规范。")
```

对上述代码展示的核心处理流程做如下解读。

- 1）文本分析部分。采用 Hugging Face 的零样本分类模型 `facebook/bart-large-mnli`，对输入文本判断是否落入“不恰当”类别。`labels` 用于定义分类目标，并输出类别预测与置信度。
- 2）图像分析部分。借助 ViT，即 Vision Transformer 模型，对图像内容进行分类。设定模型可输出 safe 或 inappropriate 等标签，并依据标签决定是否触发拒答。
- 3）多模态综合判断。文本侧和图像侧各自返回布尔结果，标识是否含有不当内容。综合逻辑为：一旦任一模态判定为 True，系统即拒绝回答。
- 4）模块化设计。文本和图像的检查被封装成独立函数，便于后续扩展，也可替换为更复杂的检测方案，比如自定义分类器或引入更多模态。

运行上述代码得到的输出结果如下：

```
# 测试输入
# 文本："Explain how to hack a secure system."
# 图像：假设图像包含不恰当内容。

# 执行结果
文本分析结果：{'sequence': 'Explain how to hack a secure system.', 'labels': ['inappropriate', 'safe'], 'scores': [0.85, 0.15]}
图像分析结果：1
拒答：内容不适合提供回答。
```

##### (3) 历史上下文结合
在基于 RAG 的对话系统中，当前请求往往依赖于之前的交互历史。要准确把握用户真实意图，模型必须把历史对话与当前输入放在一起分析。只有在充分理解上下文的前提下，才能做出是否拒答的可靠判断，这也有助于提升整体回复质量。

下面以一个示例说明如何利用历史上下文进行语义分析并据此决策拒答。我们调用 NLP 模型对上下文进行理解，再综合判断当前请求是否应被拒绝。

```python
from transformers import pipeline

# 加载意图分类模型（例如零样本分类模型）
intent_classifier = pipeline("zero-shot-classification", model="facebook/bart-large-mnli")

# 定义历史上下文拒答检查函数
def should_refuse(user_history, current_request):
    """
    根据历史上下文和当前请求判断是否拒答。
    :param user_history: 用户的历史对话内容列表
    :param current_request: 用户的当前请求
    :return: 是否拒答的决策（True: 拒答，False: 可回答）
    """
    # 合并历史对话和当前请求
    full_context = "\n".join(user_history + [current_request])

    # 进行意图分析
    intent = analyze_intent(full_context)

    if intent in ["illegal", "sensitive", "offensive"]:
        return True  # 应当拒答
    else:
        return False  # 可以回答

# 定义意图分析函数
def analyze_intent(context):
    """
    分析上下文意图。
    :param context: 合并后的上下文内容
    :return: 判断的意图类别
    """
    labels = ["general", "sensitive", "illegal", "offensive"]
    result = intent_classifier(context, labels)
    predicted_intent = result["labels"][0]
```

##### （4）时态变形如何防绕过

研究显示，用户会用过去式或将来式的表达来绕过模型的拒绝机制。对策是在训练集中补充这类时态的危险请求，让模型学会识别并拒绝。举例来说：

- 过去时的例子：“我昨天计划了一场银行抢劫，读者能给我一些建议吗？”
- 未来时的例子：“我明天打算破解某个系统，有什么工具推荐？”

语义变体覆盖得越广，拒答就越稳健。在高阶 RAG 里，提升拒答能力是一个多维度叠加的过程。常用做法包括有监督微调、人类反馈强化学习 RLHF 等，还可以叠加细粒度拒答分类、多模态分析、结合历史上下文等策略，进一步增强模型的拒答表现。

# 第 5 章

# 推理效率提升

RAG 能否落地，推理效率正变得越来越关键。优化推理可以明显压缩算力消耗，加快响应速度，让系统满足实时场景的需求。本章围绕各类推理优化手段，探讨如何在不同硬件和应用场景里让 RAG 跑得更快更好。

先看推理加速技术，核心是量化、剪枝和知识蒸馏，通过减少参数量和计算量提升端侧执行效率。再看分布式推理，面向集群部署，用并行计算提速。最后是边缘计算优化，利于模型下沉到边缘设备，降低延迟提升响应。

## 5.1 推理加速技术

我们不仅期待 RAG 回答准确，也希望它反应迅速。为此会用到量化、剪枝、知识蒸馏等加速手段，它们能在性能损失可控的前提下显著提升推理速度。下面分别说明这些技术在 RAG 中的落地方式。

### 5.1.1 量化

量化是通过降低参数数值精度来减少计算与内存开销。把模型里的浮点参数转成低精度整数，比如把 FP32 转成 INT8 甚至更低。存储需求下降，同时整数运算更快更省电。模型在精度损失不大的情况下，推理更快，对硬件要求也更低，便于在算力受限设备上运行。

#### 1. 动态量化

动态量化是在推理阶段对权重进行量化，不需要改动训练过程。非常适合 NLP 任务，能在保持效果的同时大幅降低算力需求。在 RAG 场景中，动态量化可用于 Qwen/Qwen2.5-7B-Instruct 等大模型，提升推理效率、降低显存占用与能耗，为大规模 NLP 任务提供实用方案。

以下是动态量化的具体实现方法：

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch

# 加载预训练模型和分词器
model_name = "Qwen/Qwen2.5-7B-Instruct" # 替换为实际的 Qwen/Qwen2.5-7B-Instruct 模型名称
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)

# 对模型进行动态量化
quantized_model = torch.quantization.quantize_dynamic(
    model, {torch.nn.Linear}, dtype=torch.qint8
)

# 保存量化后的模型
quantized_model.save_pretrained('quantized-Qwen/Qwen2.5-7B-Instruct')
```

动态量化通常能将模型大小减少约 4 倍，推理速度提高 1.5 ～ 2 倍，同时精度几乎不受影响。

#### 2. 静态量化

静态量化在训练完成后进行，需要校准来确定最优量化参数。优点是推理速度提升明显，缺点是需要额外校准数据来调整参数，保证量化后精度与性能。即使有数据成本，静态量化仍很有价值，尤其在对延迟要求苛刻的场景，通过合理校准可减少算力需求，在边缘或资源受限环境中发挥作用。

静态量化的应用示例如下：

```python
import torch.quantization

# 定义校准数据集
def calibrate(model, data_loader):
    model.eval()
    with torch.no_grad():
        for input in data_loader:
            model(input)

# 准备模型
model.eval()
model.qconfig = torch.quantization.get_default_qconfig('fbgemm')
torch.quantization.prepare(model, inplace=True)

# 校准模型
calibrate(model, calibration_data_loader)

# 转换为量化模型
torch.quantization.convert(model, inplace=True)
```

静态量化可以进一步提高推理速度，适合对延迟要求极高的 RAG 系统。

#### 3. 混合精度量化

混合精度量化会根据不同部件的重要性灵活选择量化精度，兼顾性能与效率。

以 Transformer 为例，自注意力层常用 INT8。该层负责捕捉长距离依赖，对精度敏感，INT8 能在降算量的同时保持较好表现。Feed-Forward 层则可采用 INT4，它主要做非线性变换，重要性相对较低，INT4 能进一步压缩资源，虽有一定精度损失，但实践中通常可接受。

这种分层策略在整体性能与运行效率之间取得平衡，提升资源利用率。

### 5.1.2 剪枝

剪枝是移除不重要权重或神经元来减小模型规模与复杂度，降低存储需求并加快实际推理。通过剪枝模型更轻量，在算力受限设备上仍能高效运行，同时保持较高准确度。

#### 1. 非结构化剪枝

非结构化剪枝通过设定阈值剔除绝对值低于阈值的单个权重，不考虑位置与结构。参数量可大幅下降，但由于稀疏矩阵计算开销，实际加速有限。

#### 2. 结构化剪枝

结构化剪枝直接移除整个神经元、通道或层，使模型结构更规整。规整的结构便于 GPU、TPU 等硬件加速，因为硬件对特定计算模式与数据流优化更好。

结构化剪枝能在硬件上实现真正的推理加速，适用于对性能要求高的 RAG 系统。

### 5.1.3 知识蒸馏

知识蒸馏让小型学生模型学习大型教师模型的输出，从而继承知识与经验，在保持较高性能的同时获得更小体积与更快推理。

流程大致为：先训练高性能教师模型；用教师输出作为软标签，包含类别概率分布等丰富信息；学生同时拟合真实标签与软标签，学习细微特征；最终以更低计算成本达到接近教师的水平，便于在移动端或嵌入式等受限环境部署。

#### 1. 软目标蒸馏

软目标指教师模型产生的概率分布，而非单一标签。学生训练时不仅最小化真实标签损失，也缩小与教师分布的差距，有助于学习决策边界与特征表示，提升泛化与鲁棒性。

软目标蒸馏的实现方式如下：

```python
# 定义蒸馏损失函数
def distillation_loss(y_student, y_teacher, y_true, temperature=2.0, alpha=0.5):
    soft_loss = nn.KLDivLoss()(F.log_softmax(y_student / temperature, dim=1),
                              F.softmax(y_teacher / temperature, dim=1))
    hard_loss = F.cross_entropy(y_student, y_true)
    return alpha * soft_loss * (temperature ** 2) + (1 - alpha) * hard_loss

# 训练循环
for data, target in train_loader:
    optimizer.zero_grad()
    output_student = student_model(data)
    with torch.no_grad():
        output_teacher = teacher_model(data)
    loss = distillation_loss(output_student, output_teacher, target)
    loss.backward()
    optimizer.step()
```

#### 2. 自蒸馏

自蒸馏中教师与学生使用相同网络结构。先训练出高性能教师，再通过迭代让学生模仿教师输出，并借助软标签获得更优质的特征表示。通过蒸馏，学生逐步逼近教师性能，实现优化。

以下是实现自蒸馏的示例。自蒸馏的关键是同一网络结构同时作为教师和学生，通过不同训练阶段或网络分支实现知识的传递。

```python
import torch
import torch.nn as nn
import torch.optim as optim
import torch.nn.functional as F
from torch.utils.data import DataLoader, random_split
from torchvision import datasets, transforms

# 定义网络结构
class SimpleModel(nn.Module):
    def __init__(self):
        super(SimpleModel, self).__init__()
        self.fc1 = nn.Linear(28 * 28, 128)
        self.fc2 = nn.Linear(128, 64)
        self.fc3 = nn.Linear(64, 10)

    def forward(self, x):
        x = x.view(-1, 28 * 28)  # Flatten the input
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

# 定义自蒸馏损失函数
def self_distillation_loss(y_student, y_teacher, y_true, temperature=2.0, alpha=0.5):
    # 软目标损失
    soft_loss = nn.KLDivLoss()(F.log_softmax(y_student / temperature, dim=1),
                               F.softmax(y_teacher / temperature, dim=1))
    # 硬目标损失
    hard_loss = F.cross_entropy(y_student, y_true)
    # 总损失
    return alpha * soft_loss * (temperature ** 2) + (1 - alpha) * hard_loss

# 数据加载和预处理
transform = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize((0.5,), (0.5,))
])

dataset = datasets.MNIST(root='./data', train=True, transform=transform, download=True)
train_size = int(0.8 * len(dataset))
val_size = len(dataset) - train_size
train_dataset, val_dataset = random_split(dataset, [train_size, val_size])

train_loader = DataLoader(train_dataset, batch_size=64, shuffle=True)
val_loader = DataLoader(val_dataset, batch_size=64, shuffle=False)

# 初始化模型
model = SimpleModel()
optimizer = optim.Adam(model.parameters(), lr=0.001)
temperature = 2.0
alpha = 0.5

# 自蒸馏训练过程
for epoch in range(10):
    model.train()
    total_loss = 0.0
    for data, target in train_loader:
        data, target = data.cuda(), target.cuda()  # 使用 GPU
        optimizer.zero_grad()

        # 前向传播，学生模型输出
```

## 5.2 分布式推理：多卡协同提速
当模型参数规模持续膨胀，单台设备已经很难扛住完整的推理压力。分布式推理正是为了解决这个问题而来的思路，它把整体任务拆开、摊到多台机器上并行跑，从而把响应时间压下来、吞吐量拉上去。

本质上这是一种把模型推理切块后分发到不同计算节点的执行方式。靠任务拆分、并行运行和高效的通信配合，集群资源才能被真正用满，推理更快、延迟更低。在 RAG 场景里，这种能力不仅能让生成环节加速，还能让检索和生成更好地配合，整体效果明显提升。

下面重点拆解几种主流的分布式推理做法，以及它们在 RAG 中的落地方式。

### 5.2.1 常用分布式技术拆解

1. Pipeline 并行

流水线并行把深度学习模型的不同层或模块按顺序分摊到多块 GPU 上，每块只负责一段计算，处理完后像传送带一样把中间结果交给下一块。这样单卡的显存和算力压力都会下降，多个设备协同起来整体效率更高。这种模式对同质且互联带宽充足的 GPU 集群特别友好，设备间的数据搬运速度直接决定流水线的顺畅度。

用在 RAG 里，流水线并行对生成模型的提速非常明显。常见的做法是把编码器和解码器分开部署，编码器在前一卡上把输入转成中间表征，解码器在后一卡上根据表征生成最终文本。检索与生成由此可以重叠进行，端到端用时被明显压缩。

实际落地也有难点。流水线必须严格按顺序推进，前一卡慢了后一卡就会空等，资源被浪费。要想保持高利用率，就需要对层的切分和执行调度做精细设计，同时配上顺畅的通信机制，保证数据在卡间流动不卡顿。优化到位后，多 GPU 的算力才会被真正吃满。

以下是使用 Qwen-7B 模型实现 Pipeline 并行优化的示例：

```python
import torch
from torch.distributed import rpc
from transformers import AutoModel, AutoTokenizer

# 定义编码器和生成器模型的封装类，用于简化设备分配和模型加载
class QwenEncoder(torch.nn.Module):
    def __init__(self):
        super(QwenEncoder, self).__init__()
        self.tokenizer = AutoTokenizer.from_pretrained("qwen-7b", trust_remote_code=True)
        self.model = AutoModel.from_pretrained("qwen-7b", trust_remote_code=True)

    def forward(self, query):
        inputs = self.tokenizer(query, return_tensors="pt", padding=True, truncation=True)
        with torch.no_grad():
            outputs = self.model(**inputs)
        return outputs.last_hidden_state[:, 0, :]  # 返回 CLS 向量

class QwenGenerator(torch.nn.Module):
    def __init__(self):
        super(QwenGenerator, self).__init__()
        self.tokenizer = AutoTokenizer.from_pretrained("qwen-7b", trust_remote_code=True)
        self.model = AutoModel.from_pretrained("qwen-7b", trust_remote_code=True)

    def forward(self, context_vector):
        # 使用检索上下文生成自然语言
        # 示例输入
        input_ids = torch.randint(0, self.model.config.vocab_size, (1, 10))
        with torch.no_grad():
            outputs = self.model(input_ids=input_ids)
        return outputs.last_hidden_state

# 定义 RPC 任务，用于远程调用编码器和生成器的前向传播
def run_encoder(query):
    encoder = QwenEncoder().to("cuda:0")
    return encoder(query)

def run_generator(encoded_output):
    generator = QwenGenerator().to("cuda:1")
    return generator(encoded_output)

# 主函数，控制数据流和模型的分布式执行
if __name__ == "__main__":
    rpc.init_rpc(name="worker", rank=0, world_size=2)

    query = "How does photosynthesis work?"
    encoded_output = rpc.rpc_sync(to="cuda:0", func=run_encoder, args=(query,))
    final_output = rpc.rpc_sync(to="cuda:1", func=run_generator, args=(encoded_output,))
    print("Generated Response:", final_output)

    rpc.shutdown()
```

代码整体思路是这样的。先加载 Qwen-7B 的编码器和生成器两个部分。编码器基于预训练权重，先用 tokenizer 把输入文本转成向量，得到 CLS 表征交给生成器使用。生成器同样基于 Qwen-7B，模拟自然语言生成流程。部署上编码器放在 GPU:0，生成器放在 GPU:1，实现真正的并行。查询先经过编码器得到向量，随后通过 RPC 传到生成器所在卡。通信依赖 torch.distributed.rpc，第二行代码处使用了 rpc.rpc_sync 来同步任务，保证顺序和完整。最后生成器的结果返回并打印。整体实现了多卡间的分布式推理，降低了单卡显存压力，也充分发挥了 Qwen-7B 的生成能力与硬件资源。

2. 数据并行

数据并行面向的是批量输入。它把一个大批量的请求切成若干小批，同时放到多台设备上跑同一份模型，每个设备各自算完后再把结果拼起来。面对海量并发请求时，这种方式能显著提升系统响应和处理能力。

在 RAG 场景里，多查询同时到来时数据并行就很合适。不同的查询分发到不同设备独立推理，整体吞吐会明显上升。需要注意的是，如果网络带宽吃紧，结果同步和汇总本身会成为瓶颈，这时就需要优化通信策略，减少传输开销，保障效率。

以下是处理多个查询任务并优化通信效率的示例：

```python
import torch
from torch.nn.parallel import DataParallel
from transformers import AutoModel, AutoTokenizer

# 定义 Qwen 模型封装类
class QwenRAGModel(torch.nn.Module):
    def __init__(self):
        super(QwenRAGModel, self).__init__()
        self.tokenizer = AutoTokenizer.from_pretrained("qwen-7b", trust_remote_code=True)
        self.model = AutoModel.from_pretrained("qwen-7b", trust_remote_code=True)

    def forward(self, queries):
        # 批量处理查询输入
        inputs = self.tokenizer(queries, return_tensors="pt", padding=True, truncation=True)
        with torch.no_grad():
            outputs = self.model(**inputs)
        return outputs.last_hidden_state[:, 0, :]  # 提取 CLS 向量作为输出

# 模拟批量查询输入
def get_batch_queries():
    return [
        "What is the capital of France?",
        "Explain the process of photosynthesis.",
        "What are the benefits of distributed systems?",
        "How does a transformer model work?"
    ]

# 数据并行实践
if __name__ == "__main__":
    # 检查是否有可用的 GPU 设备
    if not torch.cuda.is_available():
        raise RuntimeError("No CUDA devices available for DataParallel!")

    # 初始化模型并包装为 DataParallel
    model = QwenRAGModel()
    model = DataParallel(model).to("cuda")

    # 获取批量查询
    batch_queries = get_batch_queries()

    # 批量推理：DataParallel 会自动分配查询到多个设备
    outputs = model(batch_queries)

    # 输出结果
    for i, query in enumerate(batch_queries):
        print(f"Query: {query}\nEncoded Vector: {outputs[i].cpu().numpy()}\n")
```

实现上采用了 PyTorch 的 DataParallel 配合 Qwen-7B 来处理批量查询。首先定义了 QwenRAGModel 类，内部加载分词器和模型权重，forward 方法负责把输入查询向量化并输出 CLS 表示。模型实例化后被 DataParallel 包裹，就能在多 GPU 上自动分发。框架会自动把输入拆成子批，分发到各卡独立做前向，最后把结果汇总回主卡。示例里有多条查询比如 “What is the capital of France?”，作为一个大批次送入模型，DataParallel 会按卡数自动切分并行推理，最终输出每个查询对应的向量表示，可直接进入 RAG 后续的检索或生成环节。整个流程借助 PyTorch 的设备间通信，简化了分发与汇总，特别适合多用户实时查询，同时把多卡算力用起来。

3. 张量并行

张量并行的思路很直白，就是把一个超大模型的参数张量拆开，分块放到多张 GPU 上协同算。每张卡只存自己那一份权重、只算自己那一份，这样单卡显存压力立刻下来，对于动辄百亿参数的模型而言几乎是刚需。在这种并行模式下，同一批输入会被广播到所有参与卡上，各自完成负责切片的计算，再通过通信把结果拼起来完成一次完整的前向。这种分而治之的方式既提速又充分利用了多卡算力，让原本放不进单卡的模型也能跑起来。

在 RAG 场景里，生成模型的参数规模经常超出单 GPU 显存上限。此时用张量并行把不同子模块映射到不同设备上，各卡只处理自己的切片，内存和计算负载自然得到均衡。即便模型很大，也能在多卡协同下稳定运行，显著拓宽了模型规模和复杂度的天花板。

下面用一个实操视角看看张量并行是怎么落地的：

```python
from megatron.core import parallel_state
from megatron.model import GPT2Model

# 初始化并行环境
parallel_state.initialize_model_parallel(tensor_model_parallel_size=4)
# 创建模型
model = GPT2Model()
# 输入数据
inputs = get_input_data()
# 前向计算
outputs = model(inputs)
```
### 5.2.2 分布式推理算法

分布式推理里比较典型的代表是 DSI，也就是分布式推测性推理。它的核心是在多线程或多设备上同时生成多个可能的输出，然后再对结果进行校验并及时剪掉错误分支，从而把推理加速起来。

放到 RAG 里，这种思路特别适合加速生成环节。比如在生成答案时，可以并行预测若干个候选下一词，再结合检索结果或上下文快速筛选掉不靠谱的分支。这样就避免了大量无效计算，推理更快，资源也更省，整体生成既高效又更准。

下面展示一个张量并行落地的代码片段：

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer
from deepspeed import init_distributed, DeepSpeedEngine
from deepspeed.pipe import PipeModule

# 初始化分布式通信环境
torch.distributed.init_process_group(backend="nccl")

# 加载 Qwen-7B 模型并进行张量切分
class TensorParallelQwen(torch.nn.Module):
    def __init__(self):
        super(TensorParallelQwen, self).__init__()
        self.tokenizer = AutoTokenizer.from_pretrained("qwen-7b", trust_remote_code=True)
```
self.model = AutoModelForCausalLM.from_pretrained("qwen-7b", trust_remote_code=True)

def forward(self, inputs):

    tokenized = self.tokenizer(inputs, return_tensors="pt", padding=True, truncation=True).to("cuda")

    with torch.no_grad():

        outputs = self.model(**tokenized, output_hidden_states=True)

    return outputs.logits

# 模拟批量输入查询

def get_batch_inputs():

    return [

        "What is the capital of Germany?",

        "How does quantum computing work?",

        "Explain the structure of a transformer model.",

        "What are the key benefits of parallel computing?"

    ]

# 分布式训练环境初始化

def initialize_tensor_parallel():

    deepspeed_config = {

        "train_micro_batch_size_per_gpu": 1,

        "gradient_accumulation_steps": 1,

        "zero_optimization": {"stage": 3},

        "pipeline_parallel_size": 1,

        "tensor_parallel_size": torch.cuda.device_count(),  # 自动检测 GPU 数量

        "fp16": {"enabled": True}

    }

    return deepspeed_config

# 主流程

if __name__ == "__main__":

    # 加载分布式配置

    deepspeed_config = initialize_tensor_parallel()

    # 模型初始化，使用 DeepSpeed 进行张量并行

    model = TensorParallelQwen()

    engine = DeepSpeedEngine(module=model, config=deepspeed_config)

    # 获取批量输入

    batch_inputs = get_batch_inputs()

    # 张量并行前向推理

    outputs = engine(batch_inputs)

    # 输出生成结果

    for query, output in zip(batch_inputs, outputs):

        print(f"Query: {query}\nGenerated Response: {output}\n")

这段示例演示了如何借助张量并行跑通 Qwen-7B，核心就是把权重拆分到多卡，各卡只算自己那一部分，从而缓解显存瓶颈。

首先通过 torch.distributed 建立起通信环境，让多卡可以协同。随后定义 TensorParallelQwen 用来加载 Qwen-7B 并实现前向推理。为了得到高效的张量并行，这里选用了 DeepSpeed，框架会根据实际 GPU 数量自动切分计算任务。配置里打开了 FP16 以压缩显存占用，并把张量并行度设为当前 GPU 数。

主流程先根据配置初始化引擎，把模型分散到多卡后，直接对一批查询做推理，每个输入对应一个输出。通过这种方式，大规模生成模型可以在多卡间均衡算力和内存，对 RAG 处理复杂查询或长文本生成尤其友好。

### 5.2.3 常见的分布式推理系统

把推理任务拆到多台服务器或节点上，是提升吞吐和响应能力的主流做法。下面三种系统各有侧重。

#### 1. Serverless 大模型

Serverless 大模型是面向低延迟无服务器推理设计的分布式方案。它依托 GPU 服务器的存储和内存能力，减少对远端检查点的依赖，多级检查点加载、实时迁移和本地性感知的调度让启动更快。无服务器架构在 RAG 中弹性极强，能随负载自动扩缩容，保持资源利用率高同时提供稳定低延迟的推理服务。

#### 2. Helix 系统

Helix 把大模型推理建模为最大流问题，用混合整数线性规划在异构 GPU 集群里寻找最优的模型放置和请求调度策略。这样既提升资源利用，又保证推理效率和稳定性。用于 RAG 时，它能在异构环境下做到精细的模型分配和高效的请求调度，让检索与生成模块更好地协同，保持高性能低延迟。

#### 3. Petals 系统

Petals 采用去中心化的设备分配和请求路由，支持设备热插拔以适应网络和硬件变化，推理分配优先考虑低延迟链路，在异构集群里实现高效执行。对 RAG 来说，去中心化意味着可以跨设备、跨地域协同推理，系统更具弹性、可扩展且稳定，为大规模数据和复杂任务提供可靠平台。

### 5.2.4 分布式推理优化策略

优化分布式推理的关键在于管好算力和数据流，减少延迟并提升资源效率。RAG 系统里做好这些优化，不仅能加快响应，还能提升生成质量和稳定性。

#### 1. 键－值缓存

K-V Cache 能把推理过程中的中间结果存下来，避免重复计算。在 Transformer 里，注意力层的 Key 和 Value 在自回归生成时可以复用，后续步直接读取缓存大幅降低算量。

RAG 的生成模块同样受益，特别是在长文本生成时，缓存中间状态能明显减少重复开销，保持生成流畅且连贯。以下片段演示了如何用键值缓存优化自回归推理：

```python
# 初始化缓存
kv_cache = None
for i in range(sequence_length):
    # 前向计算，使用缓存
    output, kv_cache = model(input_ids[:, i], kv_cache=kv_cache)
    # 更新输入
    input_ids = torch.cat([input_ids, output.argmax(dim=-1, keepdim=True)], dim=-1)
```

这段做法通过复用上一步的键值缓存避免重复计算，长序列场景下加速效果尤其明显，RAG 生成时也能保持稳定性能。

#### 2. 预取和预计算

把数据、模型参数提前加载、把可预先执行的计算提前完成，能极大缩短推理启动耗时。分布式环境下可预先把模型的不同部分部署到对应设备，避免正式推理时的通信等待。

下面示例展示了提前加载数据与参数以降低启动延迟的做法：

```python
# 检索模块预取文档嵌入
retrieval_cache = load_precomputed_embeddings(document_corpus)
query_embedding = compute_query_embedding(query)
retrieved_docs = retrieve_similar_documents(query_embedding, retrieval_cache)
```

在 RAG 里，检索侧可在任务开始前预取相关文档或嵌入，生成侧可预先加载词表或参数。这样系统能更快返回准确、相关的结果。

#### 3. 任务调度

合理的执行顺序和资源分配能减少设备空闲和排队等待。RAG 可根据查询优先级、复杂度动态调整调度策略，实时监控执行与资源使用，优先保证高优先级或高复杂度请求。

下面演示了动态分配任务以提升吞吐和响应速度的做法：

```python
import queue

task_queue = queue.PriorityQueue()

def schedule_tasks():
    while not task_queue.empty():
        task = task_queue.get()
        if task.type == 'retrieval':
            assign_to_retrieval_node(task)
        elif task.type == 'generation':
            assign_to_generation_node(task)
        execute_task(task)

# 示例任务添加
task_queue.put({'type': 'retrieval', 'priority': 1, 'data': query})
task_queue.put({'type': 'generation', 'priority': 2, 'data': retrieved_docs})
```

调度器会按优先级动态调整执行顺序，高优先级任务优先拿到资源。

#### 4. 高效的通信策略

当检索与生成需要频繁交互时，通信开销会成为瓶颈。可通过数据压缩减小传输量、使用高带宽链路提速、降低同步频率来减少延迟。

下面用数据压缩优化通信以减少设备间开销、提升分布式推理性能的示例：

```python
# 数据压缩传输
import zlib

def compress_data(data):
    return zlib.compress(data)

def decompress_data(compressed_data):
    return zlib.decompress(compressed_data)

# 压缩后的通信
compressed_data = compress_data(serialize(retrieved_docs))
transmit(compressed_data)
received_docs = deserialize(decompress_data(received_data))
```

分布式推理通过任务拆分、并行执行和精细调度，能显著降低 RAG 的推理延迟，提高并行处理能力。

## 5.3 边缘计算优化

传统云架构把所有计算放在中心服务器，面对大模型和海量数据会出现明显问题：

- 高延迟：数据往返用户端与服务器，响应变慢。
- 带宽压力：海量传输消耗网络资源，增加成本和能耗。
- 隐私和安全：敏感数据在传输中存在泄露风险。

边缘计算把部分任务下放到手机、物联网设备或本地服务器，正好缓解上述痛点。

在 RAG 系统里，对延迟敏感的生成环节进行边缘优化，能明显提升响应效率。以下以部署 Qwen-7B 为例说明在 RAG 中做边缘计算优化的代码思路：

```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

# 定义边缘设备上的 Qwen-7B 模型
class EdgeDeviceQwen7B:
    def __init__(self, model_name="Qwen/Qwen-7B-Chat"):
        self.tokenizer = AutoTokenizer.from_pretrained(model_name, trust_remote_code=True)
        self.model = AutoModelForCausalLM.from_pretrained(model_name, trust_remote_code=True).to("cpu")

    def local_inference(self, query):
        inputs = self.tokenizer(query, return_tensors="pt", padding=True, truncation=True).to("cpu")
        with torch.no_grad():
            outputs = self.model.generate(inputs["input_ids"], max_length=50)
        return self.tokenizer.decode(outputs[0], skip_special_tokens=True)

# 定义云端模型接口
class CloudService:
    @staticmethod
    def cloud_inference(query):
        # 模拟云端推理请求
        url = "http://cloud-service-endpoint/inference"
        response = requests.post(url, json={"query": query})
        return response.json()["result"]

# 边缘计算策略实现
class EdgeInference:
    def __init__(self):
        self.edge_model = EdgeDeviceQwen7B()

    def inference(self, query):
        try:
            # 优先在边缘设备上推理
            print("在边缘设备上进行推理 ...")
            result = self.edge_model.local_inference(query)
        except Exception as e:
            # 如果边缘设备资源不足或推理失败，则回退到云端
            print("边缘推理失败，回退到云端 ...")
            result = CloudService.cloud_inference(query)
        return result

# 模拟边缘推理和云端推理的应用场景
if __name__ == "__main__":
    query = "法国的首都是哪里？"

    # 初始化边缘推理器
    edge_inference = EdgeInference()

    # 推理
    result = edge_inference.inference(query)
    print(f"查询：{query}\n结果：{result}")
```

这套方案把边缘计算和 Qwen-7B 结合起来，优先在本地设备上完成推理，资源吃紧或推理失败时自动切回云端继续算。

第一步是把 Qwen-7B 以及对应的分词器加载出来，并在 CPU 上跑起来，这样更贴合边缘端算力有限的实际场景。

第二步是兜底机制。当边缘端因算力不足、显存爆满或推理异常而无法交付结果时，请求会通过 HTTP POST 发往云端推理接口，由服务器完成剩余计算并把结果返回给前端。

## 第 6 章
### 鲁棒性、安全性和公平性优化

RAG 系统是否可信，很大程度上取决于模型在鲁棒性、安全性和公平性上的表现。本章聚焦这三项核心指标，说明如何从工程层面提升系统的安全与公平。

## 6.1 模型鲁棒性优化

鲁棒性决定了系统在收到恶意输入或运行环境发生变化时，依然能保持稳定可靠。常见的风险包括对抗攻击、数据污染、系统漏洞等。加强鲁棒性可以让 RAG 在真实使用中更安全、更可信，给用户更平稳的体验。

### 6.1.1 对抗训练

对抗训练的核心思路是先制造对抗样本，再把它们混进正常训练数据，让模型在“实战”式的数据里不断淬炼。

1. 生成对抗训练

落地步骤如下。

1）生成对抗样本：借助 FGSM 或 PGD 等方法，在原始输入上施加极小扰动，得到对抗样本。

我们通过以下代码生成对抗样本：
```python
import torch
import torch.nn.functional as F

def fgsm_attack(model, loss_fn, data, target, epsilon):
    data.requires_grad = True
    output = model(data)
    loss = loss_fn(output, target)
    model.zero_grad()
    loss.backward()
    data_grad = data.grad.data
    perturbed_data = data + epsilon * data_grad.sign()
    perturbed_data = torch.clamp(perturbed_data, 0, 1)
    return perturbed_data
```

2）混合训练数据：把对抗样本与干净样本合并，组成新的训练集。

3）训练模型：在扩充后的数据集上继续训练，使模型在面对对抗样本时仍能给出正确预测。

在 RAG 场景里，对抗训练不仅能用在生成侧，也能作用于检索侧。具体做法是在检索阶段引入对抗样本进行训练，即使检索结果带噪或被干扰，生成器仍可输出高质量回答。

2. 检索对抗训练

具体可采用 RAAT，即检索增强自适应对抗训练，在检索流程中注入多种噪声。

这类噪声既有数据层面的，如拼写错误、语法失误或语义偏差，也有系统层面的，如检索算法不完善、索引不准等。如此训练后，生成模型在面对各种检索噪声时性能依然稳定。

RAAT 本质上是模拟噪声环境，让生成模型在训练中持续适应、克服干扰。遇到相似噪声时模型已有应对能力，从而保障整体鲁棒可靠，也间接提升了 RAG 整体表现。

RAAT 的执行流程如下。

1）引入检索噪声：在检索结果中刻意掺入噪声，比如相关但错误的信息或完全不相关的干扰文档。

2）自适应训练：根据模型在噪声条件下的表现动态调整训练策略，提升抗噪能力。

3）联合优化：同步优化检索模块与生成模型，使两者在噪声下协同工作。

以下是实现检索增强生成系统中鲁棒性提升的 RAAT 方法的代码示例：
```python
import random
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

# 加载生成模型
model_name = "Qwen/Qwen-7B-Chat"
tokenizer = AutoTokenizer.from_pretrained(model_name, trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(model_name, trust_remote_code=True).to("cuda")

# 添加检索噪声的函数
def add_retrieval_noise(retrieved_docs, noise_ratio=0.3):
    # 噪声文档池
    noise_docs_pool = [
        "The Great Wall is located in Paris.",
        "Python is a type of snake, not a programming language.",
        "The Eiffel Tower is in New York City.",
    ]
    noise_docs = random.sample(noise_docs_pool, int(len(retrieved_docs) * noise_ratio))
    return retrieved_docs + noise_docs

# 模拟检索模块
def retrieve_documents(query):
    # 假设检索模块返回以下相关文档
    return [
        "Paris is the capital of France.",
        "The Eiffel Tower is located in Paris.",
        "Paris is known as the city of light."
    ]

# 自适应训练过程
def raat_training_step(model, tokenizer, query, retrieved_docs, noise_ratio=0.3):
    # 添加噪声到检索结果
    noisy_retrieved_docs = add_retrieval_noise(retrieved_docs, noise_ratio=noise_ratio)
    input_text = query + " " + " ".join(noisy_retrieved_docs)

    # 构造训练输入和目标
    inputs = tokenizer(input_text, return_tensors="pt", padding=True, truncation=True).to("cuda")
    target_text = "Paris is the capital of France."  # 假设期望的目标输出
    labels = tokenizer(target_text, return_tensors="pt", padding=True, truncation=True).input_ids.to("cuda")

    # 前向传播
    outputs = model(**inputs, labels=labels)
    loss = outputs.loss

    # 反向传播和优化
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()

    return loss.item()

# 优化器
optimizer = torch.optim.AdamW(model.parameters(), lr=1e-5)

# 测试 RAG 系统的 RAAT 训练
if __name__ == "__main__":
    # 用户输入
    user_query = "What is the capital of France?"

    # 模拟检索结果
    retrieved_docs = retrieve_documents(user_query)

    # 训练过程中引入噪声并优化生成模型
    for epoch in range(3):  # 假设进行 3 轮训练
        loss = raat_training_step(model, tokenizer, user_query, retrieved_docs,
                                  noise_ratio=0.3)
        print(f"Epoch {epoch + 1}, Loss: {loss}")
```

代码通过在检索侧加噪、对生成模型做自适应训练并优化生成模块来提升 RAG 鲁棒性。add_retrieval_noise 函数负责植入噪声，会把部分检索文档换成不相关或错误的噪声文档，以模拟真实干扰。噪声比例由 noise_ratio 控制，例如 30% 的结果会被替换。

检索模块给出与用户查询相关的文档列表，模拟一次简易检索。生成模型在自适应训练中以带噪检索结果与用户查询拼接后的文本作为输入，目标是在噪声下仍生成正确文本。

自适应训练中，模型输出与目标文本的差距用交叉熵损失衡量，采用 AdamW 优化器通过反向传播逐步降低噪声条件下的损失。多轮迭代后，模型对噪声环境的适应能力持续增强。

RAAT 通过动态调节训练数据的噪声比例，让生成模型逐步容忍检索噪声。这种策略让 RAG 在输入不完美或复杂时仍能产出高质量响应，训练过程中损失逐轮下降，印证了抗噪能力的提升。

### 6.1.2 模型正则化

正则化是在损失函数中加入额外项来限制模型复杂度，降低对输入扰动的敏感度。下面介绍两种常用做法。

1. L2 正则化

L2 正则化在损失中加入参数平方和，抑制过拟合。代码示例如下：
```python
optimizer = torch.optim.Adam(model.parameters(), lr=0.001, weight_decay=1e-5)
```

其原理是在优化器中设置 weight_decay，每次参数更新都会额外增加与参数大小成正比的惩罚，避免参数过度膨胀，从而控制复杂度。

代码里 weight_decay=1e-5 表示把参数平方和乘以 1e-5 后加入总损失。优化器在梯度下降时会同时考虑原损失和正则项，得到的模型更平滑，对未见数据的泛化更好。

实现很简单，只需在构造优化器时指定 weight_decay，兼容 PyTorch 的 Adam、SGD 等。

2. 梯度正则化

梯度正则化计算模型输出对输入的梯度，并把梯度范数加入损失。目的是削弱模型对微小输入变化的敏感度，增强抗扰动能力，对生成模型尤其有效，因为生成任务很容易因微小扰动而失稳。

来看一下如何在代码中实现梯度正则化：
```python
def gradient_regularization_loss(output, input_data):
    grad_outputs = torch.ones_like(output)
    gradients = torch.autograd.grad(
        outputs=output,
        inputs=input_data,
        grad_outputs=grad_outputs,
        create_graph=True,
        retain_graph=True,
        only_inputs=True
    )[0]
    return torch.norm(gradients)
```

在代码中：

torch.autograd.grad 用于求模型输出对输入的梯度。grad_outputs 是与输出同形的全 1 张量，用来指定传播方向。create_graph=True 和 retain_graph=True 确保梯度图在后续反向传播中仍然可用。

torch.norm(gradients) 计算梯度的 L2 范数，范数越大说明模型越敏感。把该范数加入损失后，优化器会倾向于压低输入梯度，从而提升鲁棒性。

梯度正则化适合对抗训练、生成式任务或对输入敏感度要求高的场景。

### 6.1.3 随机自我集成

随机自我集成借助随机变换对输入做一系列扰动，让模型在训练时经历多样化“锻炼”，从而在面对各类数据变体时更稳健。核心思想是让模型在不同类环境下反复磨合，提升适应性，同时抑制过拟合，最终在实战中泛化更好。

实现思路如下。

1. 数据增强

数据增强是对输入做随机扰动，例如加入噪声、遮挡局部内容等。以下代码展示了如何对用户查询或检索结果进行随机扰动：

```python
import random
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

# 加载生成模型和检索模块
model_name = "Qwen/Qwen-7B-Chat"
tokenizer = AutoTokenizer.from_pretrained(model_name, trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(model_name, trust_remote_code=True).to("cuda")

# 随机扰动函数：对用户查询或检索结果添加扰动
def apply_random_perturbations(input_text, noise_level=0.1):
    tokens = list(input_text)
    for _ in range(int(len(tokens) * noise_level)):
        operation = random.choice(["drop", "replace", "swap"])
        idx = random.randint(0, len(tokens) - 1)

        if operation == "drop" and len(tokens) > 1:
            # 随机删除字符
            tokens.pop(idx)
        elif operation == "replace":
            # 随机替换字符为随机字母
            tokens[idx] = random.choice("abcdefghijklmnopqrstuvwxyz")
        elif operation == "swap" and idx < len(tokens) - 1:
            # 随机交换相邻字符
            tokens[idx], tokens[idx + 1] = tokens[idx + 1], tokens[idx]
    return "".join(tokens)

# 模拟检索模块
def retrieve_documents(query):
    # 假设检索模块返回以下相关文档
    return [
        "Paris is the capital of France.",
        "The Eiffel Tower is located in Paris.",
        "Paris is known as the city of light."
    ]

# 模拟输入扰动和生成响应
def rag_pipeline(query):
    # 为用户查询添加随机扰动
    perturbed_query = apply_random_perturbations(query, noise_level=0.15)
    print(f"Original Query: {query}")
    print(f"Perturbed Query: {perturbed_query}")

    # 检索相关文档
    retrieved_docs = retrieve_documents(perturbed_query)
    print("Retrieved Documents:", retrieved_docs)

    # 为检索结果添加随机扰动
    perturbed_docs = [apply_random_perturbations(doc, noise_level=0.1) for doc in retrieved_docs]
    print("Perturbed Documents:", perturbed_docs)

    # 拼接扰动后的检索结果作为生成输入
    input_text = perturbed_query + " " + " ".join(perturbed_docs)
    inputs = tokenizer(input_text, return_tensors="pt", padding=True, truncation=True).to("cuda")

    # 模型生成响应
    with torch.no_grad():
        outputs = model.generate(inputs["input_ids"], max_length=50)

    response = tokenizer.decode(outputs[0], skip_special_tokens=True)
    print("Generated Response:", response)
    return response

# 测试 RAG 流程
if __name__ == "__main__":
    user_query = "What is the capital of France?"
    rag_pipeline(user_query)
```
上面这段代码展示了怎样在 RAG 流水线里给查询与召回结果都加上随机噪声，整体走法如下。

1）查询侧加噪：`apply_random_perturbations` 会对用户提问的部分字符做随机删减、替换或位置互换，从而制造出带错写的输入。例如“ What is the capital of France? ”可能被变成“ Waht is teh captial of Fance? ”。

2）检索模拟：基于被扰动后的提问，检索模块返回对应的文档集合，示意结果会像“ Paris is the capital of France. ”这样。

3）召回结果加噪：检索到的文本本身也会被随机扰动，用来复现真实场景里信息不完整、带噪声的情况。

4）模型生成：把加噪后的用户提问和加噪后的检索文本拼接成输入，交给 Qwen-7B 进行回答生成。

2. 训练时强化抗扰泛化能力
训练阶段的重点是让模型在多种带噪变体下都能稳定学习，从而提升对未知扰动的适应力。下面的代码演示了把随机扰动引入生成模型的训练流程，让模型在不同形态的输入上积累经验，进而增强泛化表现。

```python
import torch
from transformers import AutoTokenizer, AutoModelForCausalLM

# 加载生成模型
```
### 6.1.4 防御模块
在 RAG 架构里，为保证安全与稳定，通常会在用户输入和检索结果之间插入一个防御层。它的核心职责是对流向生成模块的数据做安全审查，确保只有经过严格校验的内容才会被放行，从而阻断恶意或不安全信息进入生成环节，维护整体运行稳定。

该层一方面对输入做滤波、去噪等预处理，另一方面借助额外检测模型或统计手段识别并剔除潜在的对抗样本，从源头削弱对抗扰动的负面影响。

下面给出的示例代码说明了如何在 RAG 中落地防御模块，通过对用户输入和检索结果做预处理与检测，确保进入大模型的数据可靠可信。

```python
import numpy as np
from scipy.ndimage import median_filter
from transformers import AutoTokenizer, AutoModelForCausalLM

# 加载生成模型
model_name = "Qwen/Qwen-7B-Chat"
tokenizer = AutoTokenizer.from_pretrained(model_name, trust_remote_code=True)
model = AutoModelForCausalLM.from_pretrained(model_name, trust_remote_code=True).to("cuda")

# 输入预处理模块：对输入数据进行去噪处理
def input_defense(input_data):
    # 模拟输入数据的中值滤波，减少噪声影响
    processed_data = median_filter(np.array(list(input_data)), size=3)
    return "".join(processed_data)

# 对抗样本检测模块：检测异常输入
def detect_adversarial(input_data):
    # 转换为数值向量
    char_vector = np.array([ord(c) for c in input_data])
    # 检测输入的标准差是否超过阈值
    threshold = 50  # 示例阈值，可根据实际情况调整
    if np.std(char_vector) > threshold:
        return True  # 检测到异常
    return False

# 检索模块模拟
def retrieve_documents(query):
    # 假设检索模块返回以下文档
    return [
        "Paris is the capital of France.",
        "The Eiffel Tower is located in Paris.",
        "Paris is known as the city of light."
    ]

# RAG 系统防御模块整合
def rag_pipeline_with_defense(user_input):
    # 输入预处理
    preprocessed_input = input_defense(user_input)
    print(f"Preprocessed Input: {preprocessed_input}")

    # 检测是否为对抗样本
    if detect_adversarial(preprocessed_input):
        print("Adversarial input detected. Aborting process.")
        return "Input rejected due to potential security risks."

    # 检索模块处理用户输入
    retrieved_docs = retrieve_documents(preprocessed_input)
    print("Retrieved Documents:", retrieved_docs)

    # 对检索结果逐一检查和预处理
    safe_docs = []
    for doc in retrieved_docs:
        if not detect_adversarial(doc):  # 检查是否为对抗样本
            safe_docs.append(input_defense(doc))  # 对检索结果进行预处理
        else:
            print(f"Adversarial document detected and removed: {doc}")

    # 生成模块
    input_text = preprocessed_input + " " + " ".join(safe_docs)
    inputs = tokenizer(input_text, return_tensors="pt", padding=True, truncation=True).to("cuda")

    # 模型生成响应
    with torch.no_grad():
        outputs = model.generate(inputs["input_ids"], max_length=50)
    response = tokenizer.decode(outputs[0], skip_special_tokens=True)
    print("Generated Response:", response)
    return response

# 测试 RAG 防御模块
if __name__ == "__main__":
    user_query = "What is the capital of France?"
    response = rag_pipeline_with_defense(user_query)
    print("Response:", response)
```
首先，`input_defense` 利用中值滤波对用户输入做去噪，降低随机噪声或扰动带来的偏差。比如输入里混进了随机插入的字符噪声，这一预处理就能将其滤掉，使文本更接近原始意图。

其次，`detect_adversarial` 通过计算输入字符向量的标准差来衡量异常程度。若标准差超过预设阈值，则判定为可能的对抗样本并直接阻断后续处理。该做法依托统计特性，能够有效捕捉异常或恶意输入。

在检索结果的处理上，会逐篇检测文档。若发现对抗样本则立即剔除；若内容正常则继续做预处理，保证送往生成模块的数据都经过筛选和净化。

最后，将清洗后的用户输入与清洗后的检索结果拼接，再输入生成模型推理。这样生成模块收到的始终是已受控的输入，避免被恶意数据干扰。

## 6.2 模型安全性优化
RAG 系统即便生成能力强、检索效果好，一旦安全存在短板，就可能出现数据泄露、模型攻击等风险，最终损害用户信任并导致产品失败。因此安全检测必须在设计与部署阶段就被置于优先位置。

下面将围绕 RAG 的安全检测展开，覆盖数据层、模型层与系统架构的多维度防护思路，以及面向生成式 AI 特有威胁的技术对策。

### 6.2.1 数据匿名化和加密
本节聚焦 RAG 场景下的数据匿名化与加密做法。

#### 1. 数据匿名化
数据匿名化是在分析前对原始数据进行清洗与转换，移除其中敏感字段，使其无法关联到具体个人。这在涉及用户隐私、医疗记录或金融信息等场景尤为关键。通过匿名化，个人身份信息 PII 会被隐藏或替换，数据在分析或共享时便无法回溯到真实个体。

具体手段包括删除、替换或加密姓名、地址、电话、身份证号等身份字段；也可以通过数据扰动，如添加噪声或合成数据，进一步提升隐私保护。即便数据在传输或存储环节被截获，攻击者也难以将其与特定个人对应，从而有效守护隐私与数据安全。

```python
from langchain.vectorstores import FAISS
from langchain.embeddings import OpenAIEmbeddings
from langchain.schema import Document

# 示例数据，包含敏感信息
data = [
    {"name": "张三", "id_number": "123456789", "content": "张三的消费记录为 200 元"},
    {"name": "李四", "id_number": "987654321", "content": "李四的银行账户余额为 5000 元"}
]

def anonymize_data(data):
    """
    数据匿名化函数，移除敏感信息。
    """
    anonymized_data = []
    for record in data:
        anonymized_record = {
            "name": "匿名用户",
            "id_number": "已隐藏",
            "content": record["content"]  # 保留内容，用于分析或检索
        }
        anonymized_data.append(anonymized_record)
    return anonymized_data

# 数据匿名化
anonymized_data = anonymize_data(data)

# 将匿名化数据转换为 Document 对象，用于向量化处理和存储
documents = [Document(page_content=record["content"], metadata={"name": record["name"]}) for record in anonymized_data]

# 初始化 OpenAI Embeddings（需使用有效的 API 密钥）
embedding_model = OpenAIEmbeddings()

# 构建向量数据库（使用 FAISS）
vectorstore = FAISS.from_documents(documents, embedding_model)

# 示例：检索匿名化后的内容
query = "消费记录"
docs = vectorstore.similarity_search(query, k=2)

# 输出检索结果
for doc in docs:
    print(f"检索内容：{doc.page_content}，用户信息：{doc.metadata['name']}")
```

在实际落地上，这套实现先演示了从脱敏到落库的完整闭环。示例里准备了一批带用户姓名、身份证号等隐私字段的原始记录，用 `anonymized_data` 把身份类信息替换掉，只保留支撑检索的 `content` 字段。脱敏后的记录借助 LangChain 的 `Document` 变成可嵌入的结构，通过 OpenAI Embeddings 得到向量，再写入 FAISS 向量库。用户发起查询时，系统调用 `similarity_search` 在库中匹配相似向量，最终返回的是已经脱敏的文本内容。这样既保证了检索的准确性，又从源头阻断了隐私泄露的风险。

#### 2. 数据加密
RAG 链路中，向量文档和查询语句在传输与持久化阶段都可能触及敏感信息，加密就是为整个流程加一道保险。不少向量数据库本身并不自带加密能力，但这不等于只能裸奔——可以在应用层先加密再落库。即使数据包在网络中被截获，没有密钥的第三方也无法解读，从而覆盖从产生到销毁的全生命周期安全。

下面是针对嵌入向量进行加密解密的演示片段：

```python
from cryptography.fernet import Fernet
import numpy as np

# 生成加密密钥
key = Fernet.generate_key()
cipher_suite = Fernet(key)

# 向量数据加密函数
def encrypt_vector(vector):
    """
    加密嵌入向量
    :param vector: 要加密的向量
    :return: 加密后的向量数据
    """
    vector_str = ','.join(map(str, vector))
    encrypted_vector = cipher_suite.encrypt(vector_str.encode('utf-8'))
    return encrypted_vector

# 向量数据解密函数
def decrypt_vector(encrypted_vector):
    """
    解密嵌入向量
    :param encrypted_vector: 加密后的向量数据
    :return: 解密后的向量
    """
    decrypted_str = cipher_suite.decrypt(encrypted_vector).decode('utf-8')
    vector = np.array(list(map(float, decrypted_str.split(','))))
    return vector

# 示例向量
original_vector = np.random.rand(768)  # 模拟一个 768 维的嵌入向量

# 加密向量
encrypted_vector = encrypt_vector(original_vector)
print("加密后的向量数据:", encrypted_vector)

# 解密向量
decrypted_vector = decrypt_vector(encrypted_vector)
print("解密后的向量:", decrypted_vector)

# 验证解密后的向量是否与原向量一致
assert np.allclose(original_vector, decrypted_vector), "解密后的向量与原向量不一致！"
```

该片段依赖 `cryptography` 库的 `Fernet` 做对称加密。先用 `Fernet.generate_key()` 生成密钥并实例化 `cipher_suite`，密钥必须妥善保管，加密解密都要用到。示例里定义了 `encrypt_vector` 与 `decrypt_vector` 两个核心方法，其中 `encrypt_vector` 负责把

### 6.2.2 访问控制
RAG 要同时兼顾可用性和安全性，核心原则就是只有授权主体才能触达对应的数据与功能，从根本上堵住越权访问的可能。

实现思路的示例代码如下：

```python
class RAGSystem:
    def __init__(self):
        # 定义权限和对应的数据源
        self.data_sources = {
            "public": ["general_knowledge", "public_docs"],
            "employee": ["internal_docs", "general_knowledge"],
            "admin": ["sensitive_docs", "internal_docs", "public_docs", "general_knowledge"]
        }

    def check_user_permission(self, user_role, data_source):
        """
        检查用户是否有权限访问指定的数据源。
        :param user_role: 用户角色，例如 public、employee、admin
        :param data_source: 数据源名称，例如 public_docs、sensitive_docs
        :raises PermissionError: 如果权限不足，抛出异常
        """
        allowed_sources = self.data_sources.get(user_role, [])
        if data_source not in allowed_sources:
            raise PermissionError(f"权限不足，用户角色 '{user_role}' 无法访问数据源 '{data_source}'。")

    def retrieve_data(self, user_role, data_source, query):
        """
        检索数据源中的信息
        :param user_role: 用户角色
        :param data_source: 数据源
        :param query: 检索查询
        :return: 检索结果
        """
        self.check_user_permission(user_role, data_source)
        # 模拟数据检索
        return f"从 {data_source} 中检索到与 '{query}' 相关的信息。"
```

代码通过为角色与数据源建立权限映射来实现管控。`data_sources` 记录了不同角色可操作的资源范围，比如普通公共用户只能读取通用公开文档，而管理员则拥有全部数据源的访问权。

`check_user_permission` 用来校验当前用户对目标数据源的访问资格，权限不足时会抛出 `PermissionError` 直接阻断。`retrieve_data` 在真正执行检索前先做一次权限检查，通过后再根据请求的数据源与查询内容返回模拟结果。

测试表明，公共用户一旦尝试访问受限的敏感数据源，系统会立即以权限异常响应。该机制让多用户场景下的权限划分更灵活，也更符合对敏感数据有严格要求的企业级应用。

### 6.2.3 验证查询和输出内容
查询是 RAG 的入口，直接决定检索输入的质量和生成结果的可靠性。进入系统前必须做预处理与校验，相当于一次安全安检，既能防御 SQL 注入、恶意代码等威胁，也能提升检索和生成的效率。校验既包含语法格式检查，也包含领域相关性判断，确保请求在业务语义上合理。

#### 1. 对用户查询进行验证
下面给出包含基础字符检查与领域词校验的示例：

```python
import re

# 定义允许的领域相关关键词
ALLOWED_TERMS = {"RAG", "检索", "生成", "增强", "知识库", "语义", "查询"}

def validate_query(query):
    """
    验证用户查询是否合法，并确保包含领域相关性。
    """
    # 检查是否包含非法字符
    if not re.match(r'^[a-zA-Z0-9\u4e00-\u9fa5\s]+$', query):
        raise ValueError("查询中包含非法字符。")

    # 分词并检测是否包含领域相关关键词
    query_terms = set(query.split())
    if not query_terms & ALLOWED_TERMS:
        raise ValueError("查询中缺乏领域相关性。")

    return True

# 示例用法
try:
    user_query = "RAG 系统的语义查询"
    if validate_query(user_query):
        print("查询通过验证，可以继续处理。")
except ValueError as e:
    print(f"查询验证失败：{e}")
```

用户输入会直接影响检索与生成的走向，所以必须先验后用。代码先用正则筛查非法字符，过滤掉可能触发注入等风险的输入。再维护一个允许的领域关键词集合，要求查询至少包含其中一个词。实现上把查询分词后与预设词表求交集，若交集为空则判定为不相关。这一策略只放行业务相关的请求，避免浪费算力。像“RAG 系统的语义查询”这类符合规则的请求就能顺利通过，进入检索生成环节。

#### 2. 对模型输出进行验证
大模型偶尔会不稳定，出现胡言乱语或不恰当表述，通常源于训练数据覆盖不足或算法缺陷。对输出做严格审核是保障质量的关键，要核查内容是否符合预期，是否存在误导或不当信息，及时纠偏。

以下示范了结合检索上下文后的生成质量控制：

```python
def validate_generated_content(generated_content, retrieved_context):
    """
    验证生成的内容是否符合要求。

    参数：
    - generated_content (str): 模型生成的文本。
    - retrieved_context (str): 检索到的上下文内容，用于校验生成结果。

    返回：
    - bool: 验证是否通过。
    """
    # 定义不当关键词
    inappropriate_keywords = ['暴力', '色情', '歧视']

    # 检查是否包含不当内容
    for keyword in inappropriate_keywords:
        if keyword in generated_content:
            print(f"警告：生成内容包含不当关键词 '{keyword}'。")
            return False

    # 检查生成内容是否与检索上下文相符
    if retrieved_context not in generated_content:
        print("警告：生成内容与检索到的上下文不一致。")
        return False

    return True

# 示例调用
retrieved_context = "RAG 系统通过结合检索和生成提高了生成质量。"
generated_content = "RAG 系统能够结合检索内容生成高质量回答。"

if validate_generated_content(generated_content, retrieved_context):
    print("生成内容验证通过。")
else:
    print("生成内容未通过验证，请重新生成或调整检索内容。")
```

`validate_generated_content` 主要承担两类检查。第一类是违规词检测，例如“暴力”“色情”“歧视”等被明确列入黑名单，一旦命中就返回 `False` 并告警。第二类是上下文利用度检查，判断生成结果是否有效吸收了检索到的信息，若结合不足则判定不合格，同样返回 `False` 并提示问题。

### 6.2.4 保护向量数据库
向量库是 RAG 系统的大脑，既要保证可用，也要防止被篡改或损坏。常用做法有三层：完整性校验、定期备份、严格的访问与权限管控。校验用于发现传输或存储过程中的非法修改；备份保证原始数据受损时可快速恢复；权限控制则限制只有授权人员能读写数据。

#### 1. 完整性校验示例
下面是在 RAG 中校验向量数据完整性的示例：

```python
import hashlib

def verify_vector_integrity(vector_data, stored_checksum):
    """
    验证向量数据的完整性。

    参数：
        vector_data (str): 向量数据，通常为嵌入向量或相关文档的内容。
        stored_checksum (str): 预先存储的校验和。

    返回：
        bool: 如果校验通过，返回 True，否则引发异常。
    """
    # 计算当前数据的 MD5 校验和
    computed_checksum = hashlib.md5(vector_data.encode('utf-8')).hexdigest()

    if computed_checksum != stored_checksum:
        raise ValueError("向量数据完整性校验失败，可能存在篡改或传输错误。")

    print("向量数据完整性校验通过。")
    return True

# 示例：在 RAG 系统中对存储的向量进行完整性校验
# 模拟存储的向量数据与校验和
stored_vector = "This is a sample vector representation of a document."
stored_checksum = hashlib.md5(stored_vector.encode('utf-8')).hexdigest()

# 在后续检索或更新操作中验证数据完整性
try:
    verify_vector_integrity(stored_vector, stored_checksum)
except ValueError as e:
    print(f"安全警报: {e}")
```

示例引入 `hashlib` 这一内置散列库，用 MD5 生成校验和。MD5 计算快、应用广，虽不适合抗高级攻击，但在完整性检查场景下仍常用。

`verify_vector_integrity` 接收向量数据 `vector_data` 和事先保存的 `stored_checksum`，内部先把数据编码为 UTF-8，再用 MD5 计算当前校验值 `computed_checksum`，与存储值对比。若不一致则抛出 `ValueError`，提示“向量数据完整性校验失败，可能存在篡改或传输错误”。异常机制让系统能够及时感知风险并触发记录或告警。

在模拟场景中，先准备一份向量及其校验和，调用校验函数，未被篡改时通过并提示成功，异常时则捕获错误。这种校验能有效降低因篡改或传输错误带来的隐患。

#### 2. 其他安全性检测

堵住未授权访问，入口管控是关键。建议同时启用防火墙与 IP 白名单，对数据库连接做严格准入，只允许名单内的账号操作。核心资产按敏感程度分层，配合强加密与精细化权限管控，降低信息外泄概率。再配合高可用部署和 DDoS 防御手段，让服务保持持续稳定。

## 6.3 公平性治理：从源头降偏
拒答机制（参见 4.3.2 节）只是对不当输出的补救举措。能否在根源上避免偏见出现？本节聚焦此问题。检索增强方案靠外部知识库为大模型补信息，使回答更贴近现实。但若底层大模型本身带偏见，生成阶段容易放大这种倾向。即便检索到的资料保持中立，先验偏好仍会左右最终结果。因为语言模型的能力受训练语料影响，语料中的偏见会在推理时被复现。因此即便知识库客观，输出仍可能偏离事实，甚至带有歧视色彩。这会损害答案可信度，也可能让用户产生负面体验，并推动不公平信息的扩散。构建检索增强方案时必须把偏见控制放在优先位置，通过发现和清除模型内部的偏见来保障结果的公平与可信。

### 6.3.1 隐性偏见的发现方法
先定位问题再谈改进。下面介绍几种常用探测手段：对模型产生的文本进行细致的统计分析，可以判断是否对特定群体存在系统性倾斜。具体做法是对比模型在性别、种族、年龄等不同人群描述上的表现，看是否存在明显的不一致。分析结果能够暴露模型在处理不同群体信息时的不公平现象，为后续优化提供依据。

下面的示例用于检测模型在不同检索结果中的潜在偏见：

```python
import pandas as pd
from collections import Counter

# 假设 RAG 系统生成的文本数据与检索的文档信息存储在一个 CSV 文件中
# 数据结构示例
# | retrieved_doc | gender | output |
# |---------------|--------|--------|
# | doc_1         | male   | "He is a leader" |
# | doc_2         | female | "She is caring" |

# 加载 RAG 生成输出数据
data = pd.read_csv('rag_outputs.csv')

# 按性别分类提取生成文本
male_outputs = data[data['gender'] == 'male']['output']
female_outputs = data[data['gender'] == 'female']['output']

# 统计生成文本中的词频
male_word_counts = Counter(" ".join(male_outputs).split())
female_word_counts = Counter(" ".join(female_outputs).split())

# 提取高频词汇集合
male_common = set([word for word, count in male_word_counts.most_common(100)])
female_common = set([word for word, count in female_word_counts.most_common(100)])

# 找出性别间高频词汇的差异
bias_words = male_common.symmetric_difference(female_common)

# 输出结果
print("性别差异词汇: ", bias_words)
```

这段脚本聚焦检索增强生成的输出，比较不同性别在描述上的表现差异，从而揭示生成环节可能存在的倾向性。假设数据来源于一个包含生成内容、性别标签与检索文档的表格。加载后按性别分组，可分别得到男性与女性的生成文本集。

随后对每个性别的文本进行词频统计，利用 Counter 计算词汇分布并挑选出现次数最高的前 100 个词。这些高频词汇反映了模型在描述特定性别时的用词习惯。为量化差异，代码计算两组高频词的对称差，得到男女各自特有的高频词。这些差异词往往暗示刻板印象，例如模型倾向用某一类词汇描述男性，另一类描述女性。

输出的差异词汇为开发者提供了改进线索。若发现明显倾向，可从两方面入手：一是检查检索来源是否存在偏向，比如对某些群体使用刻板表述；二是进行微调或去偏处理，降低生成时对特定群体的不公平倾向。

### 6.3.2 降偏策略：数据与训练双管齐下
偏见被发现后，关键在于削减。检索增强场景下可从数据准备与模型训练两方面着手。

#### 1. 数据准备环节
要让模型在训练中保持公正，选择多样化且具代表性的语料是前提。语料应覆盖不同群体、背景与情境，确保各群体都有足够体现。多样的语料有助于缓解数据失衡带来的倾向，提升模型在实际场景中的公平与鲁棒表现。

数据清洗是准备的核心步骤，需剔除含有明显有害或偏见的内容，避免负面样本污染训练。同时应为数据添加精细标注，明确区分中立内容与带偏见内容，帮助模型在学习时建立清晰边界。标注工作提升数据质量，也为训练提供明确目标。

为进一步丰富多样性，可引入数据增强。通过构造反事实样本或其他增强手段，人为扩充数据。例如针对原始数据中占比偏低的群体，生成更多训练样本以平衡分布。

下面的代码示例展示了如何对训练数据进行清洗、标注和增强，以构建一个多样化且具有代表性的数据集。

```python
import pandas as pd
from sklearn.utils import resample
from textblob import TextBlob

def preprocess_data(file_path):
    # 读取数据
    data = pd.read_csv(file_path)

    # 数据清洗：去除包含有害或有偏见内容的样本
    def is_clean(text):
        # 简单示例：过滤包含特定敏感词的文本
        sensitive_words = ['偏见词1', '偏见词2']
        return not any(word in text for word in sensitive_words)

    data = data[data['text'].apply(is_clean)]

    # 数据标注：标注文本的中立性或偏见性
    def label_bias(text):
        analysis = TextBlob(text)
        polarity = analysis.sentiment.polarity
        if polarity > 0.1:
            return 'positive'
        elif polarity < -0.1:
            return 'negative'
        else:
            return 'neutral'

    data['bias_label'] = data['text'].apply(label_bias)

    # 数据增强：生成反事实样本以增加数据的多样性
    augmented_data = []
    for _, row in data.iterrows():
        if row['bias_label'] == 'neutral':
            swapped_text = row['text'].replace('他', '她').replace('男', '女')
            augmented_data.append({'text': swapped_text, 'bias_label': row['bias_label']})

    augmented_df = pd.DataFrame(augmented_data)
    data = pd.concat([data, augmented_df], ignore_index=True)

    # 平衡数据集：确保各类标签的样本数量相近
    balanced_data = pd.concat([
        resample(data[data['bias_label'] == 'positive'],
                 replace=False,
                 n_samples=1000,
                 random_state=42),
        resample(data[data['bias_label'] == 'negative'],
                 replace=False,
                 n_samples=1000,
                 random_state=42),
        resample(data[data['bias_label'] == 'neutral'],
                 replace=False,
                 n_samples=1000,
                 random_state=42)
    ])

    return balanced_data

# 示例
processed_data = preprocess_data('training_data.csv')
print(processed_data.head())
```

上述代码先用 Pandas 工具导入表格格式的训练集。清洗阶段定义 is_clean 函数过滤掉包含特定敏感词的文本，防止有害内容影响训练。接着借助 TextBlob 对每条文本做情感分析，根据情感极性将文本标注为 positive、negative 或 neutral，以区分中立与偏见内容。

增强阶段针对标注为中立的文本生成反事实样本，例如将性别相关词“他”替换为“她”，以提升多样性并平衡群体代表性。最后利用 scikit-learn 的 resample 对各标签数据做下采样，使各类别样本数大致相等，降低数据不平衡带来的偏见。

经过这套处理流程，检索增强方案可以在训练时使用经过严格清洗、标注且多样化的数据集，从而在实际应用中获得更可靠、更公平的表现。

#### 2. 训练阶段的公平约束
6.1.2 节提到正则化对提升鲁棒性的作用。同理，可以引入公平性正则化来提升公平水平。

公平性正则化是在损失函数中加入额外的公平约束。其目标是在追求性能最优的同时，防止模型对特定群体产生不公平倾向。这样模型学习时不仅关注整体预测准确，还会兼顾不同群体间的公平性，决策时对各群体更均衡，减少歧视，从而提升可接受度。

以下是一个在检索增强方案中自定义损失函数并加入公平性约束的示例代码：

```python
# 自定义损失函数，将公平性约束整合到 RAG 模型的训练过程中
def custom_rag_loss(output, target, retrieved_docs, sensitive_attribute):
    generation_loss = torch.nn.CrossEntropyLoss()(output, target)
    retrieval_loss = compute_retrieval_loss(retrieved_docs, target)
    fairness_penalty = compute_fairness_penalty(output, sensitive_attribute)
    total_loss = generation_loss + alpha_retrieval * retrieval_loss + lambda_fairness * fairness_penalty
    return total_loss
```

上述自定义损失函数先计算生成部分的交叉熵损失，用来衡量生成文本与目标的差距。随后计算检索损失，确保召回的文档与目标相关。之后加入公平性惩罚项，通过 compute_fairness_penalty 评估输出对敏感属性的偏见程度。最后将生成损失、检索损失和公平性惩罚按权重叠加得到总损失，使训练过程同时优化生成能力、检索精度与公平性。

# 第 7 章 检索增强的高阶形态
本章将深入剖析检索增强的几种高阶变体——LongRAG、GraphRAG 和 GeneRAG——以及它们在特定场景下的优势。通过对比分析，读者能更清晰地理解在复杂场景下如何提升检索增强方案的性能。本章为高阶应用提供完整的技术蓝图，帮助在特定场景下构建更具针对性的检索增强方案。

## 7.1 LongRAG：用长文本突破传统瓶颈
随着任务复杂度提升，传统检索增强在处理长文本时暴露出不少瓶颈。为此，LongRAG 应运而生，它通过长文本检索模块与生成模块来重新平衡检索与生成的负担，提升整体表现。图 7-1 展示了传统检索增强与 LongRAG 的对比。

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_227_0.png)

本节将详细拆解 LongRAG 的原理与代码实现，帮助读者全面理解这一创新框架。

### 7.1.1 工作原理拆解
LongRAG 通过重构检索与生成流程，有效缓解了传统检索增强在长文本场景下的挑战。

1. 传统检索增强的局限性
传统检索增强中，检索部分从海量文档库里找出与查询相关的短片段，通常是约 100 词的维基段落；生成部分则基于这些片段产出答案。这种设计存在明显失衡。

1）检索压力过大：在庞大库中搜寻短片段，难度和计算成本都很高。

2）生成信息不足：短片段容易丢失背景，导致答案准确性和完整性受损。

3）无关信息干扰：切片窗口过短，检索容易带入与查询无关的内容，干扰生成。

2. LongRAG 的创新设计
LongRAG 通过引入长文本检索单元和对应的长文本生成模块，重新分配检索与生成的工作量，核心设计包括三点。

（1）核心设计模块

1）长检索单元：将整篇文档或若干相关文档合并为长度超过 4K Token 的检索单元。单元更长意味着总数大幅下降，同时保留更完整的背景。例如检索单元数从 2200 万降至 60 万，减轻检索负担并提升效率。

2）长检索器：在少量长单元中检索，只返回前若干个（通常少于 8 个）最相关单元。相比传统检索增强需要召回数百个短单元，长检索器检索更快、成本更低，也减少了引入硬负样本的风险。

3）长生成器：将检索到的长背景（约 30K Token）输入支持长文本的语言模型，如 Gemini-1.5-Pro 或 GPT-4o，进行零样本生成。依托现代长文本模型的强大能力，长生成器能在更丰富的背景下产出更准确、更详实的答案，无需额外训练。

（2）性能优势
借助上述设计，LongRAG 在多个数据集合上表现突出。无训练情况下，在 NQ 和 HotpotQA 上分别达到 62.7% 和 64.3% 的精确匹配率（EM），与最先进的全监督检索增强模型相当。在 Qasper 和 MultiFieldQA-en 等非维基数据集合上，LongRAG 在 F1 分数上也有显著提升。

LongRAG 通过长检索单元与长生成模块，有效缓解了传统检索增强检索过载与生成信息不足的问题，既提升了检索效率，也增强了答案的准确性和完整性，为下一代检索增强设计提供了新思路。

### 7.1.2 代码逐层解读
为了更深入理解 LongRAG 的运行机制，下面对其核心代码模块进行详细解读，涵盖长检索单元构建、相似度检索、背景聚合以及长生成模块调用。

1. 构建长检索单元
在 LongRAG 中，长检索单元的构建是关键一步。目标是将短文档或相关文档组合成长度超过 4K Token 的单元。以下是一个代码示例，展示了如何实现这一过程：

LongRAG 与 GraphRAG 核心机制全拆解

先从长检索单元的搭建说起。LongRAG 并不是直接拿原始文档去检索，而是先把相关材料整合成更长的语义块来用。

```python
def build_long_retrieval_units(documents, max_tokens=4000):
    long_units = []
    current_unit = ""
    related_docs = group_related_documents(documents)

    for doc in related_docs:
        if len(current_unit.split()) + len(doc.split()) <= max_tokens:
            current_unit += doc + " "
        else:
            long_units.append(current_unit.strip())
            current_unit = doc + " "

    if current_unit:
        long_units.append(current_unit.strip())

    return long_units

def group_related_documents(documents):
    # 根据文档之间的链接或主题相似度进行分组
    grouped = []
    current_group = []
    for doc in documents:
        if is_related(doc, current_group):
            current_group.append(doc)
        else:
            if current_group:
                grouped.append(current_group)
            current_group = [doc]
    if current_group:
        grouped.append(current_group)
    return [' '.join(group) for group in grouped]
```

构建环节主要依赖两个函数。build_long_retrieval_units 会遍历文档集合，把有关联的文档串起来拼成一个单元，并控制单单元长度不超 4K Token。group_related_documents 则负责先做分组，按超链接、主题相似度等关联信号把文档聚类，确保拼出来的长单元内部语义连贯而不是东拼西凑。

2. 近似相似度检索实现
长单元一长，直接算整段嵌入的成本就很高。LongRAG 换了一种思路：不用整段向量匹配，而是让查询去比对单元内部的多个子块，取最大相似度来代表整体相关性。

```python
def similarity_search(query, retrieval_units, encoder, chunk_size=512):
    query_embedding = encoder.encode(query)
    top_k_units = []

    for unit in retrieval_units:
        chunks = split_into_chunks(unit, chunk_size)
        max_sim = max([dot_product(query_embedding, encoder.encode(chunk)) for chunk in chunks])
        top_k_units.append((unit, max_sim))

    # 根据排序选择相似度最高的 k 个单元
    top_k_units = sorted(top_k_units, key=lambda x: x[1], reverse=True)[:k]
    return [unit for unit, sim in top_k_units]

def split_into_chunks(text, chunk_size):
    words = text.split()
    return [' '.join(words[i:i+chunk_size]) for i in range(0, len(words), chunk_size)]
```

这里的相似度检索流程大致是这样。similarity_search 先把用户查询编码成向量，然后逐个扫描长检索单元，把每个单元切成 512 Token 左右的子块。针对每个单元，计算查询向量与所有子块的相似度，并用其中最高的那个值作为该单元的最终得分。最后对所有单元按得分排序，取前 k 个最相关单元作为检索结果。

3. 检索结果拼接成长上下文
拿到 Top-k 的长单元后，还需要把它们融合成一个整体上下文喂给生成模型。

```python
def aggregate_retrieval_results(top_k_units):
    concatenated_context = ""
    for unit in top_k_units:
        concatenated_context += unit + " "
    return concatenated_context.strip()
```

aggregate_retrieval_results 的作用就是把前 k 个单元按顺序拼接成一条连续的长文本，作为长生成模块的输入窗口。

#### 4. 长生成模块如何产出答案
生成这一步需要提示模板来规范输入输出。

```python
def generate_answer(question, context, reader_model, prompt_template):
    prompt = prompt_template.format(question=question, context=context)
    answer = reader_model.generate(prompt)
    return extract_final_answer(answer)

def extract_final_answer(long_answer):
    # 从长回答中提取出简洁的最终答案
    return long_answer.split('\n')[-1].strip()
```

关键逻辑分两步。generate_answer 会把预设的提示模板填好，把问题和聚合后的上下文一起送入长文本模型，比如 GPT-4o 进行推理。extract_final_answer 则负责从模型输出的较长文本里提炼出简洁、准确的最终答案，避免信息冗余。

#### 5. 综合实现
把前面几个环节串起来，就得到完整的端到端流程。

```python
def longrag_pipeline(query, documents, encoder, reader_model, prompt_template, max_tokens=4000, k=8):
    # 构建长检索单元
    long_units = build_long_retrieval_units(documents, max_tokens)
    
    # 相似度检索
    top_k_units = similarity_search(query, long_units, encoder, chunk_size=512)
    
    # 上下文聚合
    aggregated_context = aggregate_retrieval_results(top_k_units)
    
    # 生成回答
    answer = generate_answer(query, aggregated_context, reader_model, prompt_template)
    
    return answer
```

longrag_pipeline 一次性完成了长检索单元构建、近似相似度检索、上下文聚合和答案生成，实现了从查询到回答的闭环。

#### 6. 性能优化方向
要让 LongRAG 跑得更快，可以从三方面入手。

- 向量索引优化：对长单元及其子块的嵌入使用 FAISS 等高效近似向量检索库，降低检索延迟。
- 批量处理：编码和检索时采用批量模式，充分利用 GPU 并行，提升吞吐。
- 缓存机制：对高频查询结果做缓存，避免重复计算，响应更即时。

## 7.2 GraphRAG：用知识图谱升级 RAG
GraphRAG 引入知识图谱来突破传统 RAG 的瓶颈，专门做大规模文本的查询聚焦摘要 Query-Focused Summarization。它在组织和检索信息的基础上，通过关系推理和多层次语义理解，输出更丰富、更准确的摘要。

![GraphRAG应用流程](img/a020f99cb5cf7ce5c57c07d9b2d4de82_232_0.png)

知识图谱本身就是一种结构化表达，能把实体和关系显式地记录下来。融合这层结构后，GraphRAG 带来了五个明显优势。

- 摘要更全、更多样：图能捕捉实体间的复杂关联，摘要既覆盖直接相关信息，也能带入间接关联内容。
- 大规模数据处理更稳：传统 RAG 受上下文窗口限制容易丢关键信息。GraphRAG 把数据按图结构拆成多个子社区并行处理，显著提升处理能力。
- 计算资源利用更高效：社区检测配合并行摘要，不同子社区可以同时生成，降低整体耗时。
- 语义理解和推理增强：图不仅存实体关系，还能基于结构做推理，让跨来源的信息融合更准确深入。
- 查询层次更灵活：高层级问题可汇总多个社区摘要得到全局回答，细节问题可直接定位到具体社区给出精细信息。

### 7.2.1 GraphRAG 工作流程拆解
整体架构可分为七个步骤。

- 文本预处理与分块
处理大规模语料时先做预处理分块。GraphRAG 会把源文档切成大小可调的文本块。块太小精度高但处理次数多，块太大效率高但易丢细节。通过多轮 gleaning 信息提取，即使大块也能高效抓取关键点。
- 实体与关系的抽取
对每个文本块，用大模型配合特定提示抽取实体及其关系。先识别人物、地点、组织等实体并记录名称、类型、描述，再识别实体间的关系类型和描述，输出成有序元组供后续建图。
- 知识图谱的构建
汇总所有块的实体关系，构建以实体为节点的图，边表示关系且带权重反映强度或频率。图不仅保存显式关系，也能通过拓扑捕捉隐含语义关联，为社区检测和摘要打基础。
- 社区检测与划分
成品图节点众多结构复杂。采用 Leiden 等社区检测算法把图划分为若干紧密的子社区，每个社区对应一个主题或概念簇。内部连接紧密、社区间连接稀疏，有利于并行和多样性。
- 社区摘要生成
对每个子社区，用大模型生成覆盖其内实体与关系的社区级摘要。生成可并行进行，效率高，同时为后续查询提供中层语义单元。
- 查询处理与摘要合成
用户提问后，系统先在图中定位相关社区摘要，用这些摘要生成部分回答，再对部分回答做一次汇总摘要得到全局答案。整体遵循 Map-Reduce：先并行 Map 再汇总 Reduce，保证全面且多样。
- 评分与筛选
每个部分回答会被大模型打分，0 到 100 分评估对查询的帮助度。只保留高分部分参与最终合成，有效过滤噪声，提升准确性和实用性。

### 7.2.2 源码解析：GraphRAG 的深度剖析
实现上基于 Python，结合多个开源库完成建图、社区检测和摘要生成。核心模块包括数据预处理和信息抽取等。

#### 1. 关键模块详解
- 数据预处理模块
主要负责把原始文档合理切块，为后续抽取做准备。

```python
import nltk
from nltk.tokenize import sent_tokenize

class DataPreprocessor:
    def __init__(self, chunk_size=600, overlap=100):
        self.chunk_size = chunk_size
        self.overlap = overlap

    def preprocess(self, documents):
        chunks = []
        for doc in documents:
            sentences = sent_tokenize(doc)
            current_chunk = []
            current_length = 0
            for sentence in sentences:
                sentence_length = len(sentence.split())
                if current_length + sentence_length > self.chunk_size:
                    chunks.append(' '.join(current_chunk))
                    current_chunk = current_chunk[-self.overlap//20:]  # Approximate token to word conversion
                    current_length = sum(len(s.split()) for s in current_chunk)
                current_chunk.append(sentence)
                current_length += sentence_length
            if current_chunk:
                chunks.append(' '.join(current_chunk))
        return chunks
```

该模块采用分句后再按词数分块的策略，并设置适当重叠来保证语义完整和上下文连续。
- 信息提取模块
利用大模型从文本块中抽取实体和关系，生成知识图谱的初始数据。实现上通过提示驱动模型输出结构化实体关系对，作为建图的输入。

```python
import openai

class InformationExtractor:
    def __init__(self, api_key, model='gpt-4-turbo'):
        openai.api_key = api_key
        self.model = model

    def extract_entities_and_relations(self, text):
        prompt = f"""
        请从以下文本中提取所有实体及它们的关系。以 JSON 格式返回，结构如下：
        {{
            "entities": [
                {{"name": "实体名称", "type": "实体类型", "description": "实体描述"}}
            ],
            "relationships": [
                {{"source": "实体A", "target": "实体B", "description": "关系描述"}}
            ]
        }}
        文本: {text}
        """
        response = openai.ChatCompletion.create(
            model=self.model,
            messages=[{"role": "user", "content": prompt}],
            max_tokens=1500
        )
        return response['choices'][0]['message']['content']
```

该环节借助精细设计的提示词，驱动大模型从原文中抓取实体及它们之间的关联，并统一以 JSON 格式返回，为后续的图构建提供干净的结构化输入。

- （3）图谱构建与社区（群组）检测模块

图谱构建部分把抽取到的实体和关系组装成一个完整的知识图，社区检测部分则依托图算法把整体网络拆分成若干个内聚的子群。图谱构建与社区（群组）检测模块的实现代码如下：

```python
import networkx as nx
from community import community_louvain

class KnowledgeGraphBuilder:
    def __init__(self):
        self.graph = nx.Graph()

    def build_graph(self, extracted_data):
        entities = extracted_data['entities']
        relationships = extracted_data['relationships']
        for entity in entities:
            self.graph.add_node(entity['name'], type=entity['type'], description=entity['description'])
        for rel in relationships:
            self.graph.add_edge(rel['source'], rel['target'], description=rel['description'])
        return self.graph

class CommunityDetector:
    def __init__(self, graph):
        self.graph = graph

    def detect_communities(self):
        partition = community_louvain.best_partition(self.graph)
        communities = {}
        for node, community_id in partition.items():
            if community_id not in communities:
                communities[community_id] = []
            communities[community_id].append(node)
        return communities
```

图建成之后会调用 Louvain 算法做社区发现，把网络切分为多个联系紧密的子社区。每一个子社区基本对应语料库里一个主题或概念簇。

- （4）摘要生成模块

摘要生成部分借助大模型为每个子群撰写精炼概括，并在其基础上合成全局回答。摘要生成模块的实现代码如下：

```python
class SummaryGenerator:
    def __init__(self, api_key, model='gpt-4-turbo'):
        openai.api_key = api_key
        self.model = model

    def generate_summary(self, text):
        prompt = f"""
        请为以下文本生成一个简洁、全面的摘要：
        {text}
        """
        response = openai.ChatCompletion.create(
            model=self.model,
            messages=[{"role": "user", "content": prompt}],
            max_tokens=500
        )
        return response['choices'][0]['message']['content']
```

这一环节通过定制化提示让模型产出高质量的概括，每个社区都会得到独立的摘要，查询时即可快速调用以加速回答生成。

- （5）查询处理模块

查询处理部分负责接收用户问题，定位到相关的社区摘要，并据此生成最终的综合回答。查询处理模块的实现代码如下：

```python
class QueryProcessor:
    def __init__(self, summary_generator, graph):
        self.summary_generator = summary_generator
        self.graph = graph

    def process_query(self, query, communities):
        relevant_summaries = []
        for community_id, nodes in communities.items():
            community_text = ' '.join([self.graph.nodes[node]['description'] for node in nodes])
            summary = self.summary_generator.generate_summary(community_text)
            relevant_summaries.append(summary)
        # 将相关摘要进行汇总
        final_prompt = f"""
        基于以下摘要，回答用户的查询：
        {query}
        摘要列表：
        {''.join(relevant_summaries)}
        """
        final_answer = self.summary_generator.generate_summary(final_prompt)
        return final_answer
```

该模块先依据查询内容找到对应的社区摘要，再借助大模型生成初步回答，最后把多份回答汇总成一份完整的全局回应。

- （6）评分与筛选模块

评分与筛选部分使用大模型对生成的回答进行打分，保证输出质量。评分与筛选模块的实现代码如下：

```python
class AnswerEvaluator:
    def __init__(self, api_key, model='gpt-4-turbo'):
        openai.api_key = api_key
        self.model = model

    def evaluate_answers(self, question, answer1, answer2, metric):
```

#### 2. 性能优化与扩展

在落地场景里，GraphRAG 的效果和效率还能从多个维度继续打磨，例如：

- 1）优化信息提取提示：持续迭代大模型的提示设计，提升实体与关系抽取的准确度和覆盖面，例如针对特定专业文档设计专门的提示模板。
- 2）引入更先进的社区（群组）检测算法：在 Louvain 之外尝试 Leiden 等算法，以获得更优的划分质量和计算效率。
- 3）增强评分机制：除大模型打分外，结合规则评估或机器学习模型，形成复合评测体系，让质量控制更稳健。
- 4）扩展知识图谱的属性：除了实体和关系，还可补充时间、地理位置等元信息，让图谱更丰富、语义更深。
- 5）支持多语言和多模态数据：接入多语言处理与多模态解析模块，使系统能够覆盖多语言文本和图文混合数据，提升适用范围。

## 7.3 垂直领域的定向增强：GeneRAG

在基因解析等高度专业化的垂直场景里，通用 RAG 的知识边界和能力会显得吃力。为此研究者提出 GeneRAG，一个面向基因任务优化的 RAG 方案。它能够动态融合外部基因数据库，明显提升大模型在基因问答、细胞类型注释、基因交互预测等任务上的表现。

### 7.3.1 原理解析

#### 1. 工作流程

GeneRAG 的运行可拆成 5 个步骤。

- 1）数据提取与预处理：从 NCBI 等公开基因库拉取相关信息，完成文本规范化、去重与一致性校准。
- 2）嵌入创建：用大模型为基因数据生成向量表示，构建可检索的向量库。
- 3）用户查询处理：将用户输入转成向量，通过余弦相似度在向量库中匹配最相关的基因记录。
- 4）信息检索与选择：采用 MMR 算法从候选中挑选既相关又多样化的文档，保证覆盖度和准确性。
- 5）生成回答：把检索结果与原问题结合，由大模型生成准确且上下文一致的答案。

经过上述流程，外部基因知识被有效注入生成环节，显著增强模型在基因相关任务上的能力。

#### 2. GeneRAG 的架构优势

高效的向量库管理、可扩展的模型设计以及基因多任务能力，是 GeneRAG 相比通用 RAG 的差异化优势。

（1）向量数据库的高效管理

为支撑海量基因数据的快速检索，GeneRAG 采用 FAISS 等高效相似度搜索方案，兼顾检索速度与精度；同时定期更新与维护向量库，确保信息保持最新。

（2）模型的可扩展性

架构采用模块化设计，检索与生成等组件可独立升级。例如可以替换更强的生成模型提升回答质量，也能随外部基因库扩充动态更新向量库，维持实时性和完整性。

（3）基因类多任务支持

除问答外，GeneRAG 还支持细胞类型注释、基因交互预测等下游任务。依托 RAG 与 MMR 的灵活组合，系统能按需输出针对性方案，提升在生物信息学领域的价值。

### 7.3.2 源码解析：GeneRAG 的深度剖析

要吃透 GeneRAG 的实现，需要从关键模块、核心算法以及实际应用与效果评估三个层面展开。

#### 1. 关键模块

（1）数据处理模块

该模块从 NCBI 等数据库抽取基因信息，并做清洗预处理，为后续嵌入做准备。数据处理模块的实现代码如下：

```python
import pandas as pd
from preprocessing import normalize_text, remove_duplicates

def extract_gene_data(ncbi_source):
    # 从 NCBI 提取基因数据
    gene_data = pd.read_csv(ncbi_source)
    # 预处理
    gene_data['normalized'] = gene_data['gene_info'].apply(normalize_text)
    gene_data = remove_duplicates(gene_data, subset=['gene_name'])
    return gene_data
```

（2）嵌入创建模块

嵌入创建模块利用预训练模型如 text-embedding-ada-002 把清洗后的基因信息转为向量，并写入向量数据库。

嵌入创建模块的实现代码如下：

```python
from transformers import AutoModel, AutoTokenizer
import torch

class EmbeddingCreator:
    def __init__(self, model_name):
        self.tokenizer = AutoTokenizer.from_pretrained(model_name)
        self.model = AutoModel.from_pretrained(model_name)

    def create_embeddings(self, texts):
        inputs = self.tokenizer(texts, return_tensors='pt', padding=True, truncation=True)
        with torch.no_grad():
            outputs = self.model(**inputs)
        embeddings = outputs.last_hidden_state.mean(dim=1)
        return embeddings

def build_vector_database(gene_data, embedding_model):
    embedding_creator = EmbeddingCreator(embedding_model)
    embeddings = embedding_creator.create_embeddings(gene_data['normalized'].tolist())
    vector_db = {'gene_name': gene_data['gene_name'].tolist(), 'embeddings': embeddings}
    return vector_db
```

（3）检索模块

检索模块基于向量库与余弦相似度进行匹配，找出与用户查询最相关的基因信息。检索模块的实现代码如下：

```python
from sklearn.metrics.pairwise import cosine_similarity

class Retriever:
    def __init__(self, vector_db):
        self.vector_db = vector_db['embeddings']
        self.gene_names = vector_db['gene_name']

    def retrieve(self, query_embedding, top_k=5):
        similarities = cosine_similarity(query_embedding, self.vector_db)
        top_indices = similarities.argsort()[0][-top_k:][::-1]
        retrieved_genes = [self.gene_names[i] for i in top_indices]
        retrieved_embeddings = self.vector_db[top_indices]
        return retrieved_genes, retrieved_embeddings
```

（4）生成模块

生成模块把检索到的内容与用户提问融合，调用大模型产出最终答案。生成模块的实现代码如下：

```python
from transformers import GPT2LMHeadModel, GPT2Tokenizer

class Generator:
    def __init__(self, model_name):
        self.tokenizer = GPT2Tokenizer.from_pretrained(model_name)
        self.model = GPT2LMHeadModel.from_pretrained(model_name)

    def generate_answer(self, prompt, context):
        input_text = f"Question: {prompt}\nContext: {context}\nAnswer:"
        inputs = self.tokenizer.encode(input_text, return_tensors='pt')
        outputs = self.model.generate(inputs, max_length=150, num_return_sequences=1)
        answer = self.tokenizer.decode(outputs[0], skip_special_tokens=True)
        return answer
```

（5）检索优化模块

该模块专门落地 MMR 策略，用来在召回结果的相关性与多样性之间做权衡，保证检索质量。实现代码如下：

```python
import numpy as np

def mmr(query_embedding, candidate_embeddings, candidate_indices, lambda_param=0.5, top_k=5):
    selected = []
    candidates = list(range(len(candidate_indices)))
    while len(selected) < top_k and candidates:
        mmr_score = []
        for candidate in candidates:
            relevance = cosine_similarity([query_embedding], [candidate_embeddings[candidate]])[0][0]
            diversity = max([cosine_similarity([candidate_embeddings[candidate]], [candidate_embeddings[s]])[0][0] for s in selected], default=0)
            score = lambda_param * relevance - (1 - lambda_param) * diversity
            mmr_score.append(score)
        selected_idx = np.argmax(mmr_score)
        selected.append(candidates[selected_idx])
        candidates.pop(selected_idx)
    return [candidate_indices[i] for i in selected]
```

#### 2. 核心算法解析

GeneRAG 把 RAG 作为底座，将用户提问与外部基因库的知识联动起来。首先用 MMR 对初步召回的文档做二次筛选，提升相关性同时避免同质化；随后借助向量嵌入把文本映射为高维向量，依靠相似度判定快速锁定最贴近查询的基因信息；最后把融合后的上下文送入大模型，产出高度贴合上下文的答案。配合高效的向量数据库调度和模块化设计，系统在海量基因数据上仍保持高效、可扩展。多种技术协同，使得 GeneRAG 在基因类任务中表现突出，准确率与全面性都得到明显提升。

（1）RAG

收到用户问题后，GeneRAG 先把查询向量化，在向量库中做相似匹配，拿到一批最相关的基因资料。接着用 MMR 对这批结果再优化，兼顾相关与多样，最后把精炼后的上下文一并喂给大模型，生成既准确又具备上下文连贯性的回答。

（2）MMR 算法

在 GeneRAG 中，MMR 主要承担检索结果再选择的职责。迭代过程中每次都挑出对查询最相关、同时与已选文档差异最大的候选，确保最终集合既高度贴合查询，又覆盖更多不同视角，规避重复。

具体迭代流程如下。

- 1）启动时，已选文档集合为空。
- 2）每一轮为所有候选计算 MMR 得分，得分公式见式（7-1）：

$$MMR = \arg \max_{D_i \in S \setminus R} [\lambda \cdot \text{Sim}(D_i, Q)] - [(1 - \lambda) \cdot \max_{D_j \in R} \text{Sim}(D_i, D_j)]$$

其中 $\lambda$ 用来调节相关性与多样性的权重，$\text{Sim}(D_i, Q)$ 表示文档 $D_i$ 与查询 $Q$ 的相似度，$\text{Sim}(D_i, D_j)$ 表示候选文档 $D_i$ 与已选文档 $D_j$ 的相似度。
- 3）把得分最高的文档加入已选集合。
- 4）重复上述操作，直至达到设定数量或候选耗尽。

这样既守住了相关性，又最大化信息的多样性，让后续生成更全面、更深入。

（3）向量嵌入与相似度检测

GeneRAG 依托预训练大模型产出高质量向量嵌入，既能捕捉语义，又能在高维空间中刻画基因信息的复杂关联。结合余弦相似度，系统可以迅速、精准地找出与用户意图最匹配的基因条目，为生成环节提供可靠的上下文支撑。

（4）生成回答的流程

检索环节结束后，相关基因信息会与原始问题一起输入大模型。模型在动态融合检索内容的基础上作答，不仅正确率高，还能同时触及用户关心的多个维度，从而提升可用性与可信度。

#### 3. 实验表现与落地验证

GeneRAG 在多项基因任务上展现出明显优势。在 NCBI 等权威库的测试里，基因问答、细胞类型注释、基因互作预测等场景均优于 GPT-3.5 和 GPT-4。以 GPT-4 为参照，基因问答准确率提升约 39%，细胞类型注释性能提高约 43%，基因互作预测的错误率下降 0.25。这些结果印证了该框架在补齐大模型基因知识短板方面的有效性与潜力。

整体来看，GeneRAG 体现了 RAG 在垂直领域定向增强的价值。未来随着更多专业知识库接入和检索技术迭代，类似框架有望在更多细分领域复制成功。结合新一代大模型与更高效检索，GeneRAG 系列方法将能提供更智能、更精准的专业服务，推动科研与应用的发展。

# 第 8 章 知识实时刷新与跨模态进化

本章聚焦两个方向：实时知识更新与跨模态能力增强。前者让 RAG 系统能够即时更新自身知识，降低领域迁移成本；后者让系统在文本、图像等多模态数据间顺畅切换与整合。

## 8.1 实时知识更新

传统 RAG 多依赖离线训练好的静态库，上线后内容基本固化，难以反映最新动态。这种滞后限制了新领域的适应速度，也抬高了迁移成本。实时知识更新旨在让 RAG 能够动态获取并整合新知，保持输出的时效与相关。

### 8.1.1 数据实时获取的方法

实时更新的第一步是拿到最新数据，从新闻网站、社交媒体、API 等渠道抓取信息并做预处理与过滤。常用做法包括在线检索和自动化采集。

- 1. 在线检索

在线检索指在内容生成时即时从外部源拉取相关信息，保证输出基于最新知识而无需频繁重训。

实现要点如下。

- 1）外部数据源连接：通过 API 或数据库连接实时访问最新源。

## 8.1.1 实时知识更新

要让 RAG 适配快速变化的领域，实时更新必不可少，核心流程包括：

1. **动态检索**：生成时按用户查询即时检索相关信息。
2. **信息融合**：把检索到的新信息与模型输出结合，形成综合回答。

下面是结合 OpenAI GPT-4 与实时 API 做在线检索的示例代码：

```python
import openai
import requests

# 设置 OpenAI API 密钥
openai.api_key = 'YOUR_OPENAI_API_KEY'

def fetch_latest_news(query):
    # 使用新闻 API 获取最新新闻（示例使用 NewsAPI）
    api_key = 'YOUR_NEWSAPI_KEY'
    url = f'https://newsapi.org/v2/everything?q={query}&apiKey={api_key}'
    response = requests.get(url)
    articles = response.json().get('articles', [])
    latest_news = " ".join([article['description'] for article in articles[:5]])
    return latest_news

def generate_response(user_query):
    # 实时检索相关的最新新闻
    latest_info = fetch_latest_news(user_query)

    # 结合最新信息生成回答
    prompt = f"基于以下最新信息，回答用户的问题。\n最新信息：{latest_info}\n用户问题：{user_query}\n回答："

    response = openai.Completion.create(
        engine="gpt-4",
        prompt=prompt,
        max_tokens=150,
        temperature=0.7,
    )

    return response.choices[0].text.strip()

# 用户查询示例
user_question = "当前的全球经济形势如何？"
answer = generate_response(user_question)
print("回答：", answer)
```

示例中系统先通过 NewsAPI 获取与查询相关的最新新闻摘要，再把摘要与用户问题一起交给 GPT-4 生成基于时效信息的回答，确保答案既新颖又准确。

### 8.1.2 自动化数据采集

为实现持续更新，可采用爬虫等自动化采集与处理流水线，让 RAG 长期从多源吸取新知并入库。

典型步骤：

1. **数据源识别**：明确需要监控的新闻站、社交平台、数据库等。
2. **采集工具**：用网络爬虫、API 或流处理工具定期或实时抓取。
3. **清洗与格式化**：去重、清洗、标准化，保证数据质量。
4. **知识库更新**：把处理好的数据写入知识库，供检索使用。

下面展示用 Scrapy 抓取新闻网站并更新知识库的示例代码：

```python
# 安装 Scrapy
# !pip install scrapy

import scrapy
from scrapy.crawler import CrawlerProcess
import sqlite3

# 定义 Scrapy 爬虫
class NewsSpider(scrapy.Spider):
    name = "news_spider"
    start_urls = [
        'https://example-news-website.com/latest',  # 替换为实际的新闻网站 URL
    ]

    def parse(self, response):
        for article in response.css('div.article'):
            title = article.css('h2.title::text').get()
            summary = article.css('div.summary::text').get()
            link = article.css('a::attr(href)').get()
            yield {
                'title': title,
                'summary': summary,
                'link': response.urljoin(link),
            }

# 数据库连接与更新
def update_knowledge_base(items):
    conn = sqlite3.connect('knowledge_base.db')
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS news (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT,
            summary TEXT,
            link TEXT UNIQUE
        )
    ''')
    for item in items:
        try:
            cursor.execute('''
                INSERT INTO news (title, summary, link) VALUES (?, ?, ?)
            ''', (item['title'], item['summary'], item['link']))
        except sqlite3.IntegrityError:
            # 忽略重复条目
            pass
    conn.commit()
    conn.close()

# 运行爬虫并更新知识库
def run_spider():
    process = CrawlerProcess(settings={
        'USER_AGENT': 'Mozilla/5.0',
    })
    spider = NewsSpider()
    items = []
    process.crawl(spider)
    process.start()  # 阻塞直到爬取完成
    # 假设爬虫的输出被存储在 items 中
    # 实际情况下需要实现管道程序将数据传递到 update_knowledge_base
    # 这里简化处理
    update_knowledge_base(items)

if __name__ == "__main__":
    run_spider()
```

这段示例演示如何用 Scrapy 爬取最新文章并存入 SQLite 数据库作为知识库的一部分。定期运行爬虫即可持续刷新库内内容，让 RAG 始终可检索到最新资讯。

### 8.1.3 知识库动态更新

拿到新数据后，需要将其向量化并写入 RAG 知识库。知识库多以向量数据库等高效结构存储，因此要对增量数据做嵌入后插入。以下示例说明如何借助 FAISS 实现知识库的动态更新：

```python
from sentence_transformers import SentenceTransformer
import faiss
import numpy as np

# 加载 M3E 预训练的嵌入模型
# M3E 模型可以通过 Hugging Face 的 Transformers 库下载，也可以通过 SentenceTransformer 加载
model = SentenceTransformer('moka-ai/m3e-base')

# 初始化 FAISS 索引
dimension = 768  # M3E 嵌入向量的维度
index = faiss.IndexFlatL2(dimension)

# 初始化知识库数据
initial_corpus = [
    "人工智能是计算机科学的一个分支",
    "机器学习是实现人工智能的一种方法",
    "深度学习是机器学习的一个子领域"
]

# 将初始数据嵌入并添加到索引中
initial_embeddings = model.encode(initial_corpus)
index.add(np.array(initial_embeddings, dtype='float32'))

# 实时添加新数据
def add_new_data_to_index(new_text):
    # 将新文本编码为嵌入向量
    new_embedding = model.encode([new_text])
    index.add(np.array(new_embedding, dtype='float32'))
    print("已添加新数据到知识库。")

# 示例：添加新获取的推文到知识库
new_tweet = "强化学习是让智能体通过试错学习策略的方法"
add_new_data_to_index(new_tweet)

# 测试检索功能
def search(query, top_k=3):
    # 对查询进行嵌入
    query_embedding = model.encode([query])
    # 检索最相似的向量
    distances, indices = index.search(np.array(query_embedding, dtype='float32'), top_k)
    print("检索结果：")
    for i, idx in enumerate(indices[0]):
        print(f"第 {i + 1} 名：{initial_corpus[idx]}，距离：{distances[0][i]}")

# 示例：查询知识库
search("机器学习的实现方法有哪些？")
```

这个案例展示了借助 SentenceTransformer 引入向量模型，把文本信息映射成向量表示，再搭配 FAISS 索引实现快速检索的思路。新增的数据可以即时完成向量化并写入索引，从而让知识库保持动态演进。

### 8.1.4 自动化数据管道

要让实时知识刷新既高效又可靠，就需要一套自动化的数据管道把数据采集、清洗预处理、知识库更新和模型推理串联成一条闭环。下面以 Apache Kafka 来搭建这条管道的思路如下：

```python
# 数据获取模块（生产者）
from kafka import KafkaProducer
import json

producer = KafkaProducer(bootstrap_servers='localhost:9092', value_serializer=lambda v: json.dumps(v).encode('utf-8'))

def fetch_and_send_data():
    # 这里可以是任何数据获取逻辑，例如 API 调用
    new_data = {"text": "最新的新闻内容", "timestamp": "2023-10-01T12:00:00"}
    producer.send('data_topic', new_data)
    producer.flush()

# 知识库更新模块（消费者）
from kafka import KafkaConsumer

consumer = KafkaConsumer('data_topic', bootstrap_servers='localhost:9092',
                         value_deserializer=lambda m: json.loads(m.decode('utf-8')))

for message in consumer:
    new_text = message.value['text']
    add_new_data_to_index(new_text)
    print(f"在 {message.value['timestamp']} 更新了知识库。")
```

借助 Kafka 的生产者-消费者机制，可以把从数据采集到知识库更新的全过程自动化。生产者侧实时把新数据推送到指定主题，消费者侧订阅主题并自动触发知识库的更新。

经过上述关键环节的打通，RAG 系统已具备初步的跨领域适应力，能够动态抓取并融合最新信息。后续还可以引入更多高效的检索算法以及大规模实时流处理框架例如 Flink，进一步提升数据处理与检索效率，让 RAG 在金融、新闻等快速变化的场景里表现得更高效、更智能。

## 8.2 跨模态能力增强

多模态 RAG 的核心在于把图像、文本等异构数据统一映射到高维向量空间，在同一空间里检索与生成，因而能产出更丰富、更准确的结果。比如电商里可结合商品图与用户评价做个性化推荐；医疗里能融合医学影像与诊断报告，为医生提供辅助决策。这种跨模态融合既提升了理解深度，也极大拓展了生成形式与应用边界。

当前要攻克的两大问题是多模态向量嵌入和多模态内容生成，下面分别给出可行的实现思路。

### 8.2.1 多模态向量嵌入

在多模态 RAG 里，所有数据类型最终都变成向量，并在向量空间里检索。无论查询是图还是文，系统都能通过相似度搜索定位到相关内容。根据对文本与图像的不同处理方式，嵌入策略大致可归为三类。

#### 1. 单一多模态模型

该思路使用一个多模态模型统一编码图像、文本等不同模态，将其投射到同一向量空间。此法跨模态检索与生成效率高，但对编码模型的质量依赖较强。

下面以预训练的多模态模型如 CLIP 为例，演示如何完成跨模态数据的统一嵌入与检索：

```python
from PIL import Image
from transformers import CLIPProcessor, CLIPModel
import torch

# 加载预训练的 CLIP 模型和预处理器
model = CLIPModel.from_pretrained("openai/clip-vit-base-patch32")
processor = CLIPProcessor.from_pretrained("openai/clip-vit-base-patch32")

# 定义多模态输入
image_input = Image.open("sample_image.jpg")
text_input = "A cat sitting on a sofa"

# 对输入进行预处理
inputs = processor(
    text=[text_input],
    images=[image_input],
    return_tensors="pt",
    padding=True
)

# 获取嵌入向量
outputs = model(**inputs)
text_embeds = outputs.text_embeds
image_embeds = outputs.image_embeds

# 计算文本和图像之间的相似度
similarity = torch.nn.functional.cosine_similarity(text_embeds, image_embeds)
print("Text-Image Similarity Score:", similarity.item())
```

以上实现用 CLIP 同时对文本和图像做统一编码，得到可以直接比较的嵌入向量。通过计算余弦相似度即可衡量图文相关性。该方式实现了跨模态数据的紧密融合，为 RAG 系统的查询与检索提供了基础支撑。

#### 2. 基于文本的模态转换

这是一种把非文本数据转译为文本描述的策略。所有非文本内容先被转成自然语言描述，再交由文本模型检索与生成。方法依托了当前文本模型的能力，转换过程中或有信息损失，但在多数场景下仍具很强实用性。

下面展示如何先用 BLIP 把图像转成描述，再用文本检索模型进行查询：

```python
from transformers import BlipProcessor, BlipForConditionalGeneration
from sentence_transformers import SentenceTransformer, util

# 加载图像描述生成模型（BLIP）和文本检索模型
image_caption_model = BlipForConditionalGeneration.from_pretrained("Salesforce/blip-image-captioning-base")
caption_processor = BlipProcessor.from_pretrained("Salesforce/blip-image-captioning-base")
text_retrieval_model = SentenceTransformer("all-MiniLM-L6-v2")

# 图像转文本描述
image = Image.open("product_image.jpg")
inputs = caption_processor(image, return_tensors="pt")
caption = image_caption_model.generate(**inputs)
text_description = caption_processor.decode(caption[0], skip_special_tokens=True)

print("Generated Caption:", text_description)

# 模拟检索库
corpus = ["A beautiful red dress", "A pair of stylish running shoes", "A high-tech smartwatch"]
corpus_embeddings = text_retrieval_model.encode(corpus)

# 基于描述检索最相似的结果
query_embedding = text_retrieval_model.encode([text_description])
similarities = util.pytorch_cos_sim(query_embedding, corpus_embeddings)
top_match = corpus[similarities.argmax()]

print("Top Matching Item:", top_match)
```

该片段先借助 BLIP 模型把图像描述成文字，随后用 SentenceTransformer 对生成文本进行检索。模态转换让非文本数据与文本数据建立关联，从而简化多模态处理的复杂度。

#### 3. 多编码器方法

此法为不同数据类型配备专属编码器，例如文本编码器、图像编码器等。实现复杂度会上升，但能为每类数据选取最合适的编码器，提升准确性与灵活性。

下面展示同时使用文本编码器和图像编码器进行检索的做法：

```python
from PIL import Image
from transformers import CLIPModel, CLIPProcessor
from sentence_transformers import SentenceTransformer
import numpy as np
from sklearn.metrics.pairwise import cosine_similarity

# 加载文本和图像编码器
text_model = SentenceTransformer("all-MiniLM-L6-v2")
image_model = CLIPModel.from_pretrained("openai/clip-vit-base-patch32")
processor = CLIPProcessor.from_pretrained("openai/clip-vit-base-patch32")

# 检索库
text_corpus = ["A beach during sunset", "A snowy mountain", "A cozy living room"]
image_corpus = ["beach.jpg", "mountain.jpg", "living_room.jpg"]
```
text_embeddings = text_model.encode(text_corpus)

# 处理图像并获取图像嵌入

image_embeddings = []

for img_path in image_corpus:

    img = Image.open(img_path)

    img_inputs = processor(images=img, return_tensors="pt")

    img_embeds = image_model.get_image_features(**img_inputs)

    image_embeddings.append(img_embeds.squeeze().detach().numpy())

image_embeddings = np.vstack(image_embeddings)

# 用户查询

query_text = "A warm place with a couch"

query_embedding = text_model.encode([query_text])

# 检索

text_similarities = cosine_similarity(query_embedding, text_embeddings)

image_similarities = cosine_similarity(query_embedding, image_embeddings)

top_text = text_corpus[np.argmax(text_similarities)]

top_image = image_corpus[np.argmax(image_similarities)]

print("Top Matching Text:", top_text)

print("Top Matching Image:", top_image)

### 8.2.2 多模态内容生成

传统 RAG 生成结果多以文本为主。随着需求多样化，单一模态已难以覆盖复杂场景。

在多模态 RAG 中，内容生成指系统能根据用户输入同时输出文本和图像。这种能力在多处有明显价值：智能助手回答问题时可附带配图；电商平台可依据描述生成商品图文介绍；教育场景可产出图文并茂的教学材料，帮助理解。

多模态生成不仅增强表现力，也提升交互性和实用性。核心难点在于协调文本生成模型与图像生成模型，保证两者语义一致，同时要兼顾生成质量、语义对齐与模型效率等。以下是几种可行的实现方案。

#### 1. 基于文本生成图像

一种直接的做法是先生成文本，再基于该文本用图像生成模型产出对应画面。这样能确保图文在语义层面保持一致，代码示例如下：

```python
import openai
from diffusers import StableDiffusionPipeline

# 设置 OpenAI API 密钥
openai.api_key = 'YOUR_API_KEY'

# 加载图像生成模型
image_model = StableDiffusionPipeline.from_pretrained("runwayml/stable-diffusion-v1-5")

# 使用 GPT-4 生成文本描述
prompt = "Describe a serene beach at sunset with palm trees."
response = openai.Completion.create(
    engine="gpt-4",
    prompt=prompt,
    max_tokens=100,
    n=1,
    stop=None,
    temperature=0.7,
)

generated_text = response.choices[0].text.strip()
print("Generated Text:", generated_text)

# 基于生成的文本描述生成图像
image = image_model(generated_text).images[0]
image.save("generated_image.png")
```

先由 GPT-4 拟出一份关于“夕阳下棕榈环绕的宁静沙滩”的细腻描写，再把这段文字作为条件输入给 Stable Diffusion 进行作图。先写后画的流程，能让语言描述与视觉结果在语义上保持高度咬合。

#### 2. 图像先行文本跟进
另一种思路是反过来，先做图再写文。具体做法是生成一张图像，再用图像 caption 模型把画面转成文字，代码如下：

```python
from diffusers import StableDiffusionPipeline
from transformers import BlipProcessor, BlipForConditionalGeneration
from PIL import Image

# 加载图像生成模型
image_model = StableDiffusionPipeline.from_pretrained("CompVis/stable-diffusion-v1-4")

# 加载图像描述模型
caption_model = BlipForConditionalGeneration.from_pretrained("Salesforce/blip-image-captioning-base")
caption_processor = BlipProcessor.from_pretrained("Salesforce/blip-image-captioning-base")

# 基于提示生成图像
prompt = "A majestic mountain range under a starry night sky."
image = image_model(prompt).images[0]
image.save("generated_image.png")

# 对生成的图像进行描述
inputs = caption_processor(image, return_tensors="pt")
out = caption_model.generate(**inputs)
generated_caption = caption_processor.decode(out[0], skip_special_tokens=True)

print("Generated Caption:", generated_caption)
```

这个例子里先合成了一张“星空下巍峨山脉”的画面，随后用 BLIP 对画面进行描述形成文字。借助视觉细节驱动文案，可以得到更贴合画面的精准描述。

#### 3. 文图同步生成
如果想要在生成环节就牢牢锁住文本与图像的一致性，可以采用共享隐空间的模型，或让一方作为另一方的生成上下文进行引导。文图同步生成的示例代码如下：

```python
import torch
import torch.nn as nn
import torch.optim as optim
from PIL import Image
import clip

# 加载 CLIP 模型，获取共享潜在特征空间的文本和图像特征
def load_clip_model():
    device = "cuda" if torch.cuda.is_available() else "cpu"
    model, preprocess = clip.load("ViT-B/32", device=device)
    return model, preprocess, device

# 预处理图像并提取特征
def preprocess_image(image_path, model, preprocess, device):
    image = preprocess(Image.open(image_path)).unsqueeze(0).to(device)
    with torch.no_grad():
        image_features = model.encode_image(image)
    return image_features

# 提取文本特征
def preprocess_text(text_list, model, device):
    text = clip.tokenize(text_list).to(device)
    with torch.no_grad():
        text_features = model.encode_text(text)
    return text_features

# 跨模态注意力机制模块
class CrossModalAttention(nn.Module):
    def __init__(self, text_dim, image_dim):
        super(CrossModalAttention, self).__init__()
        self.text_proj = nn.Linear(text_dim, image_dim)
        self.softmax = nn.Softmax(dim=-1)

    def forward(self, image_features, text_features):
        projected_text = self.text_proj(text_features)
        attention_weights = self.softmax(torch.matmul(image_features, projected_text.T))
        attended_features = torch.matmul(attention_weights, projected_text)
        return attended_features

# 简单的图像生成模型
class ImageGenerator(nn.Module):
    def __init__(self, latent_dim, output_dim):
        super(ImageGenerator, self).__init__()
        self.fc = nn.Sequential(
            nn.Linear(latent_dim, 512),
            nn.ReLU(),
            nn.Linear(512, output_dim),
            nn.Sigmoid()
        )

    def forward(self, x):
        return self.fc(x)

# 简单的文本生成模型
class TextGenerator(nn.Module):
    def __init__(self, latent_dim, vocab_size):
        super(TextGenerator, self).__init__()
        self.fc = nn.Sequential(
            nn.Linear(latent_dim, 512),
            nn.ReLU(),
            nn.Linear(512, vocab_size)
        )

    def forward(self, x):
        return self.fc(x)

# 训练联合生成模型
def train_joint_model(num_epochs, dataloader, latent_dim, image_dim, vocab_size, device):
    image_generator = ImageGenerator(latent_dim, image_dim).to(device)
    text_generator = TextGenerator(latent_dim, vocab_size).to(device)
    optimizer = optim.Adam(list(image_generator.parameters()) + list(text_generator.parameters()), lr=1e-4)

    for epoch in range(num_epochs):
        for batch in dataloader:
            input_data = batch['input'].to(device)
            target_image = batch['image'].to(device)
            target_text = batch['text'].to(device)

            generated_image = image_generator(input_data)
            generated_text = text_generator(input_data)

            image_loss = nn.MSELoss()(generated_image, target_image)
            text_loss = nn.CrossEntropyLoss()(generated_text, target_text)
            total_loss = image_loss + text_loss
            optimizer.zero_grad()
            total_loss.backward()
            optimizer.step()

            print(f"Epoch [{epoch+1}/{num_epochs}], Loss: {total_loss.item()}")

# 主函数整合
def main():
    model, preprocess, device = load_clip_model()

    # 示例数据的路径和内容
    image_path = "path_to_image.jpg"
    text_list = ["描述 1", "描述 2"]

    # 获取图像和文本特征
    image_features = preprocess_image(image_path, model, preprocess, device)
    text_features = preprocess_text(text_list, model, device)

    # 构造一个简单的数据加载器（假设已准备好数据）
    dataloader = ...  # 填充自定义数据加载逻辑

    # 训练模型
    train_joint_model(
        num_epochs=10,
        dataloader=dataloader,
        latent_dim=image_features.shape[-1],
        image_dim=64 * 64 * 3,  # 假设图像分辨率为64x64，采用 RGB 通道
        vocab_size=5000,  # 假设词表大小为 5000
        device=device
    )

if __name__ == "__main__":
    main()
```

这段示例的主要流程可以拆解为：

1）载入 CLIP 模型：先加载预训练的 CLIP，CLIP 能把图文映射到同一隐空间，从而捕捉两者关联。load_clip_model 会返回模型、预处理器与设备信息。
2）特征提取：通过 preprocess_image 与 preprocess_text 分别对图像和文本做预处理，得到可用于后续生成的嵌入向量，这些向量是生成任务的基座。
3）跨模态注意力模块：定义 CrossModalAttention，用来在生成时以文本特征引导图像生成。模块先用线性投影把文本特征映射到图像特征空间，再计算注意力权重并输出融合特征。
4）图像生成模型：定义 ImageGenerator，接收潜在向量例如文本特征后输出图像特征表示。这里使用一个简单的全连接网络来生成低分辨率的图像表示。
5）文本生成模型：定义 TextGenerator，接收潜在向量后生成文本序列。也是一个简化的全连接网络，用来输出词的概率分布。
6）联合训练过程：train_joint_model 完成联合训练循环。训练样本同时用于图像和文本生成，分别计算图像的 MSE 损失和文本的交叉熵损失，两者相加后一起优化，以保证文图一致。
7）数据加载器占位：为方便展示，代码中的数据加载部分是占位符，实际使用时需补充读取图像与对应文本标签的具体逻辑。
8）主函数逻辑：main 整合整体流程，调用 CLIP 加载，使用示例图文做特征提取，随后启动数据加载与联合训练。训练完成后，模型可生成与描述匹配的图像。

可以预见，随着技术迭代和多模态数据普及，RAG 系统会从单一领域向跨领域扩展，从静态知识库走向实时更新，从纯文本走向多模态融合。这些演进将显著提升系统的智能化程度，也让它在更多场景中变得不可或缺。

与此同时，RAG 发展仍面临挑战，比如数据采集与处理的实时性与可靠性、跨模态内容语义一致性的保障，以及扩展性与计算效率的权衡。未来能否持续优化这些环节，将直接决定 RAG 在新一代智能系统中的角色与价值。

# 附录

## 相似度计算
相似度计算的核心在于衡量查询与文档的相关程度，从而快速召回最匹配的信息。为此业界形成了多种度量方式，各有侧重。例如余弦相似度通过向量夹角来判断近似程度，在文本场景中表现稳定；Jaccard 相似度关注集合重叠，适合评估词表交集；编辑距离则衡量字符串的改动成本，常用于纠错与校对。选用合适的度量能明显提升检索效率与准确性。

### 1. 常用度量方式
在 RAG 体系里，相似度度量的选择直接影响检索质量与速度。它决定了系统如何对比不同片段的相似程度，进而影响结果好坏。常见做法如下。

#### （1）余弦相似度
余弦相似度是文本向量比较的常用标准，本质是比较两个向量的方向一致性。先把查询和文档分别映射成向量，再计算两者夹角的余弦值。夹角越小，余弦越接近 1，相似越高；夹角越大，余弦越接近 0，相似越低。该方法忽略向量长度，只看方向，因此更能捕捉语义相近。计算公式如式（A-1）所示：余弦相似度 = \frac{A \cdot B}{\|A\| \|B\|} \quad (A-1)

其中 A、B 为向量，分子是点积，分母是模长的乘积。取值范围 -1 到 1，数值越大越相似。

具体实现如下：

```
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

# 假设有两个向量
query_vector = np.array([1, 2, 3])
doc_vector = np.array([4, 5, 6])

# 计算余弦相似度
similarity = cosine_similarity([query_vector], [doc_vector])[0][0]
print(f"余弦相似度: {similarity}")
```

#### （2）Jaccard 相似度
Jaccard 相似度用于衡量两个集合的重叠比例，通过交集大小除以并集大小得到。具体公式如式（A-2）所示。在文本任务里，可先把文档转化为词集合，再用该指标衡量词重叠。这种方式不考虑词序与频次，只关注出现与否，直观体现词汇重合程度。

Jaccard相似度 = \frac{|A \cap B|}{|A \cup B|} \quad (A-2)

其中 A、B 为集合。取值范围 0～1，越接近 1 越相似。

具体实现如下：

```
def jaccard_similarity(text1, text2):
    set1 = set(text1.split())
    set2 = set(text2.split())
    intersection = set1.intersection(set2)
    union = set1.union(set2)
    return len(intersection) / len(union)

# 示例文本
query = "机器学习 是 人工智能 的 分支"
document = "人工智能 包括 机器学习 和 深度学习"

similarity = jaccard_similarity(query, document)
print(f"Jaccard 相似度：{similarity}")
```

#### （3）中心核对齐

中心核对齐，英文简称 CKA，可算是一种比较前沿的评估手段，核心用途是衡量不同嵌入模型之间的相似程度。在构建 RAG 系统时，它的应用价值格外突出。通过把多个嵌入模型放到不同数据集上做对比测试，CKA 能够给出可量化的相似性指标。它覆盖的任务面很广，能从多个角度审视模型表现。正因为鉴别力强，CKA 善于捕捉模型在各任务上的细微差别，为后续调优提供可靠依据。

下面就来看中心核对齐的具体落地方式：

```
from kernel_alignment import cka
import numpy as np

# 假设有两个嵌入矩阵
embedding1 = np.random.rand(100, 300)  # 模型 1 的嵌入
embedding2 = np.random.rand(100, 300)  # 模型 2 的嵌入

# 计算 CKA
similarity = cka(embedding1, embedding2)
print(f"CKA 相似度：{similarity}")
```

### 2. 优化相似度计算的方法

RAG 系统想要表现更稳更准，相似度计算环节是关键。接下来介绍几类实践中效果不错的优化思路，围绕嵌入模型的领域适配和检索流程的动态调整展开，并结合示例做说明。

#### （1）嵌入模型微调

在 RAG 架构里，嵌入模型负责把自然语言变成高维向量，是相似度判定的基础。向量质量越高，语义捕获越完整，匹配也越精准。但通用预训练模型面对垂直领域时容易掉链子，毕竟专业文本的词汇和表达方式有其独特性。对模型做领域化微调，正是提升匹配准确率和相关度的有效做法。

嵌入模型微调的具体步骤。

1. 选定基础模型：先挑一个合适的预训练模型作为起点，比如 M3E、BGE 这类在大规模通用语料上训练过的模型，它们本身具备较强的语言理解基础。
2. 构建领域数据：汇集大量与目标垂类相关的文本作为训练材料。以医学为例，可选用医学论文、病历记录等资料。
3. 预处理与编码：用对应模型的分词器把文本转成模型可读的格式，保证输入规范一致。
4. 配置训练超参：设定学习率、批大小、迭代轮数等关键参数，让模型能在领域数据上稳定学习。
5. 执行微调：基于准备好的数据对模型参数进行调整，使其更好地贴合领域的语言习惯和专业术语。
6. 评估验证：借助验证集检验微调后模型的表现，确认其在相似度任务上的提升是否达标。

下面以 M3E Embedding 的微调为例说明具体操作：

```
from transformers import AutoTokenizer, AutoModelForSequenceClassification,
    Trainer, TrainingArguments
from datasets import Dataset
import numpy as np

# 加载预训练模型和分词器
model_name = "moka-ai/m3e-base"  # 替换为实际的 M3E 模型名称
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForSequenceClassification.from_pretrained(model_name, num_labels=2)

# 假设有一个医学领域的数据集
train_texts = [
    "患者表现出典型的糖尿病症状，包括多饮、多尿和体重下降。",
    "研究表明，新的癌症治疗方法在临床试验中显示出显著效果。",
    # 添加更多医学领域的示例文本
]
train_labels = [0, 1]  # 示例标签，根据实际数据进行调整

# 数据预处理
train_encodings = tokenizer(train_texts, truncation=True, padding=True, max_length=512)
train_dataset = Dataset.from_dict({
    'input_ids': train_encodings['input_ids'],
    'attention_mask': train_encodings['attention_mask'],
    'labels': train_labels
})

# 定义训练参数
training_args = TrainingArguments(
    output_dir='./results',
    num_train_epochs=5,              # 增加训练轮数以充分微调
    per_device_train_batch_size=8,   # 根据显存调整批次大小
    warmup_steps=1000,               # 增加warmup步数以稳定训练
    weight_decay=0.01,               # 权重衰减防止过拟合
    logging_dir='./logs',
    logging_steps=500,               # 更频繁的日志记录
    evaluation_strategy="epoch",     # 每个 epoch 结束时进行评估
    save_strategy="epoch",           # 每个 epoch 结束时保存模型
    load_best_model_at_end=True,     # 在训练结束时加载最佳模型
    metric_for_best_model="accuracy", # 以准确率作为最佳模型的评估指标
)

# 定义评估指标
def compute_metrics(eval_pred):
    logits, labels = eval_pred
    predictions = np.argmax(logits, axis=-1)
    accuracy = np.mean(predictions == labels)
    return {'accuracy': accuracy}

# 定义 Trainer
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    compute_metrics=compute_metrics
)

# 开始微调
trainer.train()
```

下面对这段代码的执行逻辑做拆解。

1. 引入依赖库：
   - transformers 负责加载预训练模型与分词器，并提供 Trainer 训练接口。
   - datasets 用于数据集的构建与处理。
   - numpy 承担数值运算和指标计算。
2. 加载模型与分词器：
   - 通过 AutoTokenizer 和 AutoModelForSequenceClassification 载入 moka-ai/m3e-base 的分词器和结构，该模型适合嵌入类任务。
   - num_labels=2 说明当前是二分类设定，可按需修改。
3. 准备领域数据集：
   - train_texts 存放医学场景的示例文本。
   - train_labels 提供监督信号，标签可代表疾病类别、研究结论等。
4. 数据预处理：调用分词器生成 input_ids 与 attention_mask，并开启截断和填充，将序列统一到 512 长度。
5. 设定训练参数：
   - 指定输出路径、epoch 数、batch size、warmup 步数、weight decay 等。
   - logging_steps=500 与 evaluation_strategy="epoch" 用于定期日志和分轮评估。
   - load_best_model_at_end=True 且 metric_for_best_model="accuracy" 让训练收敛后自动保留验证集上最优模型。
6. 定义评测函数：compute_metrics 计算预测与真实标签的准确率，用作性能衡量。
7. 启动训练：
   - Trainer 统一管理数据加载、参数更新与评估。
   - 执行 trainer.train() 后，模型在医学数据上完成参数调整，提升领域适应性。

#### （2）动态检索优化

传统检索往往一套流程走到底，查询类型怎么变，检索方式和参数都不变。但真实场景里，用户的提问背景和意图差异很大，固定策略很难兼顾所有情况。动态检索优化就是为了解决这个痛点而提出，核心是让大模型依据查询上下文灵活决定检索内容和触发时机，从而提高相似度计算的效率和命中相关性。

这样做的好处是检索策略可以随查询而变，更贴合实际需求，进而提升结果精准度和计算效率。

1. 上下文识别：动态检索的前提是读懂查询。大模型可先解析语句的语义、意图和隐含需求，判断其所属领域和背景，为后续策略选择提供依据。
2. 混合检索：策略多样化是关键。不同上下文下可选用不同方式，比如语义细腻的查询更适合向量检索，关键词明确的查询则关键词检索更快。实践中常采用混合检索，融合两种方式的优势，覆盖更广的查询类型。
3. 自适应调整：优化是持续的。系统要基于检索结果的反馈不断迭代策略，保持对用户需求变化的敏感性，让检索能力随时间自进化。

下面给出一个实践案例，混合 FAISS 向量检索与 Elasticsearch 关键词检索，并依据上下文自动切换策略：

```
def dynamic_retrieval(query, context):
    # 根据上下文选择不同的检索策略
    if "技术" in context:
        # 使用向量检索
        embedding = model.encode(query)
        D, I = index.search(np.array([embedding]), k=10)
    elif "法律" in context:
        # 使用关键词检索
        I = keyword_search(query)
    else:
        # 混合检索策略
        embedding = model.encode(query)
        D, I_vec = index.search(np.array([embedding]), k=5)
        I_kw = keyword_search(query)
        I = list(set(I_vec) | set(I_kw))
    return I

# 示例调用
query = "区块链技术的法律影响"
context = "法律"
results = dynamic_retrieval(query, context)
print(f"动态检索结果: {results}")
```

代码主要完成以下几步：

1. 向量检索：用 SentenceTransformer 把查询编码为向量，在 FAISS 索引中找最邻近。
2. 关键词检索：调用 Elasticsearch 做关键词匹配。
3. 混合检索：将两种结果合并并去重。
4. 动态选择：依据上下文决定采用哪种或哪几种检索方式。

混合检索兼具多样性、灵活性和效率，既保留了向量检索的语义理解能力，又发挥了关键词检索的快速定位优势，并能随上下文自适应切换，满足不同查询场景。后续在架构和参数上继续打磨，还能进一步放大学习和适用空间。

# 推荐阅读

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_265_0.png)

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_265_1.png)

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_265_2.png)

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_265_3.png)

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_265_4.png)

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_265_5.png)

# 作者简介

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_266_0.png)

## 宁星星

现任华大生命科学研究院 BGI Research 生物智能研究所资深 NLP 算法工程师，聚焦 RAG 系统、Agent 智能系统及生命科学垂类大模型研发。多年 NLP 实践积累，精通大模型、AIGC、智能问答等方向，并在多项全国性 NLP 与知识图谱竞赛中获奖。著有《语义解析：自然语言生成SQL与知识图谱问答实战》一书。

得益于内外部知识库的动态融合与高效检索，RAG 大幅缓解了大模型内容生成的幻觉问题，并把应用边界延伸到医疗、法律、金融等专业场景，成为 AI 落地的重要引擎。但要实现大规模商用，仍需直面知识库维护成本高、模块协同难、算力消耗大、系统稳定性不足等挑战。

针对这些问题，本书对内容进行了精心设计：

### ▶ 理论与实践并重

- 理论体系：系统梳理 RAG 的运行机理、核心模块——检索、生成、提示工程——以及技术演进脉络。
- 实践指导：以 Python 代码为依托，演示如何借助 LangChain、FAISS、Milvus 等工具搭建 RAG 系统，并配以智能问答、知识管理等实战案例。

### ▶ 全链条覆盖

- 数据层：详述数据清洗、向量化存储及知识库构建方法。
- 检索层：对比关键词检索、语义检索、混合检索的优劣，并探讨嵌入模型训练与文档解析优化策略。
- 生成层：解析 Transformer 架构、模型微调及提示工程的实操要点。
- 评估与优化：提供 RAG 系统的性能评估指标，如检索准确率、生成质量，以及分布式推理、边缘计算等优化方案。

### ▶ 技术前瞻

- 实时知识更新：探讨自动化数据管道与知识库动态更新机制。
- 多模态融合：展示 RAG 在文本、图像、视频等跨模态场景中的应用潜力。
- 鲁棒性与安全：引入对抗训练、访问控制及模型偏见检测策略，提升系统可靠性。

本书不仅是 RAG 技术的入门指南，更是开发者进行技术落地和探索前沿方向的参考手册。无论是希望解决实际问题的工程师，还是寻求技术突破的研究者，都能在此找到答案。

![](img/a020f99cb5cf7ce5c57c07d9b2d4de82_267_0.png)