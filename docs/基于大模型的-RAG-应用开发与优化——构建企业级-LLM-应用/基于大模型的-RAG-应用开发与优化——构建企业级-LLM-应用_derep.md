# 企业级 RAG 实战：让大模型真正可落地

# 写在前面

大语言模型凭借强大的自然语言处理能力，正掀起一轮新的 AI 浪潮。作为大模型落地的重要形态，检索增强生成 RAG 已在智能搜索、问答、客服、数据分析以及 AI 智能体等场景里展现出很强的应用价值。

RAG 看上去很简单。它的原理用几句话就能讲清，借助低代码平台或成熟的开发框架，几分钟就能搭出一个能演示的原型。但一旦要推向生产，特别是企业级环境，业务复杂度和数据复杂度会指数级上升，对准确性、稳定性和工程化要求也大幅提高。原型和生产之间的鸿沟会立刻显现：数据形态千差万别、检索精度不够、模型输出时好时坏、用户提问五花八门、端到端响应慢等问题都会冒出来。

对开发者而言，如何高效地设计、构建、部署并持续优化生产就绪的企业级 RAG 应用，仍是核心难题。本书希望为踏入大模型应用的开发者抛砖引玉，提供一份比较系统的 RAG 开发指南。

全书以 Python 为主要语言，并以 LlamaIndex 为主线框架展开。两者生态完善、社区活跃，能大幅减少重复造轮子的成本。需要说明的是，尽管示例基于 Python 与 LlamaIndex，但书中关于 RAG 的思想、原理、架构和优化方法具有通用性，可迁移到其他语言和框架。

技术在快速演进，希望本书能作为一个起点，激发你对大模型应用的兴趣，也期待更多研究者共同推动大模型落地的进程。

严灿平
2024 年 7 月

## 入门篇

## 第 1 章 大模型与 RAG 核心概览

在动手开发和优化 RAG 之前，先厘清大模型和 RAG 的来龙去脉，有助于更稳地构建系统。

## 1.1 认识大模型

### 1.1.1 生成式 AI 的爆发期

近两年最现象级的科技产品，非 OpenAI 的 ChatGPT 莫属。它用极短时间冲到亿级用户，点燃了“百模大战”甚至“千模大战”，把 AI 推向 2.0 时代，也让人看到通用人工智能的轮廓。

大模型为何忽然火起来？回看技术史，区块链、元宇宙等也曾热闹，但热度远不及这次。关键在于应用层：大模型带来了真正能创造价值、门槛极低的普惠应用。底层是复杂的深度学习与神经网络，前端却以极简的交互呈现给普通人。

![](img/7eddb68cd925518fdc310e52e94ba9a9_14_0.png)

图 1-1

我们今天说的大模型，既有生成文本的，也有文生图、文生视频的扩散模型。相比过去擅长找规律、做决策的传统 AI，它们更擅长推理和创造，因此衍生的应用被称作生成式 AI。基于大模型产出的文本、图像、音视频等内容，无论结构化还是非结构化，都可归为人工智能生成内容 AIGC。

### 1.1.2 从个人到企业 应用在持续升级

再先进的技术，也要靠场景体现价值。大模型既在个人侧发挥作用，也在对工程化要求更高的企业侧实现规模化。

个人应用 To C：对话机器人、精准搜索、翻译、写作、文档阅读辅助、角色扮演、多媒体创作、设计助手、代码生成等，![](img/7eddb68cd925518fdc310e52e94ba9a9_15_0.png)

图 1-3
有的以独立工具出现，有的以 AI Copilot 的形式嵌入通用软件。

企业应用 To B：受限于更严苛的业务环境和工程要求，落地仍在探索中，但在智能客服、在线咨询、智能营销、交互式数据分析、企业内网搜索、RPA 增强等领域已出现大量试验。

大模型的 AI 能力可以以多种形式输出：直接调用 API 赋能现有流程，或打造独立的 AI 原生工具。值得注意的是，如今的 AI 应用已远超早期 ChatGPT 式的简单对话，更多具备自我规划、记忆、调用外部工具或插件、自我反思和纠错能力的 AI 智能体正在出现。RAG 既可以独立使用，也可以作为构建复杂智能体的常用范式。

![](img/7eddb68cd925518fdc310e52e94ba9a9_16_0.png)

图 1-4

![](img/7eddb68cd925518fdc310e52e94ba9a9_16_1.png)

图 1-5

### 1.1.3 大模型并非万能

强大不代表没有短板。大模型在自然语言理解上带来革命性提升，但也伴随难以根治的先天局限。

1. 知识时效性不足
大模型参数规模从百万到千亿不等，预训练和微调成本高昂，迭代周期从数天到数月不等。商业通用模型还需安全审查，因此模型掌握的知识往往滞后，无法回答训练截止时间之后的问题。

![](img/7eddb68cd925518fdc310e52e94ba9a9_17_0.png)

2. 黑盒可解释性差
大模型易用的原因之一是黑盒运行。用户只需输入 Prompt，无需也无法窥见内部推理过程。这降低了门槛，但在关键场景调试和精细控制时会带来困惑，往往只能靠调 Prompt 和参数碰运气。Anthropic 关于解构语言模型的研究显示，可解释性仍面临巨大挑战。

3. 输出不确定性强
基于概率的生成机制决定了同一样本多次调用可能得到不同结果。这在创意写作、自媒体内容等需要多样性的场景是优点，但在需要确定性和可预测性的场景，如意图识别、调试排障，就成为障碍。可通过 temperature 和 seed 等参数部分缓解，无法完全消除。

```json
{
  "intent": "1",
  "dest": "北京",
  "date": "2024-03-28"
}
```

图 1-8

4. 著名的幻觉问题
大模型常一本正经地胡说八道，即生成错误或虚构信息。主要成因包括：训练语料含偏差、过时或错误信息；过度泛化；缺乏真正的理解和常识；对垂直行业或企业私有知识覆盖不足。

幻觉是制约企业级落地的最大障碍。企业无法容忍产品介绍被编造、财务分析出现错误、客户意图被误读。正是这些不足，推动了更先进架构、训练方法和清洗预处理技术的发展，也催生了 RAG 这样的重要优化方案。

## 1.2 RAG 到底是什么

### 1.2.1 引入 RAG 的原因

为了改善大模型在时效性、可靠性和准确性方面的不足，尤其是缓解幻觉，业界提出了多种优化手段，RAG 是其中最成熟、应用最广的一种。

RAG 的核心思路是把生成式大模型与实时检索结合起来，在生成时为模型补充来自外部的相关数据和上下文，而非仅依赖训练知识。

![](img/7eddb68cd925518fdc310e52e94ba9a9_21_0.png)

这样做的好处体现在三方面：特定领域任务受限于训练覆盖，检索可带来更深更广的知识；静态模型无法反映最新信息，实时检索能提供新鲜内容；相比直接扩增参数规模，检索引入信息的成本更低、见效更快。

形象地说，大模型像一位学识丰富的学生，考试时可能遇到没学过的内容而编造答案。RAG 就像是考场上临时给的一本参考书，要求学生以书中内容为依据作答，相关问题的得分率自然会提升。

### 1.2.2 一个 RAG 的最小案例

假设要做一个网站上的自助产品咨询工具，用户可以用自然语言提问，比如“请对比下贵司这款产品和 XX 产品的差异”。如果直接用通用大模型，![](img/7eddb68cd925518fdc310e52e94ba9a9_22_0.png)

大模型大概率不具备企业最新产品的知识，只能拒绝或编造。

用 RAG 的做法是先从企业知识库检索出一段相关补充资料。

![](img/7eddb68cd925518fdc310e52e94ba9a9_23_0.png)

图 1-11

将检索结果组装进 Prompt，要求模型基于提供的资料作答。

![](img/7eddb68cd925518fdc310e52e94ba9a9_23_1.png)

图 1-12

模型吸收了外部知识，结合自身能力完成了推理，给出了可信的回答。

第一次接触 RAG 的开发者，很容易产生一种错觉：这不就是提示工程的升级版吗？无非是给大模型挂个知识外挂。确实，RAG 的核心可以理解为带外挂的提示工程，但它的内涵远不止于此。简化来看，RAG 就是为了给模型补充外部信息来提升生成质量这一条主线。可一旦落地，就会暴露出大量细节与难题。用户提问的自然语言千变万化，相关知识存放在哪里？用什么样的索引方式去找？怎么保证检索回来的恰好是解决当前问题的那一部分？拿考试来打比方，若考题是解一元二次方程，却塞给学生一本微积分教材，检索再精准也毫无意义。

## 1.3 RAG应用的技术架构

### 1.3.1 RAG应用的经典架构与流程

搞清楚基本概念和典型场景后，可以从工程角度拆解一个最基础、最常见的 RAG 逻辑。注意，下图仅展示了最小粒度的原理图，真实项目里会根据场景、条件和工程要求叠加更多模块和优化，细节后面会展开。

![](img/7eddb68cd925518fdc310e52e94ba9a9_24_0.png)

图 1-13

在大多数情况下，构建一个简单 RAG 可以归为两大阶段：数据索引和数据查询。每个阶段又拆分为若干处理步骤，整体关系见图 1-14。

![](img/7eddb68cd925518fdc310e52e94ba9a9_25_0.png)

### 1. 数据索引阶段

RAG 靠检索增强生成，前提是检索内容已经准备好。传统计算机检索多依赖关键词匹配，像搜索引擎或关系数据库那样按匹配度排序。在大模型场景里，主流做法是向量语义检索：把文档切成块，计算查询向量与已有知识向量的相似度，比如余弦相似度，取最相关的前 K 个块，也就是 top_K。因此向量存储索引是 RAG 最常见的索引形态。

向量本质上是一种数学表达，把文本、图像、音频等复杂信息映射成高维空间中的点，每个维度对应一种特征。向量化后，相似的语义在空间中位置更近，从而支持相似度计算和聚类。把信息转为向量表示的模型叫做嵌入模型。语义检索就是靠查询词与库中向量的相似度找最接近的内容。

索引阶段通常包含：

- (1) 加载 Loading：RAG 需要的知识来源多样，有结构化、半结构化、非结构化数据，也有互联网公开内容或企业内网文档，以及普通文件或问答对。系统要能连通并读取这些内容。
- (2) 分割 Splitting：大块知识，比如 Word/PDF、Excel、网页或数据库表，需要切分为可检索的块 Chunk。分割涉及规则设计：块多大合适？如何识别段落边界？
- (3) 嵌入 Embedding：做向量存储索引时，要把切分后的块转为高维向量，例如 1024 维。这一过程依赖商业或开源的嵌入模型，如 OpenAI 的 text-embedding-3-small。
- (4) 索引 Indexing：向量需要写入内存或磁盘持久化。实践中一般用功能完备的向量数据库来存储与管理，提供高效检索算法和接口。注意，高级应用的索引不只有向量，还可能包含知识图谱索引、关键词表索引等。

### 2. 数据查询阶段

索引准备好后，查询阶段的核心是检索与生成，也就是合成。

- (1) 检索 Retrieval：利用已建索引，比如向量库，从知识库中找出与问题相关的块，并按相关性排序，为下一步生成提供上下文。
- (2) 生成 Generation：由大模型完成。模型可以是本地部署或通过 API 调用的远程模型。它结合检索结果和用户的原始问题，在精心设计的 Prompt 引导下输出答案。

随着范式演进，流程里还增加了两个典型环节：

- (1) 检索前处理 Pre-Retrieval：检索前的准备工作，常见有查询改写、查询扩展、检索路由等，目的是提升后续检索的召回精度与生成质量。
- (2) 检索后处理 Post-Retrieval：在检索完成后对结果做精炼，比如用更专业的排序模型重排或过滤掉不符合条件的块，让最重要、最合规的内容排在上下文前端，从而提升模型输出质量。

### 1.3.2 RAG 应用面临的挑战

RAG 以简洁易懂的方式大幅提升了大模型在专业任务上的适应性，也显著增强了企业级应用的准确性，但应用场景越丰富，挑战越突出。

1. 检索召回的精确度
RAG 的思路是用临时检索给模型补充知识营养。检索到的外部块是否足够精准、全面，直接决定生成质量。自然语言本身复杂，检索带有模糊性，若带回大量噪音、无用或矛盾信息，会拖累生成。

2. 大模型自身对抗干扰的能力
检索上下文可能混杂干扰、多余或矛盾信息，模型需要具备推理、识别和甄别能力，严格按 Prompt 输出。模型本身能力的高低是最终质量的关键变量。

3. 上下文窗口的限制
大模型对输入输出的 token 数量有限。若想把大规模知识库检索出的若干相关块全部喂给模型，很容易超出窗口上限。如何在限制内塞进更多有效信息，是常见难题。

4. RAG 与微调的选择
微调是让大模型通过训练适应垂直领域的方法，它把知识固化到参数中，使用时架构更简单。RAG 与微调该如何取舍、配合、协同，以最大化输出能力，是持续被讨论的问题。

5. 响应性能问题
RAG 相比直接调用模型增加了检索、重排等步骤，复杂的范式甚至需要多次迭代调用模型。质量提升往往以响应延迟增加为代价，在对时延敏感的企业场景里，如何平衡质量与速度是挑战。

### 1.3.3 RAG 应用架构的演进

正因挑战众多，RAG 如同许多 IT 技术一样，上手快、精通难。学术界和工业界对 RAG 范式设计与优化的研究非常活跃。简要回顾发展脉络：

- （1）RAG 概念最早在 2020 年由 Meta 技术团队在论文 Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks 中提出，用以给当时兴起的大型预训练语言模型补充非参数化的外部记忆，改善生成任务。
- （2）2022 年 ChatGPT 出现前，RAG 关注度有限，部分工作聚焦检索效率。ChatGPT 引爆大模型关注后，研究重心转向如何用 RAG 提升可控性、缓解幻觉、优化推理与生成。
- （3）随着 GPT-4、Gemini Pro 等更强模型面世，RAG 应用面更广，传统范式的短板也更明显，催生了大量优化理论与实践，如 RAG 与微调结合、顺序流程改为迭代自省、针对各阶段的深度优化等。

同济大学智能自主系统上海研究所等团队在 2024 年初发布的综述 Retrieval-Augmented Generation for Large Language Models: A Survey 将演进分为三阶段。

#### 1. Naive RAG 朴素 RAG 或经典 RAG 阶段
沿用最原始的顺序流程：索引、检索、生成三个核心节点，按序执行，保留最简过程。

#### 2. Advanced RAG 高级 RAG 阶段
在朴素基础上增强三阶段，特别是在检索环节加入检索前处理与检索后处理。

#### 3. Modular RAG 模块化 RAG 阶段
突破链式顺序范式，提供更灵活、可扩展的方案。其思路是把 RAG 拆分为模块类、模块和算法三个层级：模块类代表核心流程如预检索，模块代表流程中的功能如查询转换，算法代表实现方式如普通重写、后退重写、HyDE 重写等。使用者可按需组合模块与算法，构建专属工作流。随着研究深入，模块和算法不断增多，灵活性与扩展性显著提升。第 13 章将介绍如何通过组合实现复杂优化目标。

## 1.4 关于RAG的两个话题

RAG 与微调如何选择，以及 2024 年起 RAG 与具备超长上下文能力的模型之间的关系，是业界争论焦点，下面先聚焦第一点。

### 1.4.1 RAG 与微调的选择

提升特定行业场景的适应性与准确性，除了 RAG，还可以用自有数据微调大模型。两者有何区别，选型依据是什么？

以 GPT 为例，训练大致分为两个阶段。

1. 预训练阶段
最复杂的一环，像 GPT-4 需要上万张 GPU 在海量无标注数据上训练数月。产出的叫基座模型。基座模型可直接用，但通常是“补全文档”而非“回答问题”的模型。若想让它问答，需要用提示把问题包装成文档补全任务。如何让基座变成交互式助手，就要进入下一步。

2. 微调阶段
宏观上把受监督微调、奖励模型+人类反馈强化学习 RLHF 都归到这里。在少量标注数据上对基座再训练，使其更好适应特定场景与下游任务，例如：
- （1）强化某类应用能力，比如情感检测。
![](img/7eddb68cd925518fdc310e52e94ba9a9_31_0.png)
- (2) 适应特定使用场景，如人类对话中输出无害安全内容。
- (3) 适应特定知识领域，如医疗或法律术语语义。
- (4) 适应标注数据稀缺的任务。
- (5) 适应特定语言输出要求，如风格一致性。

相比预训练，微调算力与成本大幅下降，对许多企业可行。微调本身是专业技术，涉及全量微调、Prompt Tuning、Prefix Tuning、P-tuning 等方法，对资源、指令数据要求各不相同，效果也不同。工具链也在成熟，如 OpenAI 的在线微调 API、DeepSpeed 等。

实施微调还需准备规模化高质量数据集，通常由大量指令与输出样本构成。行业特征强的垂直领域，数据准备最困难，主要挑战包括：
1. 数据来源与专业性、有效性保障。
2. 多形态数据的清洗与归一。
3. 提示、输入、输出的标注规范。
4. 知识老化后如何回传到模型。

用考试比喻：大模型是学生。
RAG：考试时给参考书，要求现学现用。
微调：考前辅导，让学生成为专家后再考试。

选型要综合考虑应用场景、行业特征、性能要求、数据能力、技术能力、预计成本，以及指令理解、输出准确性、稳定性等测试结果，见图 1-15。

![](img/7eddb68cd925518fdc310e52e94ba9a9_33_0.png)

图 1-15

两种方案各有利弊，参考对比如表 1-1，结论会随技术演进而变化。

表 1-1

| | RAG | 微调 |
|---|---|---|
| 优点 | 1. 使用灵活，可随时调整 Prompt。<br>2. 技术相对简单。<br>3. 通过上下文注入知识立即适应领域。<br>4. 无额外训练成本 | 1. 模型自身具备特定知识输出或格式适应能力。<br>2. 下游应用更友好，任务使用更简单。<br>3. 可节省推理阶段 token，推理成本更低 |
| 缺点 | 1. 受上下文窗口限制。<br>2. 连续对话中携带本地知识较困难。<br>3. 不确定性在高精度场景增加失败概率。<br>4. 长 Prompt 推理成本高。<br>5. 模型迭代后可能需重新调 Prompt | 1. 非开箱即用。<br>2. 需数据准备、标注、清洗及算力训练成本。<br>3. 需要 ML 专家与数据专家。<br>4. 无法完全阻止幻觉，过度微调可能削弱能力。<br>5. 迭代周期长，不适合实时性知识 |

目前研究与实践表明，在不考虑成本时，更适合微调的场景是：
（1）需要注入大体量且相对稳定、迭代周期长的领域知识，并形成通用领域大模型对外服务或运营。

（2）对于极少数容错空间几乎为零的关键环节，且现有手段已无法满足精度要求时，只能通过高效微调甚至全量微调来强行拉升这些任务的输出可信度，典型的例子就是医疗诊断。

（3）就算已经叠加了提示工程、RAG 等增强手段，指令理解的准确性、输出的稳定性或其它业务目标依然达不到预期。

在以上两种情形之外的绝大多数场景，先用 RAG 来提升大模型生成效果是更合理的主策略。现实条件允许时，微调与 RAG 的混合使用，极有可能成为未来综合效果更好的方案。

### 1.4.2 超长上下文能否取代 RAG
大模型的上下文窗口正以惊人的速度膨胀。早期主流模型普遍只有 4K ~ 8K<sup>①</sup> 的窗口，如今 128K 以上的模型已很常见，更有不少宣称支持更大的窗口。从 Claude 2 的 200K，到 Claude 3 与 Gemini 1.5 宣称的 1M，窗口大小似乎已不再是限制使用的主要顾虑。尤其是 Gemini 1.5 技术报告中关于“大海捞针”——在极长文本中精准定位特定事实性知识的实验结果，引发了一个争议：未来若能把上百份文档一次性塞进对话窗口，且模型真能在其中检索到事实，那外部索引与检索、也就是 RAG 范式还有存在的必要吗？

厂商用测试数据强调模型无所不能，技术乐观主义者也期待一个简洁优雅的“一劳永逸”解法，但就当前现实而言，每次需要一根“针”就把整片“大海”交给模型推理，在效用、性能和成本上都要谨慎思考，例如：

- （1）1M 的窗口真的够用了吗？如果不够怎么办？
- （2）反复喂入如此长的上下文，时延、网络和推理成本的增加是否值得？
- （3）企业里大量实时更新、形态各异的知识，如何每次都完整喂给模型？
- （4）模型的“大海捞针”能力稳定吗？会不会受“针”的数量和位置影响？
- （5）知识密集型任务远不止简单事实问答，模型能否真正应对？
- （6）上下文过长会不会让应用的追踪、调试和评估变得更难？
- （7）相比 RAG 在应用层对知识的精细管控，把所有知识塞进模型如何保证数据安全？

RAG 的核心之一就是检索：借助各种手段精确找出事实所在的知识块，再交给模型推理。正因如此，主张大模型派的人认为，一旦模型能在超长上下文里精准检索，复杂的 RAG 就可被替代。

实际上大量测试显示，受限于主流 Transformer 架构的底层原理，超长上下文的理解能力远不如宣传那样理想。我们用开源工具 LLTest_NeedleInAHaystack 对若干模型进行检验后发现，如图 1-16 所示，当前模型未必能在超长文本中准确定位事实并完成推理，准确率至少受以下因素制约。

（1）输入上下文的长度。2K 与 128K 情景下的精确检索表现不同。
（2）知识块在上下文中的位置。所处位置会影响检索效果。
（3）完成任务所需的相关知识块数量。检索 1 个块与检索 10 个块的成功率并不相同。

![](img/7eddb68cd925518fdc310e52e94ba9a9_35_0.png)

图 1-16

即便暂时不考虑成本与响应速度，仅靠超长上下文能力去完成知识密集型任务的精准检索与推理，仍不现实。

因此 RAG 与超长上下文模型更适合相互配合、取长补短。模型为 RAG 提供优化基础，例如未来可用较大文档而非极小知识块作为召回单元；RAG 则提供模型本身不具备的灵活性。简洁性与灵活性的平衡，可能是当下最务实的做法，如图 1-17 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_36_0.png)

图 1-17

## 第2章 如何搭建 RAG 开发环境
工欲善其事，必先利其器。动手前先弄清 RAG 应用常见的开发方式、可能用到的工具与平台，再据此准备合适的开发工具与环境。

技术选型差异很大，这里不做面面俱到的教程。假设你已有软件开发与大模型的基础知识，下面基于主流技术做简要的环境准备说明，后续开发都在本章搭建的环境中进行。

### 2.1 RAG 应用的两种主流开发方式
当前主要有两条路线：用低代码平台快速成型，或用主流的大模型应用开发框架深度定制。

#### 2.1.1 低代码平台快速搭建
代表产品包括百度的 AppBuilder、阿里云百炼大模型服务平台、字节跳动的 Coze 开发平台，以及开源的 FastGPT、Dify 等。这类平台的共同特点是：

(1) 上手快、操作便捷。可视化配置即可搭出自己的 RAG 应用。
(2) 通常自带完整工具链，如知识库管理、应用测试、Web UI 或 API 发布等。
(3) 部分平台支持 RAG 工作流设计，通过拖拽加少量编码实现流程编排。
(4) 部分平台可灵活选型嵌入模型与大模型，按需组合以获得更好效果。

便捷性与灵活性往往此消彼长，低代码平台也存在明显局限：

(1) 多为在线开发部署，知识密集型应用在敏感行业会面临安全合规风险。
(2) 面向通用场景设计，如简单的私有知识问答，但在企业个性化需求面前容易捉襟见肘。
(3) 企业级场景对工程化与定制化流程要求更高，通用平台难以满足。
(4) 低代码产出的主要是问答类检索应用，并非 RAG 的全部形态。例如 RAG 可与智能体结合优化输出，如开源 Vanna 框架用 RAG 向 Prompt 注入检索到的数据结构描述来提升 Text-to-SQL 准确率，这类场景低代码平台难以直接覆盖。
(5) 对开发者而言，平台降低了工作量，却屏蔽了大量技术细节，不利于后续优化与技术成长。

总体看，低代码适合个人或小微企业快速做知识型应用或智能体，面向 B 端的主流 RAG 应用往往需要更灵活的技术栈。

![](img/7eddb68cd925518fdc310e52e94ba9a9_38_0.png)

#### 2.1.2 基于开源应用开发框架
另一条路线是使用当下主流的开源框架，如 LangChain 系列、LlamaIndex 及微软 AutoGen，多采用开源+少量商业服务的模式。它们的特点是：

- （1）组件化、模块化设计成熟，功能完善、文档齐全，经过大量社区验证。
- （2）屏蔽底层细节，让开发者聚焦上层业务逻辑。框架封装了模型通信、文档解析、复杂底层算法等。
- （3）内置大量可复用组件，开发像搭积木。常见框架还内置对先进范式和算法的支持，例如 LangChain 的 LangGraph、LlamaIndex 的 Query Pipeline 用图结构编排更复杂的工作流。
- （4）扩展性强，可派生定制组件，也原生支持多种大模型、数据源、嵌入模型、向量库和第三方 API。
- （5）不仅提供开发组件，还提供工程化平台和工具帮助应用更快进入生产，如 LlamaIndex 内置评估组件。

1. 了解 LangChain 框架
LangChain 是知名的大模型应用开发框架，覆盖开发、测试、评估与部署全生命周期。基础库完全开源，可从简单调用到复杂 RAG 或 Agent。

LangChain 框架由以下库构成。

![](img/7eddb68cd925518fdc310e52e94ba9a9_40_0.png)
- （1）LangChain-Core：基础抽象与 LCEL 表达语言。
- （2）LangChain-Community：第三方集成模块，常封装为合作伙伴模块如 LangChain-openai。
- （3）LangChain：上层核心组件，包括 Chains、Agents 等。
- （4）Templates：官方提供的常见应用模板。
- （5）LangServe：将 LangChain 链部署为 REST API 的平台。
- （6）LangSmith：在线平台，用于追踪调试、测试、评估和监控。

另有 LangGraph 等用于构建更灵活的 RAG/Agent 工作流。总体功能强大、可对接几乎所有第三方模型与服务，但学习门槛较高，结构庞大，版本迭代快，部分设计存在过度封装的争议。

截至本书完稿，最新版本为 0.2.x。

2. 了解 LlamaIndex 框架
LlamaIndex 由 LlamaIndex 公司推出，用于连接私有数据与大模型，特点是简单、灵活、强大。其结构如图 2-2。

![](img/7eddb68cd925518fdc310e52e94ba9a9_42_0.png)

图 2-2
- (1) Core Framework：核心框架，实现大量可扩展的基础组件与工具。
- (2) Integrations：官方或第三方扩展组件，扩充数据加载器、LLM、嵌入模型、向量库、Agent 工具等兼容能力。
- (3) Templates：上层应用模板，便于二次开发。
- (4) Eval Datasets：用于评估的现成数据集，通常包含开发用的原始知识集与评估用的测试集。

核心框架外的扩展、模板与数据集多托管在 LlamaHub 平台上。

LlamaIndex 适合生产级与企业级应用：

- (1) 基于成熟 RAG 范式提供数据加载、向量库访问、索引构建、模型访问等可靠集成。
- (2) 灵活可扩展，可在开箱即用组件上派生定制。
- (3) 最初主攻 RAG，内置大量模块化优化组件与算法，使用上比 LangChain 更简洁。
- (4) 内置强大的 Data Agent 开发能力，支持智能规划并使用外部工具进行数据操作；最新版本引入基于图结构的 Query Pipeline 编排 RAG 工作流。

相较 LangChain，LlamaIndex 入门门槛更低，更聚焦 RAG，因而本章选用其作为基础框架。

截至本书完稿，LlamaIndex 最新版本为 0.10.x，后续开发亦基于此版本。

### 2.2 RAG 应用开发环境准备
本节说明常见的基础环境与工具，并搭建可本地化运行的开发环境。

#### 2.2.1 硬件环境建议
若希望在一台电脑上完成主程序、向量库、本地大模型、关系数据库乃至前端等全部工作，建议：

(1) MacOS 推荐，或 Windows 10、Linux。
(2) 内存不低于 16GB，若运行本地大模型则建议 32GB 及以上。
- （3）如需本地 GPU 推理，按模型大小配置独立显卡。
- （4）配备最新 Intel CPU 或 Apple M 系列芯片，以及足够的高速磁盘。
- （5）高速以太网，便于下载模型或访问模型 API。

#### 2.2.2 基础大模型
大模型是 RAG 的核心。可按条件选择两种接入方式：调用云端商业大模型 API，或部署本地开源大模型。

1. 通过 API 访问云端商业大模型
各商业服务商均提供开放 API，通常为 SDK 与 HTTP 两种形式，文档齐全，使用门槛低，一般只需一个 API Key。以阿里云通义千问为例，申请流程如下：

- （1）登录阿里云百炼大模型服务平台。
- （2）点击“登录”并注册，建议手机号验证码登录。
- （3）登录后进入左侧导航的“模型广场”。
- （4）点击“去开通”，同意协议后“确认开通”，新用户常获一定免费额度。
![](img/7eddb68cd925518fdc310e52e94ba9a9_45_0.png)

图 2-3
(5) 点击右上方账号管理，选择“API-KEY”，点击“创建新的 API-KEY”获取密钥（见图 2-4）。
![](img/7eddb68cd925518fdc310e52e94ba9a9_45_1.png)

图 2-4
(6) 点击右上方的“产品文档”可查阅模型介绍、速率限制、API 文档、计费方式等。
① 书中图中 API Key 形式不一，正文统一称 API Key。

获得 API 权限后请妥善保存 Key，若免费额度用完需确保账号余额充足。

后续教程会频繁调用 OpenAI GPT 系列的接口，建议提前在微软 Azure 云平台上开通 OpenAI 大模型 API 的使用权限，审核通过后即可按需付费，具体操作请自行登录 Azure 控制台查看。

市面上的商业大模型数量众多，不同厂商的接口协议和 SDK 并不通用，甚至同一厂商在升级后也可能出现不兼容问题。若业务层需要在多个商业模型间切换，就必须投入大量适配和调试成本。一个实用的做法是自建面向应用的统一 API 层，通过少量配置就能自动适配各家大模型的协议。这样前端调用时只需更换模型名称，无需改动业务代码，同时还能做多通道流量分发和负载均衡，提升整体可用性。

这里可以参考的开源方案是 ONE-API。它把后端接入的各类大模型接口全部转换为 OpenAI 协议形态，极大简化了前端对不同模型的调用与切换。

#### 2. Ollama 实现本地开源模型推理
若需要在本地部署开源大模型并提供推理能力，Ollama 是一个上手快的管理工具。它操作简便，可在普通 CPU 上运行，也支持 GPU 加速。安装流程很直观。

1）安装 Ollama
（1）打开 Ollama 官方站点，选择适配的操作系统，如图 2-5 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_46_0.png)

# Download Ollama

![](img/7eddb68cd925518fdc310e52e94ba9a9_47_0.png)

图 2-5

(2) 选定系统后点击下方按钮获取安装包。下载完成后在本机找到安装文件，双击启动安装向导。Ollama 会在本地安装一个命令行工具，用于后续下载和启动模型服务。

# Install the command line

```
> ollama
```

![](img/7eddb68cd925518fdc310e52e94ba9a9_47_1.png)

图 2-6

(3) 安装完毕后打开终端进行校验，输入命令查询版本号，如图 2-7。

```
> ollama --version

(base) pingcy@pingcy-macbook ~ % ollama --version
ollama version is 0.1.30
(base) pingcy@pingcy-macbook ~ %
```

图 2-7

## 2）拉取与运行大模型
完成安装后即可在本地拉取并运行模型，通常按以下步骤进行。
（1）登录 Ollama 官网，浏览当前支持的全部模型列表，如图 2-8。

![](img/7eddb68cd925518fdc310e52e94ba9a9_48_0.png)

图 2-8

（2）挑选一个模型，例如阿里巴巴的通义千问开源版。点击 qwen 或 qwen2 系列，可看到不同参数量的介绍与启动命令，根据硬件条件选择合适的规格，比如 14b，如图 2-9。

![](img/7eddb68cd925518fdc310e52e94ba9a9_48_1.png)

图 2-9

（3）复制运行命令到本机终端粘贴执行。此时 Ollama 会自动开始下载模型到本地并显示进度，如图 2-10。

```
(base) pingcy@pingcy-macbook ~ %
(base) pingcy@pingcy-macbook ~ % ollama run qwen:14b
pulling manifest
pulling de0334402b97... 19% | 1.5 GB/8.2 GB 107 MB/s 1m1s
```

图 2-10

模型下载完成后会自启推理服务，如图 2-11。

```
pulling manifest
pulling de0334402b97... 100% 8.2 GB
pulling 7c7b8e244f6a... 100% 6.9 KB
pulling 1da0581fd4ce... 100% 130 B
pulling f02dd72bb242... 100% 59 B
pulling 007d4e6a46af... 100% 484 B
verifying sha256 digest
writing manifest
removing any unused layers
success
>>>
```

图 2-11

这时可在命令行里与模型进行交互式对话，如图 2-12。

```
>>>
>>> 你好
你好！很高兴为你提供帮助。有什么问题可以问我吗？
```

图 2-12

至此 Ollama 的安装与验证完成，输入“/bye”可退出命令行。
（4）若想重新开启对话测试，直接再次执行运行命令即可，如图 2-13。

```
(base) pingcy@pingcy-macbook ~ % ollama list
NAME ID SIZE MODIFIED
qwen:14b 80362ced6553 8.2 GB 7 minutes ago
(base) pingcy@pingcy-macbook ~ % ollama run qwen:14b
>>>
>>>
>>>
>>> 创作一首清明的诗句
清明时节雨纷飞，
草色遥看近却无。
祭扫先人怀故土，
纸灰飞扬诉衷肠。
清风明月寄哀思，
春意盎然慰英魂。
```

图 2-13

## 3) 使用 API 服务模式
开发场景下通常不需要交互式命令行，而是以 API 方式调用服务。可按以下方式让 Ollama 以服务模式运行。
(1) 若尚未启动服务，可直接运行命令进入服务模式。该模式下 Ollama 会启动本地 API 服务器对外提供服务，MacOS 上用 ollama run 启动模型也会自动进入服务模式。

```
> ollama serve
```

(2) Ollama 默认监听 11434 端口，若与其它服务冲突，可通过设置环境变量修改端口。

```
> OLLAMA_HOST=:11435 ollama serve
```

(3) 验证 API 是否可用。Ollama 兼容 OpenAI 协议，以下 curl 命令可用于测试。

```
curl http://localhost:11434/v1/chat/completions \
-H "Content-Type: application/json" \
-d '{
    "model": "qwen:14b",
    "messages": [
        {
            "role": "system",
            "content": "You are a helpful assistant."
        },
        {
            "role": "user",
            "content": "你好呀!"
        }
    ]
}'
```

若能正常返回响应，则说明 API 服务运行正常。

至此，我们已拥有通义千问与 OpenAI 在线大模型的 API 服务，也具备了可随时切换模型的本地 Ollama 开源大模型 API 服务，这些基础能力将在后续开发中被反复使用。

## 2.2.3 嵌入模型
在 RAG 场景中，嵌入模型用于把知识块向量化，实现语义相似度检索，其向量质量直接影响检索效果。嵌入模型分为基于商业大模型的向量模型，如 OpenAI Embedding 系列，以及开源向量模型，如智源研究院的 BGE Embedding。

### 1. 通过 API 访问云端嵌入模型
提供大模型服务的商业平台，其嵌入接口一般无需单独申请，直接使用已有 API Key 即可。我们已开通通义千问与 OpenAI 大模型 API，因此可以直接调用两者的嵌入模型。表 2-1 列出了通义千问官方支持的嵌入模型。

| 表 2-1 |

| :--- |

| **模型中文名** | **模型英文名** | **向量维度** | **单次请求文本最大行数** | **单行最大输入字符长度** | **支持语种** |

| 通用文本向量 | text-embedding-v1 | 1536 | 25 | 2048 | 汉语、英语、西班牙语、法语、葡萄牙语、印度尼西亚语 |

| | text-embedding-async-v1 | 1536 | 100 000 | 2048 | 汉语、英语、西班牙语、法语、葡萄牙语、印度尼西亚语 |

| | text-embedding-v2 | 1536 | 25 | 2048 | 汉语、英语、西班牙语、法语、葡萄牙语、印度尼西亚语、日语、韩语、德语、俄罗斯语 |

| | text-embedding-async-v2 | 1536 | 100 000 | 2048 | 汉语、英语、西班牙语、法语、葡萄牙语、印度尼西亚语、日语、韩语、德语、俄罗斯语 |

具体调用方式可参考官方 API 文档。

### 2. 基于 Ollama 部署本地嵌入模型
前面已用 Ollama 部署本地推理模型，同样可以用它部署开源嵌入模型提供向量生成服务，例如拉取 milkey/dmeta-embedding-zh:f16 作为嵌入服务。

```
> ollama pull milkey/dmeta-embedding-zh:f16
> ollama serve
```

服务启动后，可执行以下命令测试文本向量生成是否正常。

```
> curl http://localhost:11434/api/embeddings -d '{
  "model": "all-minilm",
  "prompt": "Here is an article about llamas..."
}'
```

### 3. 基于 TEI 部署本地嵌入模型
若 Ollama 部署的嵌入模型不满足需求，可使用 Hugging Face 的 Text Embeddings Inference，简称 TEI。该工具是模型部署框架，类似 Ollama，也支持 Rerank 排序模型部署。下面以 MacOS 为例演示如何本地部署开源嵌入模型 bge-large-cn-1.5。

基本安装步骤如下：

```
#安装 rust，注意此处如果出现错误，需要修改.bash_profile 的权限
> curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

#从 GitHub 网站上下载 TEI 代码，项目名称为 text-embeddings-inference
> git clone xxxx.git

#进入代码目录，安装 metal（如果是 Intel 芯片，则修改 metal 为 mkl）
> cd text-embeddings-inference
> cargo install --path router -F metal
```

```
#启动向量服务（对于 Linux 系统来说，可能需安装 gcc/openssl:
#sudo apt-get install libssl-dev gcc -y)
#拉取模型，并启动推理服务
> model=BAAI/bge-large-zh-v1.5
> text-embeddings-inference % text-embeddings-router --model-id $model --port 8080
```

部署完成后，模型服务通过 FastAPI 暴露接口，可在浏览器访问 http://localhost:8080/docs/ 查看 TEI 的 API 文档，如图 2-14。

![](img/7eddb68cd925518fdc310e52e94ba9a9_53_0.png)

图 2-14

可对 embed 接口进行简单测试，点击该 API 并点“Try it out”，修改 Request Body 参数后执行，正常情况下会输出大量向量结果，如图 2-15。

![](img/7eddb68cd925518fdc310e52e94ba9a9_54_0.png)

图 2-15

## 2.2.4 Python 虚拟运行环境
Python 是本项目核心开发语言，简单且生态丰富。建议使用 Miniconda 搭建轻量虚拟运行环境，便于管理依赖。下面以 MacOS 为例说明准备流程，其他系统类似。

### 1. 安装 Miniconda
Miniconda 安装步骤如下。
+   (1) 访问 Miniconda 官网，进入下载中心。
(2) 根据硬件和系统选择对应安装包，例如 MacOS 下需选择 64-bit，并按 Intel x86 或 Apple M 系列芯片区分。
(3) 下载 .pkg 安装包或 .sh 安装脚本。
① 若为 .pkg，双击后按提示完成安装。
② 若为 .sh，进入下载目录执行脚本，按提示完成安装。

### 2. 验证安装
安装完成后打开终端，若使用 .sh 脚本可能需要重启终端使环境变量生效，运行以下命令：

```
> conda --version
> python --version
```

正常情况下会显示 Miniconda 与 Python 的版本号。

### 3. 搭建虚拟运行环境
Miniconda 的优势是可以为不同项目创建隔离的 Python 环境，避免库版本冲突。创建名为 rag 的虚拟环境并激活它。

```
#搭建名为 rag 的虚拟运行环境，采用默认的 Python 版本
> conda create -n rag python=3.12.1

#激活名为 rag 的虚拟运行环境
> conda activate rag
```

建议在该环境中安装第三方库，优先使用 conda install。

## 2.2.5 Python IDE 与开发插件
有了虚拟环境后，还需要一个高效的 Python IDE。选用 Visual Studio Code 并以 MacOS 为例介绍配置流程。

### 1. 安装 VS Code
+   （1）进入微软 VS Code 官网，下载对应操作系统的 Stable 版本。
（2）打开下载目录，找到应用压缩包。
（3）解压后将应用拖入 MacOS 应用程序目录。
（4）双击图标启动 VS Code。

### 2. 安装 Python 开发插件
VS Code 的扩展生态丰富，可按需增强功能。后续开发建议至少安装以下插件。
+   （1）Chinese (Simplified) Language Pack for Visual Studio Code：简体中文语言包。
（2）Python：提供代码高亮、智能提示、检查、重构与调试等功能。
（3）Jupyter：支持在 VS Code 中交互式运行 Jupyter Notebook。
（4）GitHub Copilot：基于大模型的 AI 编码助手，可提供代码补全、解释和修复建议。

插件安装方式如下。
+   （1）点击左侧扩展图标打开扩展面板。
（2）在搜索框输入插件名称进行查找。
（3）在结果列表中找到目标插件，点击安装按钮等待完成。

(4) 当插件已完成安装后，界面上将不再出现“安装”选项，取而代之的是“禁用”和“卸载”两个控制按钮。

## 2.2.6 向量数据库方案
在 RAG 场景里，嵌入模型产生的文本向量需要被长期保存并随时调用，这项工作通常交给向量数据库来承担。它能提供基于相似度的快速语义检索能力，是检索环节的核心底座。搭建 RAG 应用时，向量库的技术选型与环境部署往往是首要任务。常见的向量库大致可分为以下几类。

- （1）内存型嵌入式向量库：多用于实验或原型验证，例如 FAISS。
- （2）本地自管型向量库：部署在自有服务器上自行维护，代表有 Chroma、Milvus 以及搭载 pgvector 插件的 PostgreSQL。这类库还能再细分为嵌入式直连使用和 Client/Server 模式使用两种形态。
- （3）云托管型向量库：数据与服务均托管在云厂商侧，通过控制台与 API 访问，例如腾讯的 Tencent Cloud VectorDB、Pinecone 等。

本节挑选两款开源方案 Chroma 和 PostgreSQL+pgvector，做简要的安装与验证演示。

### 1. 部署 Chroma
Chroma 是一款高性能的开源嵌入式向量库，接口友好，可存储向量及关联元数据。它同时提供 Python 与 JavaScript SDK，可通过 pip 或 npm 安装。鉴于本项目以 Python 虚拟环境运行，直接在终端执行安装命令：

```
> pip install chromadb
```

随后需要确认 Chroma 是否可用。Chroma 支持两种使用形态：嵌入式直连和 Client/Server 服务。

+   1) 嵌入式直连方式
直接运行一段简单的 Python 探测脚本：

```
#测试 Chroma
import chromadb
client = chromadb.PersistentClient(path="./chroma_db")
client.heartbeat()
```

若返回一串纳秒级数字，说明 Chroma 已正常工作。

+   2) Client/Server 方式
先启动服务：

```
> chroma run --path ./chroma_db
```

启动成功时会看到对应提示，默认监听本机 8000 端口。

```
Running Chroma
Saving data to: ./chroma_db
Connect to chroma at: http://localhost:8000
```

再用客户端代码测试连接：

```
#测试 Chroma, Client/Server 模式
import chromadb
chroma_client = chromadb.HttpClient(host='localhost', port=8000)
chroma_client.heartbeat()
```

若能正常返回，则说明服务端与客户端均可用。

### 2. 部署 PostgreSQL 与 pgvector
PostgreSQL 是成熟的开源关系型数据库，pgvector 则是为其添加向量存储与检索能力的扩展插件，因此先部署 PostgreSQL。安装方式多样，下面以 MacOS 为例，其他系统请参考官方文档。

## 1）图形化安装
（1）进入 PostgreSQL 官网下载对应系统的安装包。
（2）打开下载目录运行安装程序，按提示完成。安装过程中需指定数据目录、默认用户密码及端口等信息，安装确认页如图 2-16 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_59_0.png)

图 2-16
（3）安装完成后数据库会自动启动。
（4）将 PostgreSQL 可执行文件加入系统环境变量。

```bash
> echo 'export PATH="/Library/PostgreSQL/15/bin:$PATH"' >> ~/.zshrc
> source ~/.zshrc
```
（5）用 `psql -U postgres` 登录，输入安装时设定的密码，若登录成功则说明服务正常。

## 2）Brew 安装
在 MacOS 上也可使用 Homebrew 安装，步骤如下：

```bash
#安装 brew 工具，如果已经安装，那么忽略，否则请自行安装 brew 工具
```

```bash
#查看 PostgreSQL 数据库的最新可安装版本，并安装，此处选择 15
> brew search postgresql
> brew install postgresql@15

#设置环境变量
> echo 'export PATH="/opt/homebrew/opt/postgresql@15/bin:$PATH"' >> ~/.zshrc
> source ~/.zshrc

#启动数据库服务
> brew services start postgresql@15
```

安装完毕后执行 `psql -d postgres` 登录，检查是否可正常连接。

## 3）添加 pgvector 扩展
要让 PostgreSQL 支持向量，需额外安装 pgvector 扩展。以 MacOS 为例：

（1）确认已安装 Xcode Command Line Tools，未安装则执行 `xcode-select --install`。
（2）在终端依次执行安装命令：

```bash
> cd /tmp
> git clone --branch v0.6.2 https://git***.com/pgvector/pgvector.git
> cd pgvector
> make
> sudo make install # may need sudo
```
（3）登录数据库，例如 `psql -U postgres`。
（4）登录后启用扩展：

```sql
psql> create extension vector;
```
（5）无报错即表示启用成功。可先建一个含向量字段的表：

```sql
psql> create table mystore(id bigserial PRIMARY KEY, embedding vector(3));
```
(6) 向 embedding 字段插入一个示例向量：

```sql
psql> insert into mystore(embedding) VALUES ('[1.1,0.2,3.8]');
```
(7) 执行查询验证插入结果：

```sql
psql> select * from mystore
```

若查询无异常，即表示全部配置完成。

## 2.2.7 LlamaIndex 集成
后续开发将以 LlamaIndex 为主框架，先安装其基础包，后续按需补充其他独立包：

```bash
> pip install llama-index
```

## 2.3 开发环境约定
至此，RAG 开发所需的环境已搭建完毕，涵盖基础大模型、嵌入模型、Python 虚拟环境、IDE 及插件、向量库、LlamaIndex 框架等。开发过程中会用到的额外 Python 库，将在使用时通过 conda install 或 pip install 按需安装，不再逐一罗列。

表 2-2 为本书采用的开发环境，可根据实际情况做调整，如更换大模型或向量库等。

| 类别 | 环境配置 | 备用环境 |
| :--- | :--- | :--- |
| 硬件 | 苹果 Macbook Pro (M3 处理器+36GB 内存) | — |
| 操作系统 | MacOS Sonoma 14.3 | — |
| 基础大模型 | Ollama 部署的本地模型 (Llama3、Qwen1.5、Qwen2 等) | OpenAI 的大模型<br>阿里云通义千问 |
| 嵌入模型 | Ollama 部署的本地模型 (dmeta-embedding-zh 等) | OpenAI 的大模型<br>TEI 的 bge-large-zh |
| Python 运行环境 | Python 版本: 3.12.2<br>Miniconda 版本: 24.1.2 | — |
| IDE | VS Code 版本: 1.88.0 | — |
| 向量库 | 本地部署的 Chroma 向量库，访问信息:<br>服务器: localhost 端口: 8000 | PostgreSQL+pgvector |
| 开发框架 | LlamaIndex 0.10.x | — |

# 【预备篇收官总结】
预备篇先回顾了大模型的发展脉络，并对 RAG 应用的产生动机、运行原理与技术架构做了系统性梳理，也简述了若干前沿话题，帮助建立对 RAG 的整体认知。

随后完成了开发环境的搭建，并确定以 LlamaIndex 作为主流开发框架来构建 RAG 应用。

接下来正式进入 RAG 应用开发阶段。

## 基础篇开启
![](img/7eddb68cd925518fdc310e52e94ba9a9_63_0.png)

## 第3章 RAG 应用实战入门
我们已了解 RAG 的典型应用场景，也看到生产实践中因需求复杂化而演进出模块化架构与更先进的工作流理念。那么如何把这些理念转化为可运行、可投产的软件？

从本章起，将介绍如何借助现有平台、工具与框架来开发真正意义上的 RAG 应用。这里的应用不仅是符合经典范式的原型，更要满足企业级业务、工程化与可维护性的要求，需经历完整测试、评估与持续优化。

本章从开发、追踪与调试切入，建立对 RAG 应用开发的技术初印象。

## 3.1 构建极简 RAG 应用
为加深对 RAG 原理的理解，将用三种不同路径实现一个简易 RAG 应用。

- (1) 原生代码实现。
- (2) 基于 LlamaIndex 框架实现。
- (3) 基于 LangChain 框架实现。

先定义一个简单场景：从本地目录读取若干 TXT 文档作为增强知识，执行切分、向量化后写入 Chroma，再调用大模型做增强生成。代码目录结构如图 3-1。

![](img/7eddb68cd925518fdc310e52e94ba9a9_66_0.png)

图 3-1

- (1) chroma_db：Chroma 向量库的存储目录。
- (2) data：存放代表领域知识的 TXT 文档，示例为文心一言简介。
- (3) src：RAG 应用的 Python 代码目录。

### 3.1.1 原生代码实现
不借助第三方框架时，需要自行完成加载、切分、嵌入、向量存储与索引、检索与生成等核心模块，并模拟用户查询或对话交互。

#### 1. 文档加载与读取
实现一个极简的 TXT 加载器，仅做路径校验并读取内容输出。提供两个函数：

```python
#加载与读取文档
import mimetypes
import os,configparser

def loadtext(path):
    path = path.rstrip()
    path = path.replace(' \n', '')

    #转换绝对路径
    filename = os.path.abspath(path)

    #判断文档存在，并获得文档类型
    filetype = ''
    if os.path.isfile(filename):
        filetype = mimetypes.guess_type(filename)[0]
    else:
        print(f"File {filename} not found")
        return None

    #读取文档内容
    text = ""
    if filetype != 'text/plain':
        return None
    else:
        with open(filename, 'rb') as f:
            text = f.read().decode('utf-8')

    return text

#这里配置了一个简单的配置器，用于读取模型名称的配置，后面要用
def getconfig():
    config = configparser.ConfigParser()
    config.read('config.ini')
    return dict(config.items("main"))
```

#### 2. 文本切分策略
直接对整篇文档做向量化检索效果不佳，需要先切分为合适粒度的知识块。切分应避免固定长度导致语义截断，本例在换行与句末处切分，并在每个块中保留若干句子。

```python
#把文档分割成知识块
import jieba,re

def split_text_by_sentences(source_text: str,
                            sentences_per_chunk: int,
                            overlap: int) -> List[str]:
    """
    简单地把文档分割为多个知识块，每个知识块都包含指定数量的句子
    """
    if sentences_per_chunk < 2:
        raise ValueError("一个句子至少要有 2 个 chunk! ")
    if overlap < 0 or overlap >= sentences_per_chunk - 1:
        raise ValueError("overlap 参数必须大于等于 0，且小于 sentences_per_chunk")

    #简化处理，用正则表达式分割句子
    sentences = re.split('(?<=[.！?])\s+', source_text)
    sentences = [sentence.strip() for sentence in sentences if sentence.strip() != '']

    if not sentences:
        print("Nothing to chunk")
        return []

    #处理 overlap 参数
    chunks = []
    i = 0
    while i < len(sentences):
        end = min(i + sentences_per_chunk, len(sentences))
        chunk = ' '.join(sentences[i:end])

        if overlap > 0 and i > 1:
            overlap_start = max(0, i - overlap)
            overlap_end = i
            overlap_chunk = ' '.join(sentences[overlap_start:overlap_end])
            chunk = overlap_chunk + ' ' + chunk

        chunks.append(chunk.strip())
        i += sentences_per_chunk

    return chunks
```

注意 overlap 参数的作用：在生成相邻块时允许一定重叠，通过重叠句子数实现语义连贯，有助于提升检索覆盖率。

文本切分是 RAG 流程的关键环节，块大小与切分策略会显著影响后续检索与生成质量，后续会进一步探讨。

#### 3. 嵌入与索引构建
这是索引准备的收尾阶段，将切分后的块经嵌入模型转为向量并写入向量库建立索引。这里复用前述加载与切分函数完成整体索引流程。

```python
import ollama, chromadb

#引入自定义模块
from load import loadtext, getconfig
from splitter import split_text_by_sentences

#向量模型
embedmodel = getconfig()["embedmodel"]

#向量库
chroma = chromadb.HttpClient(host="localhost", port=8000)
chroma.delete_collection(name="ragdb")
collection = chroma.get_or_create_collection(name="ragdb")

#读取文档列表，依次处理
with open('docs.txt') as f:
    lines = f.readlines()
    for filename in lines:
        #加载文档内容
        text = loadtext(filename)
        
        #把文档分割成知识块
        chunks = split_text_by_sentences(source_text=text,
                                         sentences_per_chunk=8,
                                         overlap=0)
        
        #对知识块依次处理
        for index, chunk in enumerate(chunks):
            #借助基于 Ollama 部署的本地嵌入模型生成向量
            embed = ollama.embeddings(model=embedmodel,
                                      prompt=chunk)['embedding']
            #存储到向量库 Chroma 中，注意这里的参数
            collection.add([filename+str(index)], [embed], documents=[chunk], metadata={"source": filename})
```

# RAG 实战：从原生代码到框架加速

先把所有要加载的 TXT 文本清单收拢到一个独立的 docs.txt 里，按顺序逐个处理。之所以不直接去 data 目录里遍历文件，是为了让数据源更可控、更好扩展。以后想加别的来源，只要在 docs.txt 里写上就行，例如配一个网络 URL，就能直接拉取对应的 HTML 网页内容。

在做前端问答之前，先验证一下前面的工作是否生效。最直接的方式是用一段轻量测试代码，看看向量库能不能完成语义检索。

先把 config.ini 里的模型相关信息配好：

```ini
[main]
embedmodel=milkey/dmeta-embedding-zh:f16
mainmodel=qwen:32b
```

随后在之前的索引准备代码下方追加测试片段：

```python
if __name__ == "__main__":
    while True:
        query = input("Enter your query: ")
        if query.lower() == 'quit':
            break
        else:
            #从向量库 Chroma 中查询与向量相似的知识块
            results = collection.query(query_embeddings=[ollama.embeddings(model=embedmodel, prompt=query)['embedding']], n_results=3)

            #打印文档内容 (Chunk)
            for result in results["documents"][0]:
                print("-------------------------------------------------")
                print(result)
```

切换到虚拟运行环境，在终端里跑刚才的索引准备脚本，比如： 等一会儿就会进入交互式提示。此时输入一个与本地 TXT 里文心一言简介相关的提问，观察是否能召回对应的知识点，效果如图 3-2 所示。①

![](img/7eddb68cd925518fdc310e52e94ba9a9_71_0.png)
看起来检索是通的，一切正常！

## 4. 检索与生成

接下来搭一个简易的交互式问答程序：用户输入问题后，从已做向量化的私有知识库里检索相关片段，再把这些片段作为上下文喂给大模型生成回答：

```python
import ollama, sys, chromadb
from load import getconfig

#嵌入模型与大模型
embedmodel = getconfig()["embedmodel"]
llmmodel = getconfig()["llmmodel"]

#向量库
chroma = chromadb.HttpClient(host="localhost", port=8000)
collection = chroma.get_or_create_collection("ragdb")

while True:
    query = input("Enter your query: ")
```

① 部分程序的输出篇幅较长，截图时只保留关键部分。

```python
if query.lower() == 'quit':
    break
else:
    #生成查询向量
    queryembed = ollama.embeddings(model=embedmodel,
                                   prompt=query)['embedding']

    #用查询向量检索上下文
    relevantdocs = collection.query(query_embeddings=[queryembed],
                                    n_results=5)["documents"][0]
    docs = "\n\n".join(relevantdocs)

    #生成 Prompt
    modelquery = f"""
    请基于以下的上下文回答问题，如果上下文中不包含足够的回答问题的信息，请回答'
    我暂时无法回答该问题'，不要编造。

    上下文:
    ====
    {docs}
    ====

    我的问题是: {query}
    """

    #交给大模型进行生成
    stream = ollama.generate(model=llmmodel, prompt=modelquery,
                             stream=True)

    #流式输出生成的结果
    for chunk in stream:
        if chunk["response"]:
            print(chunk['response'], end='', flush=True)
```

检索与生成的执行思路主要分四步。

（1）拿到用户问题后先做向量化。这里调用 ollama.embeddings 用本地嵌入模型把问题转成查询向量。
（2）用查询向量去向量库里找相似的知识块。借助 Chroma 的 query 方法完成语义检索，通常要指定查询向量、返回条数、近似搜索策略等参数。
（3）把检索到的片段保存在 docs 变量中，和原始 query 一起拼接到 Prompt 里作为上下文。
（4）调用大模型的生成接口得到最终答案。这里用 ollama.generate 来生成，参数一般包括模型名、提示词、是否流式、temperature 等，具体以各模型文档为准。

到这里，我们没有引入任何第三方 RAG 框架，完全用原生 Python 写出了一个“朴素”的 RAG 示例，完整演示了 RAG 的基础链路：文档加载与读取、文本切分、向量化、向量存储与索引、检索与生成。

启动这个交互程序，例如：

```bash
> python chat.py
```

输入你的查询问题：

```bash
Enter your query: 百度文心一言的主要应用场景有哪些呢？
```

若配置无误，终端里会呈现类似图 3-3 的输出。

![](img/7eddb68cd925518fdc310e52e94ba9a9_73_0.png)

这个原型只是基于终端的演示版本，和真正可用的生产级、尤其是企业级应用还有明显差距。可以思考以下实际问题。

(1) 知识索引的构建与使用往往是解耦、异步进行的，该如何设计？
(2) 文档采集、加载、索引是否需要一套可视化且功能完整的管控工具？
(3) 原始文档与切分后的知识块如何建立对应关系，后续的维护与更新怎样同步？
(4) 检索与生成环节常常需要通过 API 和外部系统对接，该怎么做？
(5) 管理与使用是否要支持多用户并发，而不是单用户命令行？
(6) 如果用户通过网页 UI 而非命令行交互，如何保存对话历史？
(7) 生成结果怎样追溯到具体的知识块或源文档？

这些在真实落地时都会遇到，有的会在本书后续章节里展开，有的则与 RAG 核心技术关联不大，需要结合自身需求在开发中解决。

前面用原生代码搭最简 RAG，目的就是从底层看清 RAG 的原理，这对后续理解并使用专业框架、以及掌握更高阶的 RAG 范式和优化技巧很有帮助。显然，这个示例在鲁棒性、可扩展性、可维护性上都比较初级，作为教学原型可以，但不适合直接上线。

因此才需要借助主流的大模型应用开发框架来做工程化。

## 3.1.2 使用 LlamaIndex 框架开发

LlamaIndex 官方文档里有一个经典的 5 行代码入门示例，我们就从这里开始。

```python
# 经典的 5 行代码的 RAG 应用

# 加载文档
documents = SimpleDirectoryReader("../data").load_data()

# 构造向量存储索引
index = VectorStoreIndex.from_documents(documents)
```

```python
#构造查询引擎
query_engine = index.as_query_engine()

#对查询引擎提问
response = query_engine.query('这里放入 data 目录中知识相关的问题')

#输出答案
print(response)
```

把这段代码跑起来，只需在 data 目录里放一两个测试文档，比如 TXT 或 PDF，就能直接提问文档里的内容。

虽然极简，但足以体现成熟框架的效率：靠内置组件和集成能力，把大量细节封装起来，开发速度大幅提升。拆开来看，它做了这几件事：

- （1）用 SimpleDirectoryReader 从指定目录 ../data 加载并读取知识。
- （2）用 VectorStoreIndex 对加载的文档做嵌入和索引。
- （3）在向量存储上构造 query_engine，负责检索与生成。
- （4）默认使用 OpenAI 的 GPT-3.5-Turbo 作为大模型、内存向量库作为存储、text-embedding-3-small 作为嵌入模型。

LlamaIndex 在未显式配置时默认走 OpenAI 系列模型，所以运行需要设置 OPENAI_API_KEY 环境变量。若你用 ONE-API 这类统一分发平台，则把 OPENAI_API_BASE 指向服务地址，并用平台提供的密钥填入 OPENAI_API_KEY。

现在用 LlamaIndex 来复现 3.1.1 节的最小 RAG，两种写法差异会很直观。下面是代码：

```python
import chromadb

from llama_index.core import VectorStoreIndex,StorageContext,
from llama_index.core import SimpleDirectoryReader,Settings
from llama_index.core.node_parser import SentenceSplitter
```

```python
from llama_index.vector_stores.chroma import ChromaVectorStore
from llama_index.llms.ollama import Ollama
from llama_index.embeddings.ollama import OllamaEmbedding

#设置模型
Settings.llm = Ollama(model="qwen:14b")
Settings.embed_model = OllamaEmbedding(model_name="milkey/dmeta-embedding-zh:f16")

#加载与读取文档
reader = SimpleDirectoryReader(input_files=["../../../data/yiyan.txt","../../../data/HR.txt"])
documents = reader.load_data()

#分割文档
node_parser = SentenceSplitter(chunk_size=500, chunk_overlap=20)
nodes = node_parser.get_nodes_from_documents(documents, show_progress=False)

#准备向量存储
chroma = chromadb.HttpClient(host="localhost", port=8000)
chroma.delete_collection(name="ragdb")
collection = chroma.get_or_create_collection(name="ragdb", metadata={"hnsw:space": "cosine"})
vector_store = ChromaVectorStore(chroma_collection=collection)

#准备向量存储索引
storage_context = StorageContext.from_defaults(vector_store=vector_store)
index = VectorStoreIndex(nodes, storage_context=storage_context)

#构造查询引擎
query_engine = index.as_query_engine()

while True:
    user_input = input("问题: ")
    if user_input.lower() == "exit":
        break

    response = query_engine.query(user_input)
    print("AI 助手: ", response.response)
```

要点简述如下：

- （1）模型设置：借助 LlamaIndex 的 Settings 全局组件指定大模型和嵌入模型，未指定时会回退到 OpenAI 默认。这里用 Ollama 本地部署的 qwen:14b 作为生成模型，dmeta-embedding-zh:f16 作为中文嵌入模型。
- （2）文档加载与读取：用框架的目录加载器读取两个 TXT，得到 Document 对象，这是 LlamaIndex 对知识文档的抽象。
- （3）文档切分：使用 SentenceSplitter 完成分句切分，输出 Node 对象，即框架里的知识块抽象，无需手写切分逻辑。
- （4）向量存储准备：采用本地 Chroma 作为向量库，先建一个 collection，再封装成 ChromaVectorStore 对象。
- （5）向量索引准备：把切分好的 Node 与 ChromaVectorStore 组合成索引，框架会自动完成嵌入并写入 Chroma，无需额外编码。
- （6）查询引擎构造：一行 query_engine=index.as_query_engine() 就能得到一个可直接检索并生成的引擎，后续的检索与生成流程被自动托管。

代码规模大约只有纯手写实现的二成左右，功能上却能完全等同。实际工程化时，这份价值远超砍掉八成代码的表面收益。依托成熟框架，除了省代码，还能获得很强的灵活性、可扩展性和可维护性。例如想新增文档载入格式、更换底座大模型或向量存储、调整分块方式与参数，都能平滑变更，不会把项目拖成难以梳理的“空心面条”式代码。

## 3.1.3 用 LangChain 框架实现

本书的 RAG 样例主要以 LlamaIndex 为主线，但在主流的大模型应用开发框架里，LangChain 同样是极具实力的选择。下面以本章的示例为参照，对比一下 LangChain 下的代码写法，有助于更全面地理解不同框架的能力。

下面直接给出 LangChain 的实现片段：

```python
import chromadb
from langchain_community.llms import Ollama
from langchain_community.embeddings import OllamaEmbeddings
from langchain import hub
from langchain_community.vectorstores import Chroma
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import DirectoryLoader
from langchain_community.document_loaders import TextLoader

#模型
llm = Ollama(model="qwen:14b")
embed_model = OllamaEmbeddings(model="milky/dmeta-embedding-zh:f16")

#加载与读取文档
loader = DirectoryLoader('../..//data/', glob="*.txt", exclude="*tips*.txt", loader_cls=TextLoader)
documents = loader.load()

#分割文档
text_splitter = RecursiveCharacterTextSplitter(chunk_size=500, chunk_overlap=20)
splits = text_splitter.split_documents(documents)

#准备向量存储
chroma = chromadb.HttpClient(host="localhost", port=8000)
chroma.delete_collection(name="ragdb")
```

```python
collection = chroma.get_or_create_collection(name="ragdb", metadata={"hnsw:space": "cosine"})
db = Chroma(client=chroma, collection_name="ragdb", embedding_function=embed_model)

#存储到向量库中,构造索引
db.add_documents(splits)

#使用检索器
retriever = db.as_retriever()

#构造一个 RAG “链” (使用 LangChain 框架特有的组件与表达语言)
prompt = hub.pull("rlm/rag-prompt")
rag_chain = (
	{"context": retriever | (lambda docs: "\n\n".join(doc.page_content for doc in docs)), "question": RunnablePassthrough()}
	| prompt
	| llm
	| StrOutputParser()
)

while True:
	user_input = input("问题: ")
	if user_input.lower() == "exit":
		break

	response = rag_chain.invoke(user_input)
	print("AI 助手: ", response)
```

看下来，LangChain 与 LlamaIndex 在整体设计思路和上手体验上颇为相近，也有不少细微差别。文档加载与读取、文本切分、向量库配置这两块，两者的做法基本趋同，核心差异集中在：

(1) LangChain 里可以直接调用向量库对象的 add_documents 方法来构建索引，不需要像 LlamaIndex 那样额外创建 VectorStoreIndex。

(2) 更关键的区别在于 LangChain 的核心抽象 Chain。在检索-生成环节，LangChain 借助自家的 LCEL 表达语言拼出一个 rag_chain，这个链条从原始问题出发，经过检索、提示组装、模型调用、输出解析，一气呵成，代码层面只剩一个简洁表达式，其余细节由框架接管。

## 3.2 RAG 应用的跟踪与调试方法

后续的开发以相对成熟的 LlamaIndex 为主，这大幅提升了效率，但也因为高度抽象封装，底层运行细节被遮住不少，排查问题和调优时就会吃亏。很多时候我们需要直观地看到模型的真实输入输出，以判断检索是否精准、模型是否发挥正常。这就需要一套简单可用的内部追踪机制。结合 LlamaIndex 自身的能力和生态工具，有两类常用做法可供准备。

### 3.2.1 借助 LlamaDebugHandler

LlamaIndex 在构造组件时支持传入多个回调类。这些回调会在框架定义的若干关键事件的开始与结束时触发，用来记录执行步骤、时间戳、输入与输出等信息。LlamaDebugHandler 就是专门用于调试记录的回调。LlamaIndex 的所有回调都由全局 Settings 中的 callback_manager 统一管理，因此只要实例化一个 LlamaDebugHandler 并挂到 callback_manager 上，就能对指定组件或全局生效。

1. 设置调试处理器

先导入所需模块：

```python
from llama_index.core.callbacks import (
    CallbackManager,
    LlamaDebugHandler,
    CBEventType,
)
```

实例化 LlamaDebugHandler：

```python
llama_debug = LlamaDebugHandler(print_trace_on_end=True)
```

参数 print_trace_on_end 控制事件结束时是否立刻打印简要追踪。然后把回调加入 CallbackManager，并在 Settings 中指定，即可启用全局追踪：

```python
callback_manager = CallbackManager([llama_debug])
Settings.callback_manager = callback_manager
```

当然也可以限定到组件级别，比如在创建向量存储索引时注入跟踪器：

```python
index = VectorStoreIndex.from_documents(
    docs, callback_manager=callback_manager
)
```

2. 使用跟踪与调试信息

装好回调后，关键事件触发时会自动记录。在 LlamaIndex 当前版本里，主要事件包括（后续版本可能调整，请以官方文档为准）：

- （1）CHUNKING：文本切分事件。
- （2）NODE_PARSING：Node 解析事件。
- （3）EMBEDDING: 文本嵌入事件。
- （4）LLM: 大模型调用事件。
- （5）QUERY: 通过 query 引擎调用 query 方法事件。
- （6）RETRIEVE: 语义检索相关知识事件。
- （7）SYNTHESIZE: 组装 Prompt 并用大模型生成结果事件。
- （8）TREE: 生成文本摘要信息事件。
- （9）SUB_QUESTION: 生成子问题事件。

因此可以调用 llama_debug 对象的接口，按关注事件提取信息，例如拿到事件耗时：

```python
pprint.pprint(llama_debug.get_event_time_info(CBEventType.QUERY))
```

输出里能看到对应事件的时间信息（见图 3-4）。

![](img/7eddb68cd925518fdc310e52e94ba9a9_82_0.png)

也可以用 get_event_pairs 获取更详尽的事件对：

```python
pprint.pprint(llama_debug.get_event_pairs(CBEventType.QUERY))
```

随后就能看到事件开始与结束时框架记录的完整细节（见图 3-5）。

![](img/7eddb68cd925518fdc310e52e94ba9a9_82_1.png)

若想看整体调用栈与耗时，可以用 print_trace_map 打印：

```python
llama_debug.print_trace_map()
```

结果类似图 3-6 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_83_0.png)

图 3-6

### 3.2.2 借助第三方追踪与调试平台

随着大模型应用增多，面向生产就绪的工程化平台快速发展，主要用于跟踪、调试、测试、评估、管理数据集等。LangChain 公司的 LangSmith 就是其中之一，而 LlamaIndex 也接入了不少第三方平台。下面以常见的 Langfuse 为例介绍。

Langfuse 是一个开源的大模型工程化平台，提供追踪、评估、提示管理等能力，帮助应用更快上线。它前后端一体：应用通过简单 SDK 生成追踪与指标并自动推送到 Langfuse，随后可在前端 UI 直观查看。

LlamaIndex 已内建对 Langfuse 的支持。可使用 Langfuse 云服务 Langfuse Cloud（见图 3-7），也可自建。

条件允许的话，推荐拉取 Langfuse 开源仓库本地部署。

1. 申请 API Key

打开 Langfuse 前端（云端或本地），注册登录后即可申请 API Key 获取使用权限（见图 3-8）。

![](img/7eddb68cd925518fdc310e52e94ba9a9_84_0.png)

2. 应用开发集成

基于 LlamaIndex 的项目只需几行配置即可接入，做法与 3.2.1 节的 LlamaDebugHandler 类似：

```python
from llama_index.core.callbacks import CallbackManager
from langfuse.llama_index import LlamaIndexCallbackHandler

#设置 Langfuse 平台的 API Key，参考上方申请
os.environ["LANGFUSE_SECRET_KEY"] = "sk-*****"
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-*****"
os.environ["LANGFUSE_HOST"] = "https://xx.xx.xx"

#构造 Langfuse 平台的回调类
langfuse_callback_handler = LlamaIndexCallbackHandler()

#设置到全局的 callback_manager
Settings.callback_manager = CallbackManager([langfuse_callback_handler])

......
#程序退出之前注意缓存，将缓存的跟踪信息发送到 Langfuse Server 端
langfuse_callback_handler.flush()
......
```

3. 用 Langfuse UI 观察与追踪

集成完成后，Index、Query 等事件的追踪信息和性能指标会自动汇总到 Langfuse。打开 UI 页面即可浏览（见图 3-9）。

![](img/7eddb68cd925518fdc310e52e94ba9a9_85_0.png)

图 3-9

点开一次查询的追踪，能清楚看到执行流程和每一步内情，比如查询时检索到的参考知识块（见图 3-10）。

![](img/7eddb68cd925518fdc310e52e94ba9a9_86_0.png)

图 3-10

图 3-11 展示了一次模型调用的完整输入输出。

![](img/7eddb68cd925518fdc310e52e94ba9a9_86_1.png)

图 3-11

Langfuse 还提供更多辅助功能，如 Prompt 模板管理、指标生成、调用成本统计、评估数据集管理与在线评估等，可自行探索。

![](img/7eddb68cd925518fdc310e52e94ba9a9_86_2.png)

## 3.3 基于 LlamaIndex 的 RAG 核心组件准备

LlamaIndex 内置了大量封装良好、设计规范的 RAG 组件，覆盖开发流程的各个阶段和不同场景。这些组件并非松散堆砌，而是通过组合、派生、集成形成完整的集成框架，使用上既能快速上手，也能灵活定制。既可以用高度集成的上层 API 快速搭建，也能下沉到底层 API 实现更精细的控制，或在现有组件上派生自己的实现。

强大的同时也意味着一定学习成本。为此按 RAG 的典型流程对核心组件做归类，循序讲解，最终达成端到端应用开发能力。

图 3-12 为 LlamaIndex 核心组件的学习路线图。

![](img/7eddb68cd925518fdc310e52e94ba9a9_87_0.png)

图 3-12

## 第4章 模型与 Prompt

典型的 RAG 应用一般涉及两类模型：大模型，作为推理与生成的“大脑”，是任何大模型应用的基础设施；嵌入模型，用于把文档向量化并支撑语义检索。复杂流程中可能同时使用多个模型，开发者可按任务特点、性能需求和资源限制灵活选择。

与大模型紧密相关的是 Prompt。它是驱动模型行为的核心输入。Prompt 的作用并不局限于最终生成阶段，RAG 流程中摘要生成、查询改写、路由决策、Agent 推理、响应评估等环节都会用到。由于不同模型、场景、语言环境下的响应差异并不完全可预测，框架自带的默认 Prompt 模板往往不够理想，调整 Prompt 也是基础能力之一。

## 4.1 大模型

几乎所有的大模型应用框架都会对底层 API 做抽象封装，提供简洁调用和模型切换能力。

![](img/7eddb68cd925518fdc310e52e94ba9a9_88_0.png)

### 4.1.1 大模型在 RAG 中的作用

大模型具备强大的理解与生成能力，在 RAG 流程中的典型位置有：

- （1）数据前期准备。数据整理、清洗、抽取是 RAG 建设的重头戏。借助大模型的理解与生成能力可大幅提效，例如对知识文档做梳理与总结、抽取问答对、生成问答样本、去重、结构化与规范化等。
- （2）加载与索引。此阶段可利用大模型：
    - ① 为知识生成补充元数据，如标题、假设性问答对等。

LlamaIndex 里的大模型组件与 RAG 全流程实战笔记

在 RAG 构建里，大模型不只是最后的回答者，它会在前后端多个环节发挥作用。

- ② 给知识文本做摘要，把摘要当作索引的构建材料
- ③ 把复杂文档中的表格信息转写成自然语言的描述与概括文本
- ④ 让大模型判断一段数据的相关度，决定是否值得纳入索引

- （3）检索与生成。这一阶段大模型主要负责：
  - ① 查询路由。根据输入意图借助大模型进行分流决策
  - ② 查询扩展或重写。对原始问题做改写或扩充，提升检索命中率
  - ③ 答案生成。结合问题和检索到的上下文生成最终输出
  - ④ 响应合成。面对多个子问题时，用大模型把子答案整合成统一回复

- （4）应用评估。RAG 上线前可以用大模型做整体压测：
  - ① 调用大模型自动生成结构化评测数据集
  - ② 对生成结果做多维度打分，比如相关性、正确性等

### 4.1.2 大模型组件的统一接口规范

LlamaIndex 里的大模型组件既能单独拉出来用，也能作为参数塞进索引、检索器、查询引擎等核心模块。框架为此抽象了一套统一的调用契约，开发者只要按这套契约接入，就能一致地使用不同厂商的模型。下面是接口的定义：
```python
class BaseLLM(ChainableMixin, BaseComponent):
    """BaseLLM interface."""

    @abstractmethod
    def metadata(self) -> LLMMetadata:

    @abstractmethod
    def chat(self, messages: Sequence[ChatMessage], **kwargs: Any) -> ChatResponse:

    @abstractmethod
    def complete(
        self, prompt: str, formatted: bool = False, **kwargs: Any
    ) -> CompletionResponse:

    @abstractmethod
    def stream_chat(
        self, messages: Sequence[ChatMessage], **kwargs: Any
    ) -> ChatResponseGen:

    @abstractmethod
    def stream_complete(
        self, prompt: str, formatted: bool = False, **kwargs: Any
    ) -> CompletionResponseGen:

    # ===== Async Endpoints =====
    @abstractmethod
    async def achat(
        self, messages: Sequence[ChatMessage], **kwargs: Any
    ) -> ChatResponse:

    @abstractmethod
    async def acomplete(
        self, prompt: str, formatted: bool = False, **kwargs: Any
    ) -> CompletionResponse:

    @abstractmethod
    async def astream_chat(
        self, messages: Sequence[ChatMessage], **kwargs: Any
    ) -> ChatResponseAsyncGen:

    @abstractmethod
    async def astream_complete(
        self, prompt: str, formatted: bool = False, **kwargs: Any
    ) -> CompletionResponseAsyncGen:
```

换句话说，一个具体模型组件必须覆盖这几件事：

- （1）提供元信息，通常是模型的基本描述
- （2）同时支持补全 complete 和对话 chat 两种调用方式
- （3）同时支持流式 stream 和非流式输出
- （4）同时支持同步调用和异步 async 调用

具体实现上会按此规范完成。以 OpenAI 组件为例，其内部实现里加粗的部分就是直接走 OpenAI 官方 SDK 去请求模型：
```python
......
def _complete(self, prompt: str, **kwargs: Any) -> CompletionResponse:
    client = self._get_client()
    all_kwargs = self._get_model_kwargs(**kwargs)
    self._update_max_tokens(all_kwargs, prompt)

    response = client.completions.create(
        prompt=prompt,
        stream=False,
        **all_kwargs,
    )
    text = response.choices[0].text
......
return CompletionResponse(
```

### 4.1.3 大模型组件的独立使用

LlamaIndex 的模型组件可以单独跑出来做连通性验证和可用性测试。比如用下面这段代码快速测一下 OpenAI GPT 是否能连上，需要提前配置环境变量 OPENAI_API_KEY：
```python
from llama_index.core.llms import ChatMessage
from llama_index.llms.openai import OpenAI

#测试 complete 接口
llm = OpenAI(model='gpt-3.5-turbo-1106')
resp = llm.complete("白居易是")
print(resp)

#测试 chat 接口
messages = [
    ChatMessage(
        role="system", content="你是一个聪明的 AI 助手"
    ),
    ChatMessage(role="user", content="你叫什么名字？"),
]
resp = llm.chat(messages)
print(resp)
```

想换成本地 Ollama 也很简单，只需要少量改动即可切换：
```python
from llama_index.llms.ollama import Ollama
llm = Ollama(model='qwen:14b')
```

这正是框架设计的价值所在：通过统一的设计模式，可以在不关心底层 API 差异的情况下快速替换模型，实现配置化。

### 4.1.4 大模型组件的组合使用

工程实践里很少直接裸调模型，更多是把模型对象动态注入到其他模块，让模块按需调用。

1. 修改默认模型
框架默认使用 OpenAI 的 GPT，通过 Settings 组件可以全局改写默认模型：
```python
#通过设置 Settings 组件更改使用的默认的大模型
llm = OpenAI(model='gpt-3.5-turbo-1106')
Settings.llm = llm
```

2. 显式注入到模块
使用索引、检索器等时，也可以直接传入已构造好的模型对象。例如在建索引时指定查询阶段使用的模型。通常只要构造方法里出现 llm 参数，就意味着支持动态指定：
```python
llm = OpenAI(temperature=0.1, model="gpt-4")
index = KeywordTableIndex.from_documents(documents, llm=llm)
query_engine = index.as_query_engine() #后面查询将使用这里定义的大模型
```

### 4.1.5 大模型参数的认知与配置

不同模型可配置的参数各有差异，常见的是模型名称、随机性温度 temperature、上下文窗口大小等 token 级别的限制，部分模型还有专属参数。

参数大致分两类：

- （1）模型定义参数，如模型名、文档路径、服务端口
- （2）模型生成参数，如温度、上下文窗口

在 LlamaIndex 中主要有两个配置入口：

### 1. 在大模型初始化时设置
构造模型对象时直接传参：
```python
from llama_index.llms.ollama import Ollama
_MODEL_KWARGS = {
    "base_url":"http://localhost:11434",
    "model":"qwen:14b" ,
    "context_window":4096,
    "request_timeout":60.0
}
llm = Ollama(**_MODEL_KWARGS)
```

接入方式不同、可配置项也不同，例如 Ollama、Llama_cpp、vLLM 等推理工具各有说明。以 Llama_cpp 服务为例的配置：
```python
from llama_index.llms.llama_cpp import LlamaCPP
_MODEL_KWARGS = {"logits_all": True, "n_ctx": 2048, "n_gpu_layers": -1}
_GENERATE_KWARGS = {"temperature": 0.0,"top_p": 1.0,"max_tokens": 500,
"logprobs": 32016,
}
model_path = Path(download_dir) / "selfrag_llama2_7b.q4_k_m.gguf"
llm = LlamaCPP(model_path=str(model_path),
model_kwargs=_MODEL_KWARGS,
generate_kwargs=_GENERATE_KWARGS,verbose=False)
```

### 2. 在 Settings 组件中设置
少量全局参数可以在 Settings 里先行设定，比如上下文窗口大小，后续实例化时可被覆盖：
```python
from llama_index.core import Settings
Settings.context_window = 4096
Settings.num_output = 256
```

### 4.1.6 自建大模型组件方法

前面提到的 Llama_cpp、Ollama 其实都是自定义组件。要自建一个，只需继承基础类 CustomLLM 并实现必要接口，在内部调用自己的本地模型或私有 API 即可。

一个自定义组件的示例：
```python
from typing import Any

from llama_index.core.llms import (
    CustomLLM,
    CompletionResponse,
    CompletionResponseGen,
    LLMMetadata,
)
from llama_index.core.llms.callbacks import llm_completion_callback

class MyLLM(CustomLLM):
    model_name: str = "custom"
    dummy_response = "你好，我是一个正在开发中的大模型······"

#实现 metadata 接口
@property
def metadata(self) -> LLMMetadata:
    return LLMMetadata(
        model_name=self.model_name,
    )

#实现 complete 接口
@llm_completion_callback()
def complete(self, prompt: str, **kwargs: Any) -> CompletionResponse:
    return CompletionResponse(text=self.dummy_response)

#实现 stream_complete 接口
@llm_completion_callback()
def stream_complete(
    self, prompt: str, **kwargs: Any
) -> CompletionResponseGen:
    response = ""
    for token in self.dummy_response:
        response += token
        yield CompletionResponse(text=response, delta=token)
```

简单验证一下：
```python
llm = MyLLM()
resp = llm.complete('你好! ')
print(resp)
```

运行后可以看到输出结果：
```bash
> python llms_cust.py
你好，我是一个正在开发中的大模型······
```

构建完成后即可像内置组件一样替换使用：
```python
#替换全局默认的大模型组件
Settings.llm = MyLLM()
```

### 4.1.7 调用 LangChain 中的大模型

LlamaIndex 已内置了大量常见模型，同时还提供了对 LangChain 模型的适配器，方便复用 LangChain 生态。下面以接入百度千帆为例：
```python
from llama_index.llms.langchain import LangChainLLM
from langchain_community.llms import QianfanLLMEndpoint
llm = LangChainLLM(llm=QianfanLLMEndpoint(model='ERNIE-Bot-4'))
Settings.llm=llm
```

核心做法是使用 LlamaIndex 提供的 LangChainLLM，把 LangChain 声明的模型适配成 LlamaIndex 的统一接口，就能直接使用。

## 4.2 Prompt 机制

RAG 系统与大模型的沟通载体就是 Prompt。它是获取模型输出的最基本输入。RAG 开发的各个环节都会用到 Prompt，先弄清楚 Prompt 的使用方式很重要。

### 4.2.1 Prompt 模板的运用

Prompt 模板是 LlamaIndex、LangChain 这类框架的基础组件，用来定义带有变量占位符的提示词，运行时通过代码格式化填充成最终 Prompt。

下面是一个在 RAG 生成增强场景中常用的 Prompt 模板及其填充方式：
```python
from llama_index.core import PromptTemplate
from llama_index.llms.openai import OpenAI

template = (
    "以下是提供的上下文信息: \n"
    "-------------------\n"
    "{context_str}"
    "\n-------------------\n"
    "根据这些信息，请回答以下问题: {query_str}\n"
)
qa_template = PromptTemplate(template)

prompt = qa_template.format(context_str='小米 15 PRO 是小米公司最新推出的 6.7 寸大屏旗舰手机。', query_str='小米 15pro 的屏幕尺寸是多少？')
print(prompt)

messages = qa_template.format_messages(context_str='小米 15 PRO 是小米公司最新推出的 6.7 寸大屏旗舰手机。', query_str='小米 15pro 的屏幕尺寸是多少？')
print(messages)
```

调用 `format` 或 `format_messages` 就能把 Prompt 模板从占位符状态变成可用状态。`format` 的结果是普通字符串，适合大模型的一次性问答或查询；`format_messages` 的结果是 ChatMessage 封装，里面至少包含角色和内容，专门服务于对话模型的连续多轮上下文。

### 4.2.2 自定义默认 Prompt 模板

在基于框架搭建大模型应用，尤其是 RAG 应用的实际场景里，很多人根本不会去管 Prompt 模板，具体原因有两个。

- （1）框架本身内置了大量经过验证的默认模板，能省去不少重复劳动。
- （2）模板的填充过程通常由框架自动完成，比如在返回结果时自动把相关上下文和用户问题注入进去。

在 LlamaIndex 里，所有系统默认的 Prompt 模板都放在 `llama-index-core-prompts` 模块目录下。最常见的问答类模板示例如图 4-1 所示。

```python
DEFAULT_TEXT_QA_PROMPT_TMPL = (
    "Context information is below.\n"
    "--------------------------\n"
    "{context_str}\n"
    "--------------------------\n"
    "Given the context information and not prior knowledge, "
    "answer the query.\n"
    "Query: {query_str}\n"
    "Answer: "
)
DEFAULT_TEXT_QA_PROMPT = PromptTemplate(
    DEFAULT_TEXT_QA_PROMPT_TMPL, prompt_type=PromptType.QUESTION_ANSWER
)
```

图 4-1

有时候我们确实需要改动这些默认值，比如想把英文模板换成中文模板以适配国内模型。该怎么做？RAG 应用往往涉及多个前后端流程，先要明确改动发生在哪个环节、由哪个组件负责，再通过该组件提供的接口进行设置。

LlamaIndex 中只要依赖 Prompt 模板的组件，都可以通过 `get_prompts` 和 `update_prompts` 来查看或修改。以查询引擎为例，查看它当前使用的查询模板可以这样写：

```python
......
query_engine = index.as_query_engine()
prompts_dict = query_engine.get_prompts()
pprint.pprint(prompts_dict.keys())
```

输出结果如图 4-2。

```python
dict_keys(['response_synthesizer:text_qa_template', 'response_synthesizer:refine_template'])
```

图 4-2

返回的是一个字典，因为单个组件内部可能同时用到多个模板。比如取出其中名为 `text_qa_template` 的模板：

```python
pprint.pprint(prompts_dict["response_synthesizer:text_qa_template"])
```

就能得到如图 4-3 所示的默认内容。

```python
('Context information is below.\n'
'-------------------\n'
'{context_str}\n'
'-------------------\n'
'Given the context information and not prior knowledge, answer the query.\n'
'Query: {query_str}\n'
'Answer: ')
```

![](img/7eddb68cd925518fdc310e52e94ba9a9_100_0.png)

如果要替换，比如换成中文模板，可以执行：

```python
my_qa_prompt_tmpl_str = (
"以下是上下文信息。\n"
"-------------------\n"
"{context_str}\n"
"-------------------\n"
"根据上下文信息回答问题，不要依赖预置知识，不要编造。\n"
"问题：{query_str}\n"
"回答："
)
my_qa_prompt_tmpl = PromptTemplate(my_qa_prompt_tmpl_str)
query_engine.update_prompts(
    {"response_synthesizer:text_qa_template": my_qa_prompt_tmpl}
)
```

通过 `update_prompts` 完成了查询引擎默认模板的替换。注意：`context_str` 与 `query_str` 这两个占位符的名字不要改动，否则运行时会找不到变量而报错。

除了事后更新，也可以在初始化组件时直接传入模板，例如把查询引擎的构造方式改成：

```python
......
query_engine = 
index.as_query_engine(text_qa_template=my_qa_prompt_tmpl)
```

效果与调用 `update_prompts` 完全一致。LlamaIndex 里其他组件也遵循同样思路，先看初始化参数支持哪些模板，再决定要覆盖哪一个。

### 4.2.3 调整 Prompt 模板的变量名

上一节提到，个性化模板时不要随意改动变量名。万一确实需要更易读的名字怎么办？可以借助模板对象的 `template_var_mappings` 参数，建立自己的名字和官方要求的名字的映射关系：

```python
my_qa_prompt_tmpl_str = (
    "以下是上下文信息。\n"
    "-------------------\n"
    "{my_context_str}\n"
    "-------------------\n"
    "根据上下文信息回答问题，不要依赖预置知识，不要编造。\n"
    "问题：{my_query_str}\n"
    "回答："
)
template_var_mappings = {"context_str": "my_context_str",
                         "query_str": "my_query_str"}
my_qa_prompt_tmpl = PromptTemplate(my_qa_prompt_tmpl_str)

# 使用自定义变量来格式化 Prompt 模板
print(my_qa_prompt_tmpl.format(my_context_str="......",
                               my_query_str="......"))
```

上面这段代码把官方要求的 `context_str` 映射到了我们自定义的 `my_context_str`。还能更进一步，通过 `function_mappings` 把 `context_str` 映射到一个函数上。这样在格式化时，`context_str` 的实际取值会由该函数动态生成，函数的入参来自 `format` 调用时传进去的关键字参数。实现方式如下：

```python
# kwargs 为调用 format 方法时携带的关键词参数
def fn_context_str(**kwargs):
    # 自定义逻辑
    return fmtted_context

prompt_tmpl = PromptTemplate(
    qa_prompt_tmpl_str,
    function_mappings={"context_str": fn_context_str}
)

# format 参数传入 fn_context_str 变量中
prompt_tmpl.format(context_str="...", query_str="...")
```

## 4.3 嵌入模型

### 4.3.1 嵌入模型在 RAG 中的角色

在 RAG 流程里，嵌入模型的核心任务是把切分好的知识块转成向量。这一步本质上是把自然语言映射成一组数值，让机器更容易理解、存储和计算，同时保留语义信息。

因为向量能体现文本语义，所以可以做语义相似检索。不同于关键词匹配，语义检索是基于向量之间的相似度来判断相关性，通常会用余弦相似度、点积等方法计算。这种在海量向量中找相似的能力，正是向量库的关键所在。本书主要讨论文本嵌入，但其他模态的知识也可以通过专用模型嵌入。

嵌入模型贯穿 RAG 的索引和生成两个阶段。

- （1）索引阶段：把知识块向量化后存入向量库。
- （2）生成阶段：把用户问题向量化，通过向量库检索出语义相近的知识块，用于增强生成。

想对比不同嵌入模型的效果，或给自己的模型做评测，可以参考 Hugging Face 上发布的 Massive Text Embedding Benchmark，MTEB，以及对应的开源项目。

### 4.3.2 嵌入模型组件的核心接口

直观来看，嵌入模型组件最核心的能力就是把文本生成向量。参考 LlamaIndex 嵌入模型基类的设计，主要接口如下：

```python
# 用于保存嵌入后的向量
Embedding = List[float]

# 相似度计算的 3 种算法：余弦相似度、点积、欧几里得距离
class SimilarityMode(str, Enum):
    """Modes for similarity/distance."""

    DEFAULT = "cosine"
    DOT_PRODUCT = "dot_product"
    EUCLIDEAN = "euclidean"

# 辅助方法：两个向量相似度比较
def similarity(
    embedding1: Embedding,
    embedding2: Embedding,
    mode: SimilarityMode = SimilarityMode.DEFAULT,
) -> float:
    ...

# 嵌入模型基础类
class BaseEmbedding(TransformComponent):
    ...
    @abstractmethod
    def _get_query_embedding(self, query: str) -> Embedding:

    @abstractmethod
    async def _aget_query_embedding(self, query: str) -> Embedding:

    @abstractmethod
    def _get_text_embedding(self, text: str) -> Embedding:

    @abstractmethod
    async def _aget_text_embedding(self, text: str) -> Embedding:
    ...
```

这些抽象方法需要在具体模型实现时填补，作用都是借助不同模型的 API 生成向量。比如 OpenAI 的具体实现类：

```python
# 模块: llama-index-embedding-openai
class OpenAIEmbedding(BaseEmbedding):
    ...
    def _get_text_embedding(self, text: str) -> List[float]:
        """Get text embedding."""
        client = self._get_client()
        return get_embedding(
            client,
            text,
            engine=self._text_engine,
            **self.additional_kwargs,
        )
    ...
```

其中 `get_embedding` 的实现逻辑是：

```python
...
def get_embedding(client: OpenAI, text: str, engine: str, **kwargs: Any) -> List[float]:
    text = text.replace("\n", " ")
    return (
        client.embeddings.create(input=[text],
                                 model=engine, **kwargs).data[0].embedding
    )
```

简单说就是用 OpenAI 官方 SDK 创建客户端，再调用 `create` 方法拿到向量。

### 4.3.3 单独调用嵌入模型

嵌入模型组件也可以脱离 RAG 单独使用，下面以几种典型模型为例演示。

#### 1. OpenAI 嵌入模型

LlamaIndex 默认使用 OpenAI 的 `text-embedding-ada-002`，无需额外配置即可直接用：

```python
from llama_index.embeddings.openai import OpenAIEmbedding
from llama_index.core import Settings

embed_model = OpenAIEmbedding()
embeddings = embed_model.get_text_embedding(
    "中国的首都是北京"
)
print(embeddings)
```

运行后得到一段浮点数向量数组，部分展示如下：

```bash
> python embed_simple.py
[0.007398069836199284, -0.011682811193168163,
-0.021248308941721916, -0.00428474135696888……
```

还可以调用接口比较两个向量的相似度：

```python
embeddings1 = embed_model.get_text_embedding(
    "中国的首都是北京"
)
embedding2 = embed_model.get_text_embedding(
    "中国的首都是哪里？"
)
embedding3 = embed_model.get_text_embedding(
    "苹果是一种好吃的水果"
)
```

```python
print(embed_model.similarity(embeddings1, embedding2))
print(embed_model.similarity(embeddings1, embedding3))
```

运行后能明显看出 embedding1 与 embedding2 的相似度更高：

```bash
> python embed_simple.py
0.9324159699236407
0.7942800233749084
```

#### 2. Ollama 的本地嵌入模型

如果想用 Ollama 本地模型，先启动本地服务，再把代码里相关部分替换即可：

```python
from llama_index.embeddings.ollama import OllamaEmbedding
embed_model = OllamaEmbedding(model_name="milkey/dmeta-embedding-zh:f16")
```

#### 3. TEI 的本地嵌入模型

借助 Text Embeddings Inference 工具，可以部署 Hugging Face 上的成熟嵌入模型，例如中文表现优秀的 `bge-large-zh`。

先启动服务：

```bash
> model=BAAI/bge-large-zh-v1.5
> text-embeddings-router --model-id $model --port 8080
```

然后就可以用这个本地 TEI 服务完成文本嵌入：

```python
from llama_index.embeddings.text_embeddings_inference import TextEmbeddingsInference
embed_model = TextEmbeddingsInference(
    model_name="BAAI/bge-large-zh-v1.5",
    timeout=60,  # timeout in seconds
    embed_batch_size=10, # batch size for embedding
)
```

在 LlamaIndex 的使用场景里，替换或切换嵌入模型的操作非常省心。

### 4.3.4 嵌入模型组件的集成使用

嵌入模型组件的用法和大模型组件很像，常常会被嵌套到其它模块中工作，比如在构建知识索引时直接完成自动向量化。

1. 更改默认的嵌入模型

统一修改默认嵌入模型最直接的方式是通过 Settings 组件里的 `embed_model` 属性来指定：

```python
Settings.embed_model = OllamaEmbedding(model_name="milkey/dmeta-embedding-zh:f16")
```

2. 将嵌入模型组件插入其他模块中

也可以在实例化具体模块时把嵌入模型直接传入，例如创建向量存储索引对象时就带上模型：

```python
......
embed_model=OllamaEmbedding(model_name="milkey/dmeta-embedding-zh:f16")
index = VectorStoreIndex(nodes, embed_model=embed_model)
......
```

### 4.3.5 了解与设置嵌入模型的参数

嵌入模型同样拥有可配置项，常见参数包括：

1. `model_name`：要调用的模型名称。
2. `embed_batch_size`：一次送入模型处理的批量窗口大小。
3. `timeout`：单次处理超时时长。
4. `max_retries`：最大重试次数。
5. `dimensions`：生成的向量维度，例如 2048。

不同模型支持的参数集合并不相同，这由模型自身决定，而非 LlamaIndex 框架。使用前最好查阅对应模型的官方说明，或查看框架中该模型组件的初始化代码。

创建嵌入模型组件时可以按需传入参数，例如：

```python
_MODEL_KWARGS={
    "model_name": "milkey/dmeta-embedding-zh:f16",
    "embed_batch_size": 50
}
embed_model = OllamaEmbedding(**_MODEL_KWARGS)
```

### 4.3.6 自定义嵌入模型组件

目前市面上公开的商业和开源嵌入模型已上百种，并非全部都已在 LlamaIndex 中完成封装。企业内部也可能存在自研的嵌入模型。若想在 LlamaIndex 中使用，就需要自行实现一个嵌入组件。做法是继承 `BaseEmbedding` 并实现必要接口，具体逻辑需参照模型调用文档。下面是一个模拟示例：

```python
from llama_index.core.embeddings import BaseEmbedding

# 导入自己的嵌入模型提供的模块，实现 embed 方法
from ... import MyModel

class MyEmbedding(BaseEmbedding):
    def __init__(
        self,
        model_name: str = 'MyEmbeddingModel',
        **kwargs: Any,
    ) -> None:
        # 构造一个模型调用对象（模拟）
        self._model = MyModel(model_name)
        super().__init__(**kwargs)

    # 生成向量（模拟）
    def _get_text_embedding(self, text: str) -> List[float]:
        embedding = self._model.embed(text)
        return embedding

    # 批量生成向量（模拟）
    def _get_text_embeddings(self, texts: List[str]) -> List[List[float]]:
        embeddings = self._model.embed(
            [text for text in texts]
        )
        return embeddings

    # ......实现其他必需的接口......
```

完成自定义后，该组件的后续使用方式与内置组件一致。

## 第 5 章 数据加载与分割

搭建 RAG 应用的第一步是准备能够用来增强生成的内容。具体来说，就是把各种类型的知识来源接入进来读取并做预处理，为后续的存储和索引做铺垫。典型的来源有：

- （1）本地电脑上的文档，包括 TXT、PDF、Office 文件、邮件、图片等。
- （2）存放在各类数据库中的结构化数据。
- （3）可通过互联网 URL 直接访问的网页或资源。
- （4）在线云存储中的文档。
- （5）可通过公开接口获取的网络数据，如社交媒体的公开信息。

本章将演示如何借助 LlamaIndex 的组件对这些来源进行加载与切分。

## 5.1 理解两个概念：Document 与 Node

### 5.1.1 什么是 Document 与 Node

处理多源数据前，需要先掌握 LlamaIndex 里两个基础的数据抽象：Document 与 Node。

（1）文档类型 Document。一个 Document 对象可以看作一个通用数据容器［注意，这里说的文档不等于磁盘上的物理文件 File］，如图 5-1 所示。它能容纳来自文档、数据库或企业系统的各类数据。基础的 Document 主要用于保存文本内容及其元数据。可以手工创建，也可由数据连接器自动从数据源读取生成。

![](img/7eddb68cd925518fdc310e52e94ba9a9_111_0.png)

图 5-1

（2）节点类型 Node。直观上，Node 可以理解为 Document 切分后得到的一个“块”Chunk，可能是文本块或图像块。Node 包含元数据以及与相关 Node 的关系信息。可以单独创建 Node，也可把 Document 切分成多个 Node，且切分后的 Node 会继承原 Document 的元数据。

Document 与 Node 看起来很像。实际上在 LlamaIndex 的实现里两者同源。Node 体系来源于 BaseNode：

```python
class BaseNode(BaseComponent):
    """Base node Object.
    Generic abstract interface for retrievable nodes
    """
```

Document 的定义为：

```python
class TextNode(BaseNode):
    ...
class Document(TextNode):
    ...
```

BaseNode 定义了所有 Node 共有的属性与接口，部分抽象接口由更具体的子类实现。Node 共分为 4 类。

- (1) TEXT：文本 Node，类型为 TextNode。基础容器，用于存放文本和元数据。
- (2) IMAGE：图片 Node，类型为 ImageNode，继承 TextNode，增加 base64 图像内容、图片 URL 等字段。
- (3) DOCUMENT：文档，类型为 Document，继承自 TextNode。这就是前面提到的文档类型，本质上也是一个 Node，只是用于表示从数据源加载的文档。
- (4) INDEX：索引 Node，类型为 IndexNode，继承自 TextNode，增加对其它任意对象的引用，常用于指向后续的 Index、Retriever，实现递归检索等功能。

因此 Document 与 Node 都是存储数据及元数据的容器。Document 多用于表示从数据源读取的原始内容；Node 多用于表示从 Document 切分出来的数据块。

### 5.1.2 深入理解 Document 与 Node

下面用一个自定义的 Document 对象来直观展示其内部结构：

```python
from llama_index.core.schema import Document
import pprint
doc = Document(text='RAG 是一种常见的大模型应用范式，它通过检索—排序—生成的方式生成文本。',metadata={'title':'RAG 模型介绍','author':'llama-index'})
pprint.pprint(doc.dict())
```

通过 `doc.dict` 方法把内部数据转为字典并打印：

```python
{'class_name': 'Document',
 'embedding': None,
 'end_char_idx': None,
 'excluded_embed_metadata_keys': [],
 'excluded_llm_metadata_keys': [],
 'id_': 'dbe95286-c380-4fb4-b77a-8ca0b85735df',
 'metadata': {'author': 'llama-index', 'title': 'RAG 模型介绍'},
 'metadata_seperator': '\n',
 'metadata_template': '{key}: {value}',
 'relationships': {},
 'start_char_idx': None,
 'text': 'RAG 是一种常见的大模型应用范式，它通过检索—排序—生成的方式生成文本。',
 'text_template': '{metadata_str}\n\n{content}'}
```

如果把这里的 Document 换成 TextNode 打印，结果会完全一样，因为 Document 本质上就是一种 TextNode。

Document 内部的关键属性有：

- （1）text：Node 最核心的属性，保存文本内容。
- （2）metadata：元数据，用于描述数据本身。
- （3）id_：文档唯一标识，可自行指定也可自动生成。
- （4）relationships：记录关联的文档或 Node，如前后或父子关系。
- （5）embedding：嵌入模型生成的向量，类型为 List[float]，后续用于构建向量索引。
- （6）start_char_idx/end_char_idx：标记数据在原始文档中的起止字符位置。

### 5.1.3 深入理解 Node 对象的元数据

Document 或 Node 都保存着重要的 `metadata` 属性。元数据即“描述数据的数据”，用来记录原始文档、创建时间、表名、创建者等辅助信息。例如前面代码中

```python
{'x_title':'RAG 模型介绍','author':'llama-index'}
```

就是为 Document 赋予的元数据。元数据的特点如下：

- (1) 类型为 dict，由多个 key/value 对构成。
- (2) 可填入任意信息，通常建议 value 为字符串或数值。
- (3) 可手动设置，部分字段会由框架自动生成。
- (4) 由 Document 生成的 Node 会自动继承源 Document 的元数据。
- (5) 在生成向量或调用大模型时，元数据会与文本一起输入，帮助模型更好地控制输出。

在后续流程中，元数据与文本会一并送入嵌入模型或大模型。那如何组合呢？Document 与 Node 提供了 `get_content` 接口来完成组合。字典形式的元数据如何转成字符串？这可通过以下属性配置：

- (1) metadata_seperator：连接多个 key/value 对的分隔符，默认 “\n”。
- (2) metadata_template：单个 key/value 的格式，默认 “{key}:{value}”，可改为 “{key}->{value}”。
- (3) text_template：元数据字符串与文本内容的组合方式，默认 `{metadata_str}\n\n{content}`。
- (4) excluded_embed_metadata_keys：在送入嵌入模型时需要排除的元数据 key。
- (5) excluded_llm_metadata_keys：在送入大模型时需要排除的元数据 key。

下面构造一个带有多项元数据参数的 Document 来体会其作用：

```python
......
doc4 = Document(
    text="百度是一家中国的搜索引擎公司。",
    metadata={
        "file_name": "test.txt",
        "category": "technology",
        "author": "random person",
    },
    excluded_llm_metadata_keys=["file_name"],
    excluded_embed_metadata_keys=["file_name", 'author'],
    metadata_seperator=" | ",
    metadata_template="{key}=>{value}",
    text_template="Metadata: {metadata_str}\n----\nContent: {content}",
)

print("\n全部元数据: \n",
    doc4.get_content(metadata_mode=MetadataMode.ALL))
print("\n嵌入模型看到的 \n",
    doc4.get_content(metadata_mode=MetadataMode.EMBED))
print("\n大模型看到的: \n",
    doc4.get_content(metadata_mode=MetadataMode.LLM))
print("\n没有元数据: \n",
    doc4.get_content(metadata_mode=MetadataMode.NONE))
```

`get_content` 会依据传入的输出模式，结合上述参数把文本与元数据组合后返回。共有 4 种元数据输出模式：

```python
class MetadataMode(str, Enum):
    ALL = "all"        #输出全部元数据
    EMBED = "embed"    #输出嵌入模型看到的元数据
    LLM = "llm"        #输出大模型看到的元数据
    NONE = "none"      #不需要输出元数据
```

观察这 4 种模式的输出可以清楚看到，在大模型和嵌入模型的模式下，会自动剔除 `excluded_llm_metadata_keys` 与 `excluded_embed_metadata_keys` 中指定的字段内容：

```text
全部元数据:
Metadata: file_name=>test.txt | category=>technology |
author=>random person
-----
Content: 百度是一家中国的搜索引擎公司。

嵌入模型看到的
Metadata: category=>technology
-----
Content: 百度是一家中国的搜索引擎公司。

大模型看到的:
Metadata: category=>technology | author=>random person
-----
Content: 百度是一家中国的搜索引擎公司。

没有元数据:
百度是一家中国的搜索引擎公司。
```

## 5.1.4 创建 Document 对象

在 LlamaIndex 里 Document 可以直接捏出来，也可以交由数据连接器从外部拉取，二者都能得到可用的 Document 对象。

### 1. 手动创建 Document

最简单的办法就是把一段文本文字直接作为参数传入，就能得到一个最基础的 Document。把多段文字分别包装，通常会得到一个 Document 列表，这套做法在做单元测试或临时验证时特别顺手：

```python
from llama_index.core.schema import Document, TextNode, MetadataMode
texts = ["This is a test", "This is another test", "This is a third test"]
docs = [Document(text=text) for text in texts]
```

### 2. 借助数据连接器加载

工程实战中更常见的是用数据连接器从各种数据源把内容读进来再封装成 Document。比如用目录读取器扫一个文件夹，把里面的文档批量读成 Document 数组，并输出最终的数量：

```python
from llama_index.core import SimpleDirectoryReader

#加载一个 PDF 文档
docs2 = \
    SimpleDirectoryReader(input_files=["../../data/Llama2PaperDataset/source_files/llama2.pdf"]).load_data()
print("The number of documents in docs2 is: ", len(docs2))

#加载这个目录下的所有文档（共 48 个 TXT 文档）
docs3 = \
    SimpleDirectoryReader("../../data/MiniTruthfulQADataset/source_files").load_data()
print("The number of documents in docs3 is: ", len(docs3))
```

打印出最后生成的对象数量：

```text
The number of documents in docs2 is: 77
The number of documents in docs3 is: 48
```

这里能看出 Document 并不是和文件一一对应的。读一个 PDF，默认会拆成 77 个 Document，实际上对应的是页数；而整目录批量读取时，Document 的数量和文件数量基本保持一致。

数据连接器的具体用法会在 5.2 节展开。

## 5.1.5 创建 Node 对象

Node 一般代表 Document 里的一个片段，可能是文本型的 TextNode，也可能是图片型的 ImageNode。和 Document 一样，Node 也有直接构造和由 Document 派生两种做法。

### 1. 手动创建 Node

直接造 Node 和直接造 Document 写法几乎一致，因为 Document 本身就是继承自 TextNode 的一种：

```python
texts = ["This is a chunk1","This is a chunk2"]
nodes = [TextNode(text=text) for text in texts]
pprint.pprint(nodes[0])
```

从打印信息可以看出，TextNode 与 Document 其实是同源的类型：

```text
[TextNode(id_='c5ddd431-ef5b-4126-b461-43c65beb7d67',
embedding=None, metadata={}, excluded_embed_metadata_keys=[],
excluded_llm_metadata_keys=[], relationships={}, text='This is a
chunk1', start_char_idx=None, end_char_idx=None,
text_template='{metadata_str}\n\n{content}',
metadata_template='{key}: {value}', metadata_seperator='\n')]
```

### 2. 由 Document 派生 Node

实际开发里绝大多数 Node 都是通过数据分割器从 Document 切分出来的，分割器负责解析内容并拆块，细节将在 5.3 节介绍。下面演示先构造一个 Document，再用基于分隔符的分割器把它切成若干 Node：

```python
......
docs = [Document(text='AIGC 是一种利用人工智能技术自动生成内容的方法，这些
内容可以包括文本、音频、图像、视频、代码等多种形式。\n AIGC 的发展得益于深度学习技术的进步，特别是自然语言处理领域的成就，使得计算机能够更好地理解语言并实现自动化内容生成。')]

#构造一个简单的数据分割器
parser = TokenTextSplitter(chunk_size=100,
chunk_overlap=0,separator="\n")
nodes = parser.get_nodes_from_documents(docs)

for i, node in enumerate(nodes):
    print(f"Node {i}: {node.text}")
```

输出信息如下：

```text
Node 0: AIGC 是一种利用人工智能技术自动生成内容的方法，这些内容可以包括文本、音频、图像、视频、代码等多种形式。
Node 1: AIGC 的发展得益于深度学习技术的进步，特别是自然语言处理领域的成就，使得计算机能够更好地理解语言并实现自动化内容生成。
```

因为设置了用换行符 "\n" 来切分，所以这份 Document 被分成了两块 Node。真实场景中文档形态差异很大，于是才会有多种分割器与参数可选。

### 3. 理解 Node 之间的关联

Node 通常源自 Document 的拆分，所以拆出来的 Node 之间以及和源 Document 之间会形成天然的父子、兄弟关联。框架里共定义了 5 种基础关系：

- （1）SOURCE：指向源头 Node，比如原始的 Document。
- （2）PREVIOUS：指向同一 Document 拆分出的前一个 Node。
- （3）NEXT：指向同一 Document 拆分出的后一个 Node。
- （4）PARENT：指向当前 Node 的父级 Node。
- （5）CHILD：指向当前 Node 的子级 Node。

所有关联都保存在 relationships 属性里，它是一个字典，值是对应关系的 RelatedNodeInfo。下面是一个手工指定关系的例子：

```python
nodes[0].relationships[NodeRelationship.NEXT] =
```把第一个 Node 的下一个 Node 对象 (NEXT 关系类型) 设置成第二个 Node 对象: 在 RelatedNodeInfo 对象中保存第二个 Node 对象的 node_id。

多数情况下框架会帮你自动建立这些关系。比如用 Document 生成 Node 时，关联信息会自动补齐。打印刚才生成的两个 Node 的 relationships：

```python
Node 0 relationships:
{<NodeRelationship.SOURCE: '1'>:
RelatedNodeInfo(node_id='a66cbaa8-c6b1-4dbb-8a80-0e13534b9fe5',
node_type=<ObjectType.DOCUMENT: '4'>, metadata={},
hash='c26cd260a4ec35b565b1755e4a4ecae975a962c5cd8bf8b1ddc5c66dc40
04030'), <NodeRelationship.NEXT: '3'>:
RelatedNodeInfo(node_id='cd0aff6b-139b-4c69-8d25-4c5c0ab0356e',
node_type=<ObjectType.TEXT: '1'>, metadata={},
hash='c0f4a11d100ca248a094d055f700522350b0f7ba2b41c690a87be1d5ee4
adea0')} }

Node 1 relationships:
{<NodeRelationship.SOURCE: '1'>:
RelatedNodeInfo(node_id='a66cbaa8-c6b1-4dbb-8a80-0e13534b9fe5',
node_type=<ObjectType.DOCUMENT: '4'>, metadata={},
hash='c26cd260a4ec35b565b1755e4a4ecae975a962c5cd8bf8b1ddc5c66dc40
04030'), <NodeRelationship.PREVIOUS: '2'>:
RelatedNodeInfo(node_id='72ea0260-b3d4-41cd-867e-e3410e4e20c4',
node_type=<ObjectType.TEXT: '1'>, metadata={},
hash='346fc26780860f0efbee0605bfe886ba2170c7f3acf09aa55c60c3edaa3
3602a')} }
```

能看到，第一个 Node 自动带上了 SOURCE 类型的源 Document，以及 NEXT 类型的后继 Node，没有 PREVIOUS。第二个 Node 则保存了 PREVIOUS 指向前一个 Node，而没有 NEXT。这种自动关联在后续检索与重建文档时非常关键。

## 5.1.6 元数据的设定与抽取

Document 和 Node 的元数据既可以自己写死，也可以让框架自动补全，还能借助元数据抽取器做更智能的增强。

### 1. 手动设定与自动生成

下面这段代码演示了自定义元数据、系统自动生成的元数据，以及 Node 如何继承 Document 的元数据：

```python
#手工设置元数据
doc1 = Document(text="百度是一家中国的搜索引擎公司。",
metadata={"file_name": "test.txt","category": "technology","author": "random person",})
print(doc1.metadata)

#自动生成 Document 对象的元数据
doc2 = SimpleDirectoryReader(input_files=["../..//data/yiyan.txt"]).load_data()
print(doc2[0].metadata)

#元数据自动继承到 Node 对象
parser = TokenTextSplitter(chunk_size=100, chunk_overlap=0,separator="\n")
nodes = parser.get_nodes_from_documents(doc2)
print(nodes[0].metadata)
```

输出的结果如下：

```python
{'file_name': 'test.txt', 'category': 'technology', 'author': 'random person'}
{'file_path': '../..//data/yiyan.txt', 'file_name': 'yiyan.txt',
'file_type': 'text/plain', 'file_size': 1699, 'creation_date':
'2024-04-08', 'last_modified_date': '2024-04-07'}

{'file_path': '../../data/yiyan.txt', 'file_name': 'yiyan.txt',
'file_type': 'text/plain', 'file_size': 1699, 'creation_date':
'2024-04-08', 'last_modified_date': '2024-04-07'}
```

可以看到，系统会自动补上路径、文件名、类型、大小、时间等基础信息，而由 Document 派生的 Node 会自动继承这些元数据。

### 2. 用元数据抽取器增强

LlamaIndex 提供了 MetadataExtractor 组件，能够借助大模型或自定义算法，从内容里抽取更复杂的语义信息作为元数据，比如摘要、概括标题、实体、文档可回答的假设问题等。这些信息在嵌入或喂给大模型时可以一并携带，用来丰富语义、提升检索与生成的质量。

几个典型抽取器举例。

1）摘要抽取器：SummaryExtractor

它负责从 Node 文本里生成摘要。示例代码如下：

```python
from llama_index.core.extractors import SummaryExtractor
llm = Ollama(model='qwen:14b')

# 自动生成 Document 对象的元数据
docs = SimpleDirectoryReader(input_files=["../../data/yiyan.txt"]).load_data()
summary_extractor = SummaryExtractor(llm=llm,
show_progress=False,
prompt_template="请生成以下内容的中文摘要: {context_str}",
metadata_mode=MetadataMode.NONE)
print(summary_extractor.extract(docs))
```

控制台输出如下：

```python
[{'section_summary': '摘要: 本文介绍了百度研发的人工智能大模型产品——文心一言。该模型具备理解、生成、逻辑和记忆四大基础能力，适用于工作、学习、生活中的各种场景，成为高效、便捷的助手和伙伴。'}]
```

常用参数说明：

- （1）llm：抽取摘要需要大模型，传入一个模型实例。
- （2）prompt_template：给大模型的提示模板，这里使用了中文提示。
- （3）metadata_mode：控制输入时是否带上已有元数据，这里设置不携带。

2）问答抽取器：QuestionsAnsweredExtractor

该抽取器会为给定的 Node 生成一组它能回答的假设性问题列表。用法与 SummaryExtractor 类似，只需把抽取器换成 QuestionsAnsweredExtractor 即可。

```python
......
questions_extractor = QuestionsAnsweredExtractor(llm=llm, show_progress=False, metadata_mode=MetadataMode.NONE)
print(questions_extractor.extract(docs))
```

此刻的输出被替换为：

```python
[{'questions_this_excerpt_can_answer': '1. 文心一言的最新版本升级到了什么？这对我使用它的性能有何影响？\n\n2. 文心一言如何处理复杂的逻辑难题和数学计算,它能提供哪些步骤或策略来帮助我解决这些问题? \n\n3. 对于需要长期记忆的任务,文心一言是如何保持信息的准确性和完整性的? \n\n4. 文心一言在生成文本时是否具有原创性? 如果有,它的创意来源是什么? \n\n5. 我可以在哪些平台上找到并使用文心一言? 它是否支持多种语言? ' }]
```

可以看出，问答提取器通过 LLM 为整份文档自动生成了若干可被追问的问题。

3) 标题提取器: TitleExtractor

该提取器负责为传入的 Node 产生概括性标题：

```python
title_extractor = TitleExtractor(llm=llm,show_progress=False,metadata_mode=MetadataMode.NONE)
print(title_extractor.extract(docs))
```

示例结果表明，标题提取器调用 LLM 从文档内容中提炼出标题：

```python
[{'document_title': '"深入解析: 百度文心一言——人工智能对话助手的革新功能与广泛应用研究"\n'}]
```

进一步验证：如果把同一份 Document 切分得到的多个 Node 同时喂给它，结果会怎样？

```python
parser = TokenTextSplitter(chunk_size=300,
chunk_overlap=0,separator="\n")
nodes = parser.get_nodes_from_documents(docs)
for node in nodes:
    print(node.ref_doc_id)

title_extractor = TitleExtractor(llm=llm,metadata_mode=MetadataMode.NONE)
print("\nTitle extracted:", title_extractor.extract(nodes))
```

做法是先把一份 Document 切分为若干 Node，再丢给标题提取器处理。投递前打印每个 Node 关联的 Document 标识 ref_doc_id，输出如下：

```python
cc919439-8a2e-4770-8e9d-f2258ce3e5dd
cc919439-8a2e-4770-8e9d-f2258ce3e5dd
cc919439-8a2e-4770-8e9d-f2258ce3e5dd

Title extracted:
[{'document_title': '"百度文心一言：人工智能语言助手的深度解析与功能展示"\n'}, {'document_title': '"百度文心一言：人工智能语言助手的深度解析与功能展示"\n'}, {'document_title': '"百度文心一言：人工智能语言助手的深度解析与功能展示"\n'}]
```

从打印信息能总结出两点。

(1) 所有 ref_doc_id 完全一样，说明这些 Node 都来源于同一份 Document。
(2) 不同 Node 得到的标题完全一致，说明该提取器的输出是文档级别的，同一 ref_doc_id 下的各 Node 标题保持统一。

通过元数据提取器获取的元数据可以手工写入对应 Node 的元数据区，例如：

```python
titles = extractor.extract(nodes)
for idx, node in enumerate(nodes):
    node.metadata.update(titles[idx])
```

## 5.1.7 IndexNode 类型概览

IndexNode 是在 TextNode 基础上扩展出来的一种节点类型。除了拥有 TextNode 的基础属性外，它还能携带指向其他节点或对象的引用。引用实现有两种方式。

(1) index_id 属性：保存目标对象的 id，例如 node id 来建立链接。
(2) obj 属性：直接保存目标对象的引用，例如检索器。

IndexNode 与 TextNode 的继承与扩展关系如图 5-2 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_126_0.png)

图 5-2

IndexNode 中通过 index_id 或 obj 指向的对象类型很宽泛，可能是其他 Node、Index、Retriever 或 QueryEngine。其主要价值在于检索到 IndexNode 时，能够依据内部指针进行递归操作，最典型的应用是递归检索，主要体现在：

- （1）由摘要节点定位到完整内容节点。
- （2）由一级节点定位到二级检索所需的检索器或查询引擎。

IndexNode 在递归检索中的具体用法将在进阶篇详细展开。

## 5.2 数据接入

数据连接器，也常称为数据读取器或数据加载器，负责从不同来源、协议和格式中读取数据，抽取出包含内容与元数据的 Document 实体，供后续嵌入、索引、检索等统一处理。

### 5.2.1 本地目录读取

在构建 Document 的过程中曾使用过 SimpleDirectoryReader，即简单目录读取器，用来从本地目录加载多种格式文件。LlamaIndex 生态还支持大量第三方数据连接器。

SimpleDirectoryReader 是把本地目录文件转为 Document 实体最便捷的方案。它会读取指定目录下全部或部分文件，根据扩展名自动判断类型并调用对应读取器完成加载。目前支持的类型包括：

- (1) .csv: 以逗号分隔字段的多条记录。
- (2) .docx: Microsoft Word 文档。
- (3) .ipynb: Jupyter 笔记本。
- (4) .jpeg/.jpg: JPEG 图像。
- (5) .md: Markdown 文档。
- (6) .mp3/.mp4: 音频与视频。
- (7) .pdf: PDF 文档。
- (8) .png: 便携式网络图形。
- (9) .ppt/.pptx: Microsoft PowerPoint 文档。

SimpleDirectoryReader 具备简单且强大的加载能力。我们有必要了解它的常用参数，以便更好使用，见表 5-1。

| 参数 | 类型 | 说明 |
| --- | --- | --- |
| input_dir | str | 待加载的目录路径 |
| input_files | List | 明确指定的文件清单，会覆盖 input_dir 与 exclude |
| exclude | List | 需排除的文件清单，支持通配符语法 |
| exclude_hidden | bool | 是否跳过隐藏文件 |
| encoding | str | 文本编码，默认 utf-8 |
| recursive | bool | 是否递归读取子目录，默认 False |
| filename_as_id | bool | 是否以源文件名作为 doc_id，默认 False |
| required_exts | Optional[List[str]] | 限定加载的扩展名白名单 |
| file_extractor | Optional[Dict[str, BaseReader]] | 按扩展名映射到对应 BaseReader 的字典 |
| num_files_limit | Optional[int] | 最大处理文件数 |
| file_metadata | Optional[Callable[str, Dict]] | 根据文件名生成元数据的处理函数 |
| raise_on_error | bool | 遇到读取错误时是否抛异常 |
| fs | Optional[AbstractFileSystem] | 文件系统对象，默认本地，可换为网络 FS |

下面深入了解 SimpleDirectoryReader 的使用方式。

### 1. 多格式文档快速导入

下面演示 SimpleDirectoryReader 对不同类型文件的加载效果，使用核心代码：

```python
......
#定义一个打印 Document 数组的方法，这个方法在后面经常使用
def print_docs(docs: list[Document]):
    print('Count of documents:', len(docs))
    for index, doc in enumerate(docs):
        print("-----")
        print(f"Document {index}")
        print(doc.get_content(metadata_mode=MetadataMode.ALL))
        print("-----")

#设置多种不同类型的原始文档
input_files = [
    "../../data/1-news.txt",
    "../../data/2-novels.docx",
    "../../data/3-taxquestions.csv",
    "../../data/4-taxquestions.pdf",
    "../../data/5-python.md",
    "../../data/6-chatdata.png",
]
```

reader = SimpleDirectoryReader(input_files=input_files)

print_docs(reader.load_data())

这里以几种常见本地文件作为 SimpleDirectoryReader 的输入，然后打印加载后生成的 Document 实体及其元数据。观察输出可得到以下结论。

- （1）默认只抽取输入文件中的文本内容，并写入 Document 的 text 属性。
- （2）源文件并不总是与 Document 实体一一对应。
- （3）PDF 默认按页拆分为多个 Document 实体。
- （4）Markdown 默认在标题处拆分为多个 Document 实体。
- （5）图片默认不会生成 Base64 编码，也不会做 OCR 文字识别。

## 2. 通过参数调控底层读取

SimpleDirectoryReader 大多数输入参数含义直观，但 file_extractor 需要特别说明。这是一个可插拔组件，用于控制底层读取行为。通过设置它，可以改变 SimpleDirectoryReader 的底层读取策略。

默认情况下，SimpleDirectoryReader 对图片不做额外智能处理，只生成基础元数据如文件路径、创建时间、修改时间等，这类信息对 RAG 场景价值有限。若后期想用多模态 LLM 生成图像向量并检索，可能需要先将图片转为 Base64 编码。又如图片中包含大量有效文字，加载时希望借助 OCR 提取，就需要指定专用读取器或自建读取器。

例如针对上述图片指定读取器，可以这样做：

```python
#从阅读器中导入 ImageReader 组件
from llama_index.readers.file import ImageReader
```

```python
#图片阅读器
image_reader = ImageReader(keep_image=True)
reader = SimpleDirectoryReader(input_files=[input_files[5]],
                        file_extractor={".png":image_reader})
print(reader.load_data()[0].image)
```

代码中挑选了一个 PNG 文件进行加载，并通过 file_extractor 指定 PNG 的读取器为 image_reader，且该读取器设置 keep_image=True。这表示要求在生成的 Document 实体中保留原始图片的完整 Base64 编码。

打印生成的 Document 实体中的图片属性，可见其成功保存了图 5-3 所示的完整 Base64 内容。

![](img/7eddb68cd925518fdc310e52e94ba9a9_130_0.png)

图 5-3

若希望读取器能够用 OCR 识别图片文字，可设置 parse_text = True，需先安装必要的 OCR 库后自行测试。

图片与多模态文档的加载与检索涉及较多复杂问题，将在进阶篇深入介绍。

## 3. 自建专用读取器

前面通过参数调整内置 ImageReader 改变了底层读取行为。那么遇到不支持的文件类型或需要特殊读取逻辑时该怎么办？

构造一个有趣的场景：假设存在 .psql 文件，里面存放 PostgreSQL 数据库的执行语句。现在需要设计一个读取器，在读取时自动执行文件中的 SQL 并把结果作为 Document 实体内容，应该如何处理？

需要自建一个继承自 BaseReader 的 PSQLReader 类，并实现 load_data 核心接口。核心代码如下：

```python
......
class PSQLReader(BaseReader):
    def __init__(self, *args: Any, **kwargs: Any,) -> None:
        super().__init__(*args, **kwargs)

    def load_data(self, file: Path, extra_info: Optional[Dict] = None) -> List[Document]:
        with open(file) as f:
            content = f.read()

        # 执行这个文档中的 SQL 语句，获得 "result"
        result = execute_sql_and_return_results(content)

        metadata = {'file_suffix': 'SQL'}
        if extra_info:
            metadata = {**metadata, **extra_info}

        # 将 "result" 作为 "text" 生成 Document 对象
        return [Document(text=result, metadata=metadata)]
```

这里需要实现一个简单的 execute_sql_and_return_results 辅助函数：

```python
import psycopg2
def execute_sql_and_return_results(sql: str) -> str:
    conn = psycopg2.connect(
        host="localhost",
        user="postgres",
        password="*******",
        database="postgres"
    )
    cur = conn.cursor()
    cur.execute(sql)
```

```python
results = []
for result in cur:
    results.append(str(result))

conn.close()
return "\n".join(results)
```

有了这个自定义读取器，就能加载 .psql 文件，并在加载时自动把 SQL 语句转换为执行结果：

```python
reader = SimpleDirectoryReader(
    input_files=['../../../../data/9-test.psql'],
    file_extractor={".psql": PSQLReader()}
)
documents = reader.load_data()
print_docs(documents)
```

运行代码看一下输出，注意到 Document 的 text 字段恰好就是 SQL 执行后得到的查询结果：

```text
Count of documents: 1
-----
Document 0
file_suffix: SQL
file_path: ../../data/9-test.psql
file_name: 9-test.psql
file_size: 25
creation_date: 2024-04-18
last_modified_date: 2024-04-18

(1, 'tom')
(2, 'george')
-----
```

## 4. 自定义生成元数据

继续聚焦 SimpleDirectoryReader 的另一个重要配置 file_metadata。这个参数接受一个函数，你可以借此为一批文档指定专属的元数据生成逻辑，而不是使用框架的默认规则。假如你用的是自研的读取器，也同样可以在 load_data 内部直接给 metadata 赋值，就不需要动这个参数。

下面给出一个最小可用的演示：

```python
#元数据生成函数
def gen_metadata(file):
    return {"catagory": "technology", "author": "random person",
"file_name": file}

reader = SimpleDirectoryReader(input_files=[input_files[0]],file_metadata=gen_metadata)
print_docs(reader.load_data())
```

gen_metadata 函数里重新定义了元数据的组织方式，所以最终得到的 Document 其元数据也会遵循这个新结构。打印内部的元数据可以看到变化：

```text
Count of documents: 1
-----
Document 0
catagory: technology
author: random person
file_name: ../../data/1-news.txt
```

## 5.2.2 从网络获取数据

除了本地目录，借助各类数据连接器从外部来源拉取数据也是常见需求。对企业级应用而言，数据源可能包括关系数据库、云存储、网站、SaaS 服务或第三方大数据平台。LlamaIndex 统一通过 LlamaHub 平台提供这些连接器，你可以查阅并直接接入，也能自行开发新连接器并贡献回 LlamaHub。

## 1. 从网页抓取内容和知识

把网页上的内容和知识抽取出来在实际项目中非常普遍，比如门户的产品页、内部知识库，或是公开网络上的共享资料。Python 生态的爬虫库让这件事并不困难，而 LlamaIndex 已经把常用的网页加载能力封装好了，无需重复造轮子。

下面介绍两个最常用的网页加载组件。

### 1）SimpleWebPageReader

该读取器用于快速抓取网页。传入 URL 列表后，可得到原始 HTML 或纯文本，行为由 html_to_text 参数控制：

```python
from llama_index.readers.web import SimpleWebPageReader
web_loader = SimpleWebPageReader(html_to_text=True)
docs = web_loader.load_data(urls=["https://cloud.bai**.com/doc/COMATE/s/rlvnio4a"])
print_docs(docs)  # 自定义的打印 docs 变量的方法
```

执行后，系统会依次访问指定 URL 并把内容转成文本。

### 2）BeautifulSoupWebReader

BeautifulSoup 是从 HTML 中抽取信息的经典库。它能把页面解析成 BeautifulSoup 对象，再用多种方式精细提取，这点比只能全量抓取的 SimpleWebPageReader 更灵活，更适合需要自定义提取逻辑的场景。

下面是一个使用 BeautifulSoupWebReader 的例子：

```python
from llama_index.readers.web import BeautifulSoupWebReader
```

```python
#定义一个个性化的网页内容提取方式
def _baidu_reader(soup: Any, url: str, include_url_in_text: bool =
True) ->
    Tuple[str, Dict[str, Any]]:
        main_content = soup.find(class_='main')
        if main_content:
            text = main_content.get_text()
        else:
            text = ''
        return text, {"title":
            soup.find(class_="post__title").get_text()}

web_loader = BeautifulSoupWebReader(website_extractor={"cloud.bai**.com": _baidu_reader})

docs = web_loader.load_data(urls=["https://cloud.baidu.com/doc/COMATE/s/rlvnio4a"])
print_docs(docs)
```

这里在加载器中传入了 website_extractor 参数，可以为不同站点配置不同的提取策略。例如，对 cloud.baidu.com 的页面使用自定义的 _baidu_reader。

阅读器拿到的是 BeautifulSoup 对象，便可按需定位元素。比如提取 class 为 "main" 的节点下的文本，用来去掉无关的导航、广告等噪音。

另外也可以返回自定义元数据，代码中就把 class 为 "post__title" 的元素内容作为元数据字段。最终打印可以看到标题已经被成功写入元数据。

```text
Count of documents: 1
------
Document 0
URL: https://cloud.bai**.com/doc/COMATE/s/rlvnio4a
title: 产品定价
文档中心智能代码助手公有云 COMATE 产品定价······
······
```

## 2. 直接读取数据库数据

在以数据为核心的企业应用里，大量知识都沉淀在关系型数据库中，如何把这些数据直接变成 Document 对象。下面先看 DatabaseReader 的典型用法：

```python
from llama_index.readers.database import DatabaseReader
from llama_index.core.schema import Document,TextNode,MetadataMode
db = DatabaseReader(
    scheme="postgresql",  # Database Scheme
    host="localhost",     # Database Host
    port="5432",          # Database Port
    user="postgres",      # Database User
    password="******",    # Database Password
    dbname="postgres",    # Database Name
)
docs = db.load_data(query="select * from questions")
print_docs(docs)
```

DatabaseReader 用法很简洁，先实例化再调用 load_data 即可。这里通过 SQL 查询 questions 表并把结果封装成 Document，输出如下：

```text
Count of documents: 2
-----
Document 0
id: 1, question: 文心一言是什么？, answer: 文心一言是百度公司的大模型产品，用于提供 AI 智能文本生成、对话与推理的能力., createtime: 2024-01-01
-----
-----
Document 1
id: 2, question: LlamaIndex 框架有什么用？, answer: LlamaIndex 可以用于开发基于大模型的应用程序，提供了大量可用的开发组件与工具，简化开发过程，提高开发效率, createtime: 2024-01-02
-----
```

再看一下其内部实现：

```python
from llama_index.core.utilities.sql_wrapper import SQLDatabase
from sqlalchemy import text
from sqlalchemy.engine import Engine

class DatabaseReader(BaseReader):
    # ......省略初始化代码......

    def load_data(self, query: str) -> List[Document]:
        """
        查询数据库，返回 Document 对象
        Args:
            query (str): SQL 语句
        Returns:
            List[Document]: Document 对象的列表
        """
        documents = []
        with self.sql_database.engine.connect() as connection:
            if query is None:
                raise ValueError("A query parameter is necessary to filter the data")
            else:
                #执行 SQL 语句，获得结果
                result = connection.execute(text(query))

                for item in result.fetchall():
                    doc_str = ", ".join(
                        [f"{col}: {entry}" for col, entry in zip(result.keys(), item)]
                    )
                    documents.append(Document(text=doc_str))
        return documents
```

组件内部基于 SQLAlchemy 构建数据库引擎，在 load_data 中连接数据库执行 SQL，然后把查询结果按固定格式组装成 Document 返回。

## 3. 自建网络数据加载器

如果你的数据放在阿里云 OSS 上，而框架里又没有现成的连接器，就能继承 BaseReader 自己实现一个 AliOSSReader，直接从云端读取并转为 Document，减少手工导入的负担。

示例实现如下：

```python
class AliOSSReader(BaseReader):
    def __init__(
        self,
        bucket_name: str,
        access_key_id: Optional[str] = None,
        access_key_secret: Optional[str] = None,
        endpoint: Optional[str] = None,
        *args: Any,
        **kwargs: Any,
    ) -> None:

        #参考 OSS SDK 的规范初始化 bucket 对象
        access_key_id = access_key_id or os.getenv('ACCESS_KEY_ID')
        access_key_secret = access_key_secret or os.getenv('ACCESS_KEY_SECRET')
        endpoint = endpoint or os.getenv('ENDPOINT')

        if access_key_id and access_key_secret and endpoint and bucket_name:
            auth = oss2.Auth(access_key_id, access_key_secret)
            self.bucket = oss2.Bucket(auth, endpoint, bucket_name)
        else:
            raise ValueError("Please provide access_key_id, access_key_secret, endpoint and bucket_name")

        #加载文档，这里支持文档通配符
        def load_data(self, object_names: List[str]) -> List[Document]:
            documents = []
            for object_pattern in object_names:
                for object_info in oss2.ObjectIterator(self.bucket):
                    if fnmatch.fnmatch(object_info.key, object_pattern):
                        content = self.bucket.get_object(object_info.key).read()
                        document = Document(text=content, metadata={"file_name": object_info.key})
                        documents.append(document)
            return documents
```

代码从 BaseReader 派生出 AliOSSReader，调用阿里云官方 OSS SDK 来读取文件，并支持批量传入及通配符。这里仅演示了文本格式的读取，若要处理 Excel、图片等复杂格式，可以先下载到本地再配合框架内的解析器处理，本文不再展开。

## 5.3 数据分割

连接器把来自不同来源的文档载入并整理成 Document 后，通常还需要进一步解析、切分成更细粒度的数据块，也就是 Node。这些 Node 会作为后续索引、检索等环节的基本单元。负责这一步的组件一般叫做分割器或解析器，最常见的是文本分割器 TextSplitter。

为什么不能直接用 Document 作为检索单元？

Document 块越大，承载的语义越完整，但检索时的召回精度会下降，就像把所有数据塞进数据库的一条记录里一样，会影响后续的生成质量。因此要把原始 Document 和分割后的 Node 在逻辑上分开，保持灵活性，并根据场景控制分割策略和粒度。

### 5.3.1 数据分割器的使用方式

分割器的使用和其它组件类似，既可单独调用，也能嵌入工作流自动触发。输入通常是 Document 列表，输出是 Node 列表，每个 Node 来自原始 Document 的一个子片段，并会自动继承父文档的元数据。

#### 1. 独立使用

构造一个分割器后即可对文档进行切分，例如：

```python
from llama_index.core.node_parser import SentenceSplitter
splitter = SentenceSplitter()
nodes = splitter.get_nodes_from_documents([Document(text="This is a test.\n haha!")])
```

代码里用到的 SentenceSplitter 算是 LlamaIndex 里最常见的数据切分器之一，直接实例化不传任何参数，就全走默认配置，这是最省事的上手方式。后面会再展开它的可调参数。先快速过一下 LlamaIndex 里数据分割器的继承关系，以 SentenceSplitter 为例，如图 5-4 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_140_0.png)

所有分割器最终都归到 NodeParser 这个基类之下。基类负责 get_nodes_from_documents 这个核心入口的实现，里面会处理切分后 Node 之间的关联构建、内部关系信息的生成以及元数据的继承。真正产生 Node 的切分逻辑则交给具体实现，比如文本相关的切分最终会落到内部的 _split_text 方法。

#### 2. 插入其他组件中被自动调用

造好的分割器也可以作为参数塞进别的组件里，由这些组件在运行期自动触发 Document 的解析与切分。举个例子，用 Document 直接建向量索引时，可以把分割器当作转换器传入，索引构建前会自动调用它把 Document 切成多个 Node：

```python
index = VectorStoreIndex.from_documents(
    documents,
    transformations=[SentenceSplitter(chunk_size=1024, chunk_overlap=20)],
)
```

转换器的详细说明放在 5.4 节。

## 5.3.2 常见的数据分割器

下面要聊几种常用的数据分割器怎么用，以及它们内部的大致工作思路。

### 1. 文本分割的底层方法

在 LlamaIndex 里对 Document 文本做切分，底层其实只有 4 种做法。无论选哪种开箱即用的分割器，本质上都是这 4 种方法的其中一种或组合。先认识这 4 种基础做法。

可以用下面这段代码把 4 种基础方法都跑一遍，看看效果：

```python
from llama_index.core.node_parser.text.utils import (
    split_by_char,
    split_by_regex,
    split_by_sentence_tokenizer,
    split_by_sep,
)

fn = split_by_sep("\n")
result = fn('Google 公司介绍 \n Google 是一家搜索引擎与云计算公司，总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算等。 ')
print(result)
print("Size of the result array:", len(result))

fn = split_by_sentence_tokenizer()
result = fn('Google 公司介绍 \n Google 是一家搜索引擎与云计算公司，总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算等。 ')
print(result)
print("Size of the result array:", len(result))

fn = split_by_regex("[^,.;。？！]+[,.;。？！]?")
result = fn('Google 公司介绍 \n Google 是一家搜索引擎与云计算公司，总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算等。 ')
print(result)
print("Size of the result array:", len(result))

fn = split_by_char()
result = fn('Google 公司介绍 \n Google 是一家搜索引擎与云计算公司，总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算等。 ')
print(result)
print("Size of the result array:", len(result))
```

输出结果如下：

```text
['Google 公司介绍 ', '\n Google 是一家搜索引擎与云计算公司，总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算等。 ']
Size of the result array: 2
['Google 公司介绍 \n Google 是一家搜索引擎与云计算公司，总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算等。 ']
Size of the result array: 1
['Google 公司介绍 \n Google 是一家搜索引擎与云计算公司，总部位于美国加利福尼亚州山景城。', '主要产品是搜索引擎、广告服务、企业服务、云计算等。 ']
Size of the result array: 2
['G', 'o', 'o', 'g', 'l', 'e', '公', '司', '介', '绍', ' ', '\n', ' ', 'G', 'o', 'o', 'g', 'l', 'e', '是', '一', '家', '搜', '索', '引', '擎', '与', '云', '计', '算', '公', '司', ' ', '总', '部', '位', '于', '美', '国', '加', '利', '福', '尼', '亚', '州', '山', '景', '城', ' ']
['主', '要', '产', '品', '是', '搜', '索', '引', '擎', '、', '广', '告', '服', '务', '、', '企', '业', '服', '务', '、', '云', '计', '算', '等', '。']
Size of the result array: 74
```

不同方法的产物差异很明显。

(1) 分隔符切分 split_by_sep：按指定的分隔符切。这里给了换行符“\n”，文本于是从“\n”处被切成两段。

(2) 句子切分 split_by_sentence_tokenizer：依赖 nltk 的自然语言处理能力，用无监督算法把长文本拆成句子列表，但需要提前准备好目标语言的训练包，目前对中文不生效，所以这里没有切分效果。

```python
#split_by_sentence_tokenizer 的实现
import nltk
tokenizer = nltk.tokenize.PunktSentenceTokenizer()
def split(text: str) -> List[str]:
    spans = list(tokenizer.span_tokenize(text))
    sentences = []
    for i, span in enumerate(spans):
        start = span[0]
        if i < len(spans) - 1:
            end = spans[i + 1][0]
        else:
            end = len(text)
        sentences.append(text[start:end])
    return sentences
```

(3) 正则切分 split_by_regex：通过正则把文本抽成若干句子。实现简单，对中文更友好。

(4) 字符切分 split_by_char：按字符硬切。是最粗暴的方式，很容易破坏语义，实战中基本不用。

光有切分逻辑还不够，把 Document 变成多个 Node 时，框架还会额外做几件事。

(1) 补全 Node 的其它属性，比如在原文档中的位置信息。
(2) 组织 Node 的元数据，既有从 Document 继承的，也有新抽取的。
（3）生成 Node 的内部关系，即 relationships 属性。

弄清这些基础切分方式，有助于更好掌握后面介绍的现成分割器。

### 2. 理解 chunk_size 与 chunk_overlap 参数

用不同分割器时都会碰到 chunk_size 和 chunk_overlap。这两个值不仅影响切分效果，对后续 RAG 效果的调优也很关键，先把它讲清楚。

字面上很好理解，一个控制切出的文本块即 Node 文本内容的上限，另一个控制相邻块的重叠长度。看框架源码会发现几个细节。

（1）这两个参数的度量单位是 token 数量，不是字符串长度。

这里的 token 指大模型进出用的 token。LlamaIndex 默认用 OpenAI 开源的 Python 分词库 tiktoken 来数 token，该库可针对不同模型加载对应的词表。下面是一段对比实验：

```python
import tiktoken
import re
enc = tiktoken.encoding_for_model("gpt-3.5-turbo")
print('length of tokens:',len(enc.encode('Google 公司是一家搜索引擎公司。')))
print('length of string:',len('Google 公司是一家搜索引擎公司。'))
```

代码比较了字符串长度和 token 数量，输出如下：

```text
length of tokens: 12
length of string: 18
```

通常情况下两者数值不一致，但不在数量级上差别。如果你要精细控制，可以自行替换 tiktoken。

（2）这两个参数是上限，不是精确值。

切出的块大小不一定正好等于设定值，只会小于等于。因为切分不是逐字符截断，chunk_size 也是按 token 算，所以没法保证绝对精确。

（3）默认的 chunk_size 已把元数据算进去。

实际能放进文本的内容会比参数值小一点，因为 Node 的元数据也会用 token 计入，且默认会和文本一起进索引和模型。

（4）切分器会尽量让块大小贴近设定值。

切完后会再跑合并策略 Merge，尝试把已切的块合并，让块大小尽可能接近 chunk_size，重叠也尽可能接近 chunk_overlap。有兴趣可以看看下面的 def_merge 算法：

```python
# 将分割好的文本块(splits)尝试合并成接近 chunk_size 参数值的块
def _merge(self, splits: List[str], chunk_size: int) -> List[str]:
    chunks: List[str] = []
    cur_chunk: List[str] = []
    cur_len = 0
    for split in splits:
        split_len = len(self._tokenizer(split))

        # 如果在添加新的分割后超过了块大小，
        # 那么需要结束当前的块，并构造一个新的块
        if cur_len + split_len > chunk_size:
            chunk = "".join(cur_chunk).strip()
            if chunk:
                chunks.append(chunk)

        # 构造一个新的块,但注意保留重叠部分
        # 一直弹出前一个块的第一个元素，直到:
        # 1. 当前块的大小小于块重叠部分的大小
        # 2. 总大小小于块大小
        while cur_len > self.chunk_overlap or cur_len + split_len > chunk_size:
```

## 3. 理解与使用 TokenTextSplitter

先介绍 TokenTextSplitter，它是最基础的、基于指定分隔符做文本切分的分割器。

```python
# 弹出第一个元素
first_chunk = cur_chunk.pop(0)
cur_len -= len(self._tokenizer(first_chunk))

cur_chunk.append(split)
cur_len += split_len

# 处理最后一个块
chunk = "".join(cur_chunk).strip()
if chunk:
    chunks.append(chunk)
return chunks
```

```python
......
docs = [Document(text="Google 公司介绍 \n Google 是一家搜索引擎与云计算公司 \n 总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算等。")]
splitter = TokenTextSplitter(
    chunk_size=50,
    chunk_overlap=0,
    separator="\n",
    backup_separators=["."]
)
nodes = splitter.get_nodes_from_documents(docs)
print_nodes(nodes)
```

它对 Document 文本的处理顺序是：

按优先级依次使用下面 3 种基础方法，直到每个块都小于 chunk_size 为止。

1.  基于 separator 参数的分隔符切分。
2.  基于 backup_separators 参数的备选分隔符切分。
3.  基于字符的切分。

所以除了 chunk_size、chunk_overlap，影响行为的主要还有 separator 和 backup_separators。上例的输出是：

```
Count of nodes: 2
-----
Node 0
Google 公司介绍
Google 是一家搜索引擎与云计算公司
-----
-----
Node 1
总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算
等。
-----
```

这里要求按换行符“\n”切，且每个块不超过 50，数值在实际场景里很少这么小，因此输出里前两个小块被合并，最终得到两个 Node。

## 4. 理解与使用 SentenceSplitter

SentenceSplitter 是更常用的段落加句子级别的分割器。下面例子展示它的用法和参数：

```python
#自定义一个分割文本的函数
def my_chunking_tokenizer_fn(text:str):
    #跟踪是否进入本方法
    print('start my chunk tokenizer function...')
    sentence_delimiters = re.compile(u'[.！? ]')
    sentences = sentence_delimiters.split(text)
    return [s.strip() for s in sentences if s]


docs = [Document(text="***Google 公司介绍***Google 是一家搜索引擎与云计算公司***总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算等。")]
nnode_parser = SentenceSplitter(chunk_size=50,
```

```python
python
chunk_overlap=0,
paragraph_separator="***",
chunking_tokenizer_fn=my_chunking_tokenizer_fn,
secondary_chunking_regex =
"[^,.;。?!]+[,.;。?!]?",
separator="\n")
nodes = node_parser1.get_nodes_from_documents(docs)
print_nodes(nodes)
```

它的切分流程是：

按优先级依次使用下面 5 种基础方法，直到每个块都小于 chunk_size 为止。

-   (1) 基于分隔符切段落，可用参数 paragraph_separator 指定，默认值是 “\n\n\n”。

SentenceSplitter 提供了五层递进的切分策略，可以按需组合使用。

- 基于句子切分，可通过参数 `chunking_tokenizer_fn` 指定，默认会调用 nltk 库来完成。
- 基于正则表达式切分，可通过参数 `secondary_chunking_regex` 指定，默认模式为 `[^,.;。?!]+[,.;。?!]?`。
- 基于分隔符切分句子，可通过参数 `separator` 指定，默认值是空格字符。
- 基于字符切分，日常使用中基本用不到。

现在深入拆解 SentenceSplitter 的执行逻辑。

在示例中为了看清不同参数的效果，每个参数都做了显式指定。代码先让它以字符串 “***” 为依据拆分段落，同时把 `chunk_size` 设为 50。因为文档里确实存在 “***”，第一次按段落符号切分就能把整段文本拆成多个不超过 50 的块。按照执行顺序推断，后续基于 `chunking_tokenizer_fn` 的句子切分就不会被触发。

观察实际输出，`my_chunking_tokenizer_fn` 内部的 print 并没有打印出来，和预期一致。

```python
Count of nodes: 2
```

```
Node 0
***Google 公司介绍***Google 是一家搜索引擎与云计算公司
------
------
Node 1
***总部位于美国加利福尼亚州山景城。主要产品是搜索引擎、广告服务、企业服务、云计算等。
------
```

如果把文本里的 “***” 全部换成句号，首轮按分隔符拆段落就无法把内容切成小于 50 的多个块。这时系统会进入第二步，调用自定义的 `my_chunking_tokenizer_fn` 来做句子级切分。最终结果如下：

```
start my chunk tokenizer function...
Count of nodes: 2
------
Node 0
Google 公司介绍 Google 是一家搜索引擎与云计算公司总部位于美国加利福尼亚州山景城
------
------
Node 1
主要产品是搜索引擎、广告服务、企业服务、云计算等
------
```

输出的第一行说明自定义的句子切分方法确实被执行了。

SentenceSplitter 是文本类数据分割器里非常常用的组件，围绕它的关键参数和运行原理做一次深入讲解，有助于后面处理各类格式文本时更得心应手。

## 5. 理解与使用 SentenceWindowNodeParser
SentenceWindowNodeParser 本质上是一个基于句子切分的文档分割器。不同之处在于，它在生成每个 Node 时，会把该句子的上下文窗口一并写入元数据，窗口宽度由 `window_size` 控制。

下面是一个典型用法：

```python
docs = [Document(text="Google 公司介绍:Google 是一家搜索引擎与云计算公司。 \n总部位于美国加利福尼亚州山景城。 \n主要产品是搜索引擎、广告服务、企业服务、云计算等。 \n百度是一家中国的搜索引擎公司。")]
splitter = SentenceWindowNodeParser(
    window_size=2,
    sentence_splitter = my_chunking_tokenizer_fn
)
nodes = splitter.get_nodes_from_documents(docs)
print_nodes(nodes)
```

示例里准备了一段包含 4 个句子的文本，用前面出现过的 `my_chunking_tokenizer_fn` 做句子切分，并把窗口大小设为 2，前两个 Node 的展示结果如下：

```
Count of nodes: 4
-----
Node 0
window: Google 公司介绍:Google 是一家搜索引擎与云计算公司 总部位于美国加利福尼亚州山景城 主要产品是搜索引擎、广告服务、企业服务、云计算等 百度是一家中国的搜索引擎公司
original_text: Google 公司介绍:Google 是一家搜索引擎与云计算公司

Google 公司介绍:Google 是一家搜索引擎与云计算公司
-----
Node 1
window: Google 公司介绍:Google 是一家搜索引擎与云计算公司 总部位于美国加利福尼亚州山景城 主要产品是搜索引擎、广告服务、企业服务、云计算等 百度是一家中国的搜索引擎公司
original_text: 总部位于美国加利福尼亚州山景城

总部位于美国加利福尼亚州山景城
-----
```

注意元数据里多了一个 `window` 字段，它保存了当前 Node 上下窗口内其他 Node 的文本。窗口范围由 `window_size` 决定，使用时有三点要清楚：

1. 与普通元数据不同，`window` 的内容默认对嵌入模型和大模型不可见。
2. 不建议把 `window` 的内容一起喂给嵌入模型。嵌入应只针对 Node 自身文本，这样语义更细，检索更准。
3. 建议在生成阶段把 Node 原文本替换为 `window` 包含的内容发送给大模型，以提供更充分的上下文，提升生成质量。

## 6. 理解与使用 HierarchicalNodeParser
SentenceWindowNodeParser 的价值在于在不损害检索精度的前提下，让 Node 携带更丰富的上下文。实现类似目标的另一种思路是 HierarchicalNodeParser。

它是一个多层级的 Node 解析器，会在多个粒度上对 Document 文本进行解析，生成不同大小且具备层级关系的 Node 集合，每个 Node 都能通过 `relationships` 字段找到对应的父子节点。

好处很明显：检索到细粒度 Node 后，可以自动追溯到其父节点，从而把更宽的上下文带入大模型生成。看一个例子：

```python
from llama_index.core.node_parser import HierarchicalNodeParser

docs = [Document(text="Google 公司介绍:Google 是一家搜索引擎与云计算公司。\n总部位于美国加利福尼亚州山景城。\nGoogle公司成立于1998年9月4日,由拉里·佩奇和谢尔盖·布林共同创立。\n主要产品是搜索引擎、广告服务、企业服务、云计算等。\n百度是一家中国的搜索引擎公司。\n百度公司成立于2000年1月1日，由李彦宏创立。")]

node_parser = HierarchicalNodeParser.from_defaults(
    chunk_sizes=[2048, 100, 50]
)
nodes = node_parser.get_nodes_from_documents(docs)
print_nodes(nodes)
```

代码里同时使用了 3 个不同的 `chunk_size` 作为分割器的输入。输出示意如图 5-5：

![img](img/7eddb68cd925518fdc310e52e94ba9a9_152_0.png)

可以看出，`chunk_size=2048` 的层级只产生 1 个 Node；`chunk_size=100` 的层级产生 2 个 Node；`chunk_size=50` 的层级产生 4 个 Node，合计 7 个 Node。若打印某个 Node 的 Relationships，会得到类似图 5-6 的信息：

![img](img/7eddb68cd925518fdc310e52e94ba9a9_152_1.png)

Relationships 中完整记录了该 Node 的关联节点，比如包含更多文本的父节点。生成时就能据此补全上下文。

## 7. 理解与使用 SemanticSplitterNodeParser
SemanticSplitterNodeParser 是一种基于语义和向量的分割器。它不依赖分隔符、正则或固定长度，而是借助嵌入模型衡量句子间的语义相似度，把相关句子合并到同一 Node，以最大化语义的连贯与独立性。

简单测试如下：

```python
......
docs = 
SimpleDirectoryReader(input_files=["../../data/yiyan.txt"]).load_data()
embed_model = 
OllamaEmbedding(model_name="milkey/dmeta-embedding-zh:f16")
splitter = SemanticSplitterNodeParser(
    breakpoint_percentile_threshold=85,
    sentence_splitter = my_chunking_tokenizer_fn,
    embed_model=embed_model
)
nodes = splitter.get_nodes_from_documents(docs)
print_nodes(nodes)
```

关键参数有三项：

1. `breakpoint_percentile_threshold`：控制语义相关性的阈值，可理解为句子向量距离的临界点。距离大于阈值就切分。阈值越大，块越少；阈值越小，块越多。
2. `sentence_splitter`：控制句子切分方式，默认使用 nltk。
3. `embed_model`：指定嵌入模型，用来计算向量距离，这里采用本地模型。

先把 `breakpoint_percentile_threshold` 设为 85，输出结果如下：

```
start my chunk tokenizer function...
Count of nodes: 3
-----
Node 0
```

```
file_path: ../../data/yiyan.txt
file_name: yiyan.txt
file_type: text/plain
......
```

再把阈值调到 20，输出变化如下：

```
start my chunk tokenizer function...
Count of nodes: 5
-----
Node 0
file_path: ../../data/yiyan.txt
file_name: yiyan.txt
file_type: text/plain
......
```

很明显，阈值下降后 Node 数量显著增加，因为可合并的语义相关句子变少了。在这种语义分割方式下，Node 数量不固定，也不需要 `chunk_size`，完全由语义相关性决定。缺点是依赖嵌入计算，大文档处理时速度会慢一些。

## 8. 分割特殊的文本
除了普通的 TXT 文本，实际项目中常遇到 Markdown、HTML、JSON、源代码等特殊平面文档。框架提供了现成的分割器来应对：

```python
......
#分割 Markdown 格式的文档
docs = FlatReader().load_data(Path("../../data/5-python.md"))
markdown_parser = MarkdownNodeParser()
nodes = markdown_parser.get_nodes_from_documents(docs)
```

```python
print_nodes(nodes)

#分割 HTML 格式的文档
docs = FlatReader().load_data(Path("../../data/10-google.html"))
html_parser = HTMLNodeParser()
nodes = html_parser.get_nodes_from_documents(docs )
print_nodes(nodes)

#分割 JSON 格式的文档
docs = FlatReader().load_data(Path("../../data/11-quantum.json"))
json_parser = JSONNodeParser()
nodes = json_parser.get_nodes_from_documents(docs )
print_nodes(nodes)

#分割源代码文档
docs = FlatReader().load_data(Path("../../data/8-test.py"))
code_parser = CodeSplitter(language="python")
nodes = code_parser.get_nodes_from_documents(docs )
print_nodes(nodes)
```

这些分割器基于预设规则把源文档转为 Node，比如 Markdown 分割器会自动识别标题 Header 并据此切块。更上层的 `SimpleFileNodeParser` 也能直接处理 Markdown、HTML、JSON 等格式。

## 5.4 数据摄取管道

### 5.4.1 什么是数据摄取管道
前面介绍了 RAG 流程里的两个核心环节：数据加载与数据分割。加载把外部数据读入内存的 Document 对象，分割把 Document 切成多个 Node，后续再生成向量并写入向量库。整个过程像一条流水线，数据在其中被持续处理与转换。LlamaIndex 用 IngestionPipeline 来更清晰地描述这条流水线（见图 5-7）。

![](img/7eddb68cd925518fdc310e52e94ba9a9_156_0.png)

图 5-7

摄取管道的核心思想是：通过串联多个数据转换器 Transformation，实现自动化、连续处理而无需人工干预。每个转换器遵循统一接口，接收输入并输出新的 Node 列表，列表再进入下一个转换器。中间结果会被缓存，减少重复计算。

先看一个简化示例，再展开原理：

```python
from llama_index.core import Document, SimpleDirectoryReader
from llama_index.core.node_parser import SentenceSplitter
from llama_index.core.extractors import TitleExtractor
from llama_index.embeddings.ollama import OllamaEmbedding
from llama_index.llms.ollama import Ollama
from llama_index.core.ingestion import IngestionPipeline, IngestionCache
import pprint

llm = Ollama(model='qwen:14b')
embedded_model = OllamaEmbedding(model_name="milkey/dmeta-embedding-zh:f16", embed_batch_size=50)
docs = SimpleDirectoryReader(input_files=["../../data/yiyan.txt"]).load_data()
```

```python
# 构造一个数据摄取管道
pipeline = IngestionPipeline(
    transformations=[
        SentenceSplitter(chunk_size=500, chunk_overlap=0),
        TitleExtractor(llm=llm, show_progress=False)
    ]
)

# 运行这个数据摄取管道
nodes = pipeline.run(documents=docs)
```

这个例子展示的是一条完整的数据摄取流水线，里面接进了两个转换器，正是前面讲过的文本分割器与元数据抽取器。于是这条流水线对传入的原始文档，比如经由读取 TXT 得到的 Document 对象，先用 SentenceSplitter 做文本切分，再用 TitleExtractor 抽取出标题，最终把结果整理成 Node 列表输出。

上文的代码逻辑，与下面这种自己手写的处理步骤本质上是等价的：

```python
......
# 文本分割，相当于第一个转换器
splitter = SentenceSplitter(chunk_size=500, chunk_overlap=0)
nodes = splitter.get_nodes_from_documents(docs)

# 抽取标题，并将其设置到 Node 对象的元数据中，相当于第二个转换器
extractor = TitleExtractor(llm=llm, show_progress=False)
titles = extractor.extract(nodes)
for idx, node in enumerate(nodes):
    node.metadata.update(titles[idx])
......
```

因此，数据摄取管道的本质，是对解析文档、抽取元数据、生成向量等各种数据处理组件的使用方式做的一层抽象，让整段处理代码更优雅、更简洁。

## 5.4.2 用于数据摄取管道的转换器

转换器是流水线里的执行单元。那么哪些组件可以被放进流水线呢？先看转换器的基类 TransformComponent：

```python
class TransformComponent(BaseComponent):
    """Base class for transform components."""

    class Config:
        arbitrary_types_allowed = True

    @abstractmethod
    def __call__(self, nodes: List["BaseNode"], **kwargs: Any) -> List["BaseNode"]:
        pass

    async def acall(self, nodes: List["BaseNode"], **kwargs: Any) -> List["BaseNode"]:
        return self.__call__(nodes, **kwargs)
```

核心就是 `__call__` 这个接口。每个转换器都必须实现它，输入是 Node 列表，输出也是 Node 列表。正因为输入输出类型一致，多个转换器才可以像链条一样串起来，按顺序完成整套处理。

目前符合这个接口标准的组件有如下几类。

1. **NodeParser**：数据分割器，包含文本分割器 TextSplitter、针对特殊文档的解析器如 CodeParser 等，负责把 Document 对象拆成 Node 列表。

2. **MetadataExtractor**：元数据抽取器，包含 TitleExtractor、SummaryExtractor 等，负责为已解析的 Node 列表补充元数据。

3. **Embedding Model**：嵌入模型，负责为 Node 列表中的文本内容生成向量。

再看 NodeParser 如何满足转换接口的要求：

```python
class NodeParser(TransformComponent, ABC):
    """Base interface for node parser."""
    #转换器接口的实现
    def __call__(self, nodes: List[BaseNode], **kwargs: Any) -> List[BaseNode]:
        return self.get_nodes_from_documents(nodes, **kwargs)
```

它直接调用 `get_nodes_from_documents` 方法完成 Document 到 Node 的转换。嵌入模型组件的实现方式如下：

```python
class BaseEmbedding(TransformComponent):
    """Base class for embeddings."""
    #转换器接口的实现
    def __call__(self, nodes: List[BaseNode], **kwargs: Any) -> List[BaseNode]:
        embeddings = self.get_text_embedding_batch(
            [node.get_content(metadata_mode=MetadataMode.EMBED) for node in nodes],
            **kwargs,
        )
        for node, embedding in zip(nodes, embeddings):
            node.embedding = embedding
        return nodes
```

该组件的 `__call__` 接收 Node 列表，内部调用 `get_text_embedding_batch` 生成内容向量，并把向量写入 Node 的 embedding 字段后再返回列表，也符合输入输出规范。

## 5.4.3 自定义转换器

转换器的实现门槛很低，所以完全可以按需自建。例如做一次简单的 Node 文本清洗：

```python
from llama_index.core.schema import TransformComponent
import re

#定义一个做数据清理的转换器
class TextCleaner(TransformComponent):
    def __call__(self, nodes, **kwargs):
        for node in nodes:
            node.text = re.sub(r"[^\u4e00-\u9fa5A-Za-z0-9,。？！“”‘’；：【】《》（）\[\]\'\.\,\?\!\:\;\(\)\n\r]", "", node.text)
        return nodes
```

创建实例后把它插入流水线，就能很优雅地完成清洗工作：

```python
......
pipeline = IngestionPipeline(
    transformations=[
        SentenceSplitter(chunk_size=500, chunk_overlap=0),
        TextCleaner(),  # 插入自定义转换器
        TitleExtractor(llm=llm, show_progress=False)
    ]
)
nodes = pipeline.run(documents=docs)
......
```

## 5.4.4 使用数据摄取管道

弄明白转换器的机制后，再来看流水线的具体使用方式。

### 1. 运行数据摄取管道

基本用法非常直接：构建好流水线然后执行，拿到结果即可：

```python
......构造数据摄取管道，设置转换器......
#运行数据摄取管道
nodes = pipeline.run(documents=docs)
```

### 2. 存储到向量库中

如果希望处理结束后把 Node 存入向量库，可以传入向量存储相关的参数，并提供一个嵌入模型作为转换器参与处理。

在前面例子上做一点改造：

```python
#构造一个向量存储对象用于存储最后输出的 Node 对象
chroma = chromadb.HttpClient(host="localhost", port=8000)
collection = chroma.get_or_create_collection(name="pipeline")
vector_store = ChromaVectorStore(chroma_collection=collection)

pipeline = IngestionPipeline(
    transformations=[
        SentenceSplitter(chunk_size=500, chunk_overlap=0),
        TitleExtractor(llm=llm, show_progress=False),
        embedded_model  #提供一个嵌入模型用于生成向量
    ],
    vector_store = vector_store  #提供一个向量存储对象，用于存储最后的 Node 对象
)
nodes = pipeline.run(documents=docs)
```

运行后流水线会输出带有向量的 Node 列表，同时把向量化后的 Node 写入向量库。可通过简单的语义查询来验证是否可用：

```python
#用输入问题做语义检索
results = vector_store.query(VectorStoreQuery(
    query_str='文心一言是什么？',
    similarity_top_k=3))
pprint.pprint(results.nodes)
```

若输出与图 5-8 类似，就说明向量存储可用，检索功能正常。

![](img/7eddb68cd925518fdc310e52e94ba9a9_161_0.png)

图 5-8

### 3. 并行处理

当原始知识文档数量很大，流水线里又包含多个转换器，处理时间可能会变长，尤其是涉及大模型或嵌入模型时。可以设置并行参数来提升效率，系统会启动多个进程分担 Node 的处理任务。例如：

```python
if __name__ == '__main__':
    freeze_support()
    start_time = time.time()
    nodes = pipeline.run(documents=docs, num_workers=2)
    elapsed_time = time.time() - start_time
    print(f'Elapsed time: {elapsed_time:.3f} s')
```

流水线对应的参数是 `num_workers`。通过记录运行时间可以对比不同并行度下的表现，选择最合适的数值。例如设置 `num_workers = 1` 时的输出如图 5-9：

![](img/7eddb68cd925518fdc310e52e94ba9a9_162_0.png)

图 5-9

设置 `num_workers = 2` 时的输出如图 5-10：

![](img/7eddb68cd925518fdc310e52e94ba9a9_162_1.png)

图 5-10

可见耗时明显下降。但 `num_workers` 并非越大越好，会受到 CPU、内存等硬件资源，以及被调用模型并发限制或流控的影响，需要实际测试后确定。

### 4. 文档存储与管理

大规模文档经流水线处理后，会遇到文档变更与同步的问题。若输入文档未变，重复处理会浪费性能与成本。可以通过流水线自带的文档存储功能来做重复排除。

构建流水线时传入一个文档存储对象即可。该对象可以是本地系统，也可以是网络存储。流程如下：

1. 每次运行结束后，流水线会把本次处理的文档信息保存到存储对象。
2. 下一次运行时，流水线会从存储对象读取历史记录。
3. 对比本次输入与历史记录，根据文档 hash 判断是否变化，未变化的文档将被跳过。

继续用之前的测试代码稍作修改：

```python
#第一次运行数据摄取管道
pipeline = IngestionPipeline(
    transformations=[
        SentenceSplitter(chunk_size=1000, chunk_overlap=0),
        embedded_model
    ],
    docstore=SimpleDocumentStore(),  #需要导入 SimpleDocumentStore 对象
)
docs = SimpleDirectoryReader(input_files=["../../data/sales_tips1.txt"]).load_data()
nodes = pipeline.run(documents=docs, show_progress=True)
print(f'{len(nodes)} nodes ingested into vector store')

pipeline.persist("./pipeline_storage")
```

图 5-11 为第一次运行的输出，处理了 9 个 Node。

![](img/7eddb68cd925518fdc310e52e94ba9a9_164_0.png)

图 5-11

第二次运行使用以下代码：

```python
#第二次运行数据摄取管道
......此处省略构造数据摄取管道的代码......

pipeline.load("./pipeline_storage")

docs = SimpleDirectoryReader(input_files=["../..//data/sales_tips1.txt"]).load_data()
nodes = pipeline.run(documents=docs,show_progress=True)
print(f'{len(nodes)} nodes ingested into vector store')
```

在运行前通过 `load` 方法从本地加载了文档存储对象。由于输入文档没有变化，输出如图 5-12 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_164_1.png)

图 5-12

这次没有任何 Node 被处理。流水线判定本次无需额外操作。若此时新增一个文档，新增部分会被处理，原有文档仍被忽略。

## 5.5 完整认识数据加载阶段

到这里，RAG 应用中数据加载与分割阶段的核心技术已经介绍完毕。图 5-13 总结了该阶段的关键组件及关系，有助于把握要点：

- （1）数据连接器即阅读器，能把不同来源文档提取为 Document 对象。
- （2）数据分割器即解析器，能把 Document 切分成更细粒度的 Node 对象。
- （3）元数据是 Node 的重要信息，可手动设定，也可借助抽取器自动生成。
- （4）数据摄取管道通过组装多个转换器实现 Node 的自动化连续处理，分割器、元数据抽取器、嵌入模型都可以作为转换器。
- （5）流水线具备缓存与持久化设计，并支持并行处理。

![](img/7eddb68cd925518fdc310e52e94ba9a9_165_0.png)

图 5-13

## 第6章 数据嵌入与索引

RAG 应用的增强环节依赖检索来获取相关上下文，再输入大模型生成结果。因此在加载与分割后，我们已得到大量知识块 Node，下一步就是为它们构建索引以便快速检索。

向量存储索引是 RAG 中最常见的形式，它基于嵌入模型与向量库构建，用于在生成阶段迅速找到相关 Node 形成上下文。向量存储并非万能方案，虽然语义检索优势明显，但在某些场景下表现不佳，本章也会介绍其他常用索引形式。

### 6.1 理解嵌入与向量

嵌入是指把文本内容和语义映射为多维浮点数，这些数字就是向量。向量的关键特性在于它不是简单的编码对应，而是承载了语义。如果两段文本语义相近，即使表面写法不同，它们的向量在数学上也会相近。

正是这个特性让向量能够支持语义检索：根据用户问题的向量，可以找出语义最贴近的多个 Node，而不是只做关键词匹配。

嵌入过程需要嵌入模型。不同模型在性能、维度、成本上各有差异，选型很重要。嵌入模型可以是远程调用的服务，如 OpenAI 的模型，也可以是本地部署的模型，如 TEI。需要特别注意，构建向量索引时使用的嵌入模型，必须与检索时使用的模型保持一致。

LlamaIndex 框架内部其实提供了好几种为 Node 生成向量的路径，但你在基于它搭建 RAG 时，很少会显式地去调用向量化代码，多半是框架在背后自动搞定。

#### 6.1.1 手动调用嵌入模型生成向量
在介绍嵌入模型时提到过，每个向量模型组件都内置了一个 get_text_embedding 方法，这就是给 Node 赋予向量最直接的入口：

```python
# 调用模型接口生成向量，此处使用批量接口
embeddings = embedded_model.get_text_embedding_batch(
    [node.get_content(metadata_mode=MetadataMode.EMBED) for node in nodes],show_progress=True)

#把生成的向量放到 Node 对象中
for node, embedding in zip(nodes, embeddings):
    node.embedding = embedding
```

每一个 Node 都会在 embedding 字段里保存计算出来的向量，这个字段来自 BaseNode 的属性定义，后续构造向量索引时正是依赖它。BaseNode 的属性集合也是后面构建索引的必要信息。

注意点：用来算向量的内容不要直接取 node.text，而应该调用 get_content 方法。因为 get_content 会按配置把部分元数据也拼进去，第 5 章讲过这个机制。

#### 6.1.2 用转换器来批量生成向量
还记得数据摄取管道里的转换器吗？所有嵌入模型组件都已经实现了转换器接口，所以完全可以充当转换器使用。上面那段手动生成向量的逻辑，实际上已经封装在嵌入模型的 __call__ 里，因此可以压缩成一行：

```python
nodes = embedded_model(nodes)
```

如果你走的是数据摄取管道，只需要用嵌入模型实例化一个转换器并插到管道里即可：

```python
......
pipeline = IngestionPipeline(
    transformations=[
        splitter,
        embedded_model    #把嵌入模型作为转换器，将自动生成向量
    ]
)
#运行后将会自动生成 nodes 向量
nodes = pipeline.run(documents=docs, show_progress=True)
```

### 6.2 向量存储的工作方式
向量存在的意义是让查询阶段能够做语义检索。关系数据库靠索引快速定位记录，向量检索也需要把向量存起来并建索引，这个工作就交给向量存储 VectorStore 来完成。向量存储可以是轻量嵌入式的比如 FAISS，也可以是客户端/服务器的本地或云端库比如 Chroma、Milvus。

LlamaIndex 支持多种向量存储，它们都实现了增删 Node、语义检索等基础标准接口。但底层实现差异很大，有的在内存、有的落盘、有的跑在云端，所以实际使用时要对照对应组件的文档。下面从接口层面对比两种常见类型。

#### 6.2.1 基于内存的简单向量库
SimpleVectorStore 是框架自带的一个内存型基础库。如果没有特别指定，框架会默认使用它。通常只适合做测试或原型，不建议上生产。

##### 1. 写入向量
把向量写入库的大致流程如下：

```python
#model
embedded_model = OllamaEmbedding(model_name="milkey/dmeta-embedding-zh:f16",
embed_batch_size=50)
Settings.embed_model=embedded_model

#docs
docs = [Document(text="百度文心一言是什么？文心一言是百度的大模型品牌。",
metadata={"title":"百度文心一言的概念"},doc_id="doc1"),
Document(text="什么是大模型？大模型是一种生成式推理 AI 模型。",
metadata={"title":"大模型的概念"},doc_id="doc2")]
splitter = SentenceSplitter(chunk_size=100, chunk_overlap=0)
nodes = splitter.get_nodes_from_documents(docs)

#生成嵌入向量
nodes = embedded_model(nodes)

#存储到向量库中
simple_vectorstore = SimpleVectorStore()
simple_vectorstore.add(nodes)
```

代码里先模拟了几个 Node，用嵌入模型算好向量，再调用简单向量库的 add 方法写入。打印对象可以看到类似图 6-1 的结构。

![](img/7eddb68cd925518fdc310e52e94ba9a9_170_0.png)

可以发现，SimpleVectorStore 除了保存每个 Node 的嵌入向量，还保留了 Node id 与原始 Document id 的映射关系，以及 Node 的元数据。

##### 2. 语义检索
向量写进去并建好索引后，就能用问题向量去找最相似的 Node：

```python
# 查询
result = simple_vectorstore.query(
VectorStoreQuery(query_embedding=embedded_model.get_text_embedding('什么是文心一言'),similarity_top_k=1))
print(result)
```

这里先组装一个 VectorStoreQuery，用嵌入模型把问题转成向量，再调用 SimpleVectorStore 的 query 接口，要求只返回 top_k=1 的结果，输出如下：

```python
VectorStoreQueryResult(nodes=None,
similarities=[0.8055511120370833],
ids=['a6e7fe8f-88fa-4c53-a004-bc06a4e8bb19'])
```

返回的是一个 VectorStoreQueryResult，内部包含命中 Node 的 id 和相似度 scores。值得注意的是 nodes=None，说明 SimpleVectorStore 本身不保留原始 Node 的完整内容，只存 id 与向量。第三方库通常会把完整内容也存下来，取用更方便。

##### 3. 持久化
内存库可以通过 persist 接口落盘：

```python
#持久化存储，默认存储到当前的./storage 目录中
simple_vectorstore.persist()
```

执行后会在本地 ./storage 目录下生成 vector_store.json，打开后可看到完整的对象快照，和之前的打印一致。

如果要恢复，可以这样加载：

```python
simple_vectorstore = SimpleVectorStore.from_persist_path('./storage/vector_store.json')
```

有了持久化与加载能力，就不用每次启动都重新算向量再写入。

#### 6.2.2 第三方向量库以 Chroma 为例
下面以本书常用到的 Chroma 为例说明第三方库的使用。框架里的对应封装是 ChromaVectorStore，本质是对官方 SDK 的一层包装。

##### 1. 写入向量
在 6.2.1 的例子上把 SimpleVectorStore 换成 ChromaVectorStore：

```python
......
from llama_index.vector_stores.chroma import ChromaVectorStore
......
#此处省略 Node 对象的准备过程，同上

#构造一个 collection 对象,此处使用 Server 模式下的 Chroma 向量库
chroma = chromadb.HttpClient(host="localhost", port=8000)
collection = chroma.get_or_create_collection(name="vectorstore")

#构造向量存储对象
vector_store = ChromaVectorStore(chroma_collection=collection)
ids = vector_store.add(nodes)
print(f'{len(ids)} nodes ingested into vector store')
pprint.pprint(vector_store.__dict__)
```

构造方式会不一样，但写入接口仍是 add，已带向量的 Node 可以直接存入。执行后的提示如下：

```python
2 nodes ingested into vector store
{'collection_kwargs': {},
 'collection_name': None,
 'flat_metadata': True,
 'headers': None,
 'host': None,
 'is_embedding_query': True,
 'persist_dir': None,
 'port': None,
 'ssl': False,
 'stores_text': True}
```

提示两个 Node 已写入 Chroma，但内部不会再展示 Node 的细节，因为它们已经进入 Chroma。

这里要关注 stores_text 这个属性。它表示库是否同时保存 Node 的文本内容。若为 True，写入时文本会一起保存，后续检索就能直接重建完整 Node 供大模型使用。若为 False，检索后只能靠 id 映射自己再去拼装 Node，流程更复杂。

需要的话，还能借助 Chroma 原生接口检查，比如用 collection.count 查看当前记录数：

```python
count_result = collection.count()
print('count_result:', count_result)
```

##### 2. 向量检索
检索写法和 SimpleVectorStore 完全一致，因为都遵循统一的 query 接口，但结果形态不同，如图 6-2 所示。

```python
......
#语义检索
result = vector_store.query(VectorStoreQuery(query_embedding=embedded_model.get_text_embedding('什么是语言模型'),similarity_top_k=1))
print(result)
```

![](img/7eddb68cd925518fdc310e52e94ba9a9_173_0.png)

图 6-2
区别很明显：SimpleVectorStore 的结果里 nodes 为空，而 ChromaVectorStore 在写入时会把 Node 的完整内容与元数据一并保存，所以 query 时能直接把完整的 Node 对象返回。

##### 3. 持久化机制
持久化由底层的 Chroma 保证，无需自行处理。
- 使用 Client/Server 模式时，数据由服务端持久化。
- 使用嵌入模式时，构造 ChromaVectorStore 时需要传入 persist_dir 指定目录。

### 6.3 向量存储索引
了解了向量和向量存储的基础后，我们进入更上层的向量存储索引组件。

索引的本质是在海量数据里快速定位目标数据的机制，RAG 场景下就是从解析分割得到的众多 Node 中，快速找出语义相关的一个或多个 Node，再把它们作为上下文交给大模型。LlamaIndex 在底层把索引和检索交给向量存储和向量库实现，为了简化检索和后续生成，又封装了更上层的 VectorStoreIndex。

#### 6.3.1 基于已有向量库构建索引
假设你已经完成了加载文档得到 Document、切分为 Node、为 Node 计算嵌入、写入向量库这几步，就可以在向量库之上直接封装一个索引对象并使用。下面复现完整的处理链路：

```python
······省略 embedded_model 与向量存储对象的构造······
#加载与解析文档，这里直接构造
docs = [
Document(text="智家机器人是一种人工智能家居软件，让您的家变得更智能，让您轻松地掌控生活的方方面面。",metadata={"title":"智家机器人"},doc_id="doc1"),
Document(text="速达飞行者是一种飞行汽车，能够让您在城市中自由翱翔，体验全新的出行方式。",metadata={"title":"速达飞行者"},doc_id="doc2")]

#文本分割
splitter = SentenceSplitter(chunk_size=100, chunk_overlap=0)
nodes = splitter.get_nodes_from_documents(docs)

#嵌入向量
nodes = embedded_model(nodes)

#存储到向量库中
vector_store.add(nodes)

#NEW: 构造基于向量存储的向量存储索引对象
index = VectorStoreIndex.from_vector_store(vector_store)

#测试
query_engine = index.as_query_engine()
response = query_engine.query(
"什么是速达飞行者"
)
```

通过 from_vector_store 方法，可以把已经装好向量的库快速包装成向量存储索引，进而进行检索与生成。

虽然 QueryEngine 尚未展开，但它正是利用 VectorStoreIndex 完成语义检索，再把检索到的知识作为上下文喂给大模型生成回答的组件。

可以推测 VectorStoreIndex 的工作方式：它调用底层向量存储的 query 方法得到相似结果，再根据结果重建出对应的 Node 列表，最后把这些内容组装为模型的上下文。真实实现还会涉及对不同底层库的支持，以及如何根据检索结果重建 Node 等细节。

图 6-3 展示了从原始文档到向量存储索引的完整构造流程，各个阶段的输入输出一目了然。

![](img/7eddb68cd925518fdc310e52e94ba9a9_176_0.png)

图 6-3

#### 6.3.2 直接用 Node 列表构建索引
前面基于向量库构造索引的过程已经很简洁了，还能更进一步吗？比如把 Node 列表写入向量库这一步交给索引自己完成？答案是可以的。下面看例子：

······省略用文档生成 Node 对象的部分，同 6.3.1 节······

```python
#以下代码被注释
#嵌入向量
#nodes = embedded_model(nodes)
#存储到向量库中
#vector_store.add(nodes)

#NEW: 构造基于向量存储的向量存储索引对象
index = VectorStoreIndex(nodes)

#测试
query_engine = index.as_query_engine()
response = query_engine.query(
    "什么是速达飞行者"
)
```

这段演示的核心就是用一份现成的 Node 列表直接实例化 VectorStoreIndex，关于向量如何生成、如何入库的部分都省略掉了。实际上这一步是全自动完成的：创建索引时框架会先检查 Node 是否已带向量，若没有就用默认的嵌入模型去生成；生成完毕后立刻写入向量库，最后把可用的索引对象交给你使用。

把这个过程和 6.3.1 节对照，就能提炼成图 6-4 的简化流程。

![](img/7eddb68cd925518fdc310e52e94ba9a9_177_0.png)

到这里有经验的开发者会产生疑问：构造索引既依赖嵌入模型也依赖向量存储，要怎么替换成自己想要的类型？最直接的办法是把索引对象的属性打印出来观察。

```python
index = VectorStoreIndex(nodes)
pprint.pprint(index.__dict__)
```

图 6-5 所示为输出结果。

![](img/7eddb68cd925518fdc310e52e94ba9a9_178_0.png)

图 6-5

内部结构一目了然，从标注处可以看到当前使用的嵌入模型是 OllamaEmbedding，这是之前在 Settings 组件里改过全局默认的结果；向量存储是 SimpleVectorStore 类型，说明不做显式指定时框架会自动选用 SimpleVectorStore 作为底层。

如果想更换向量存储，可以这样配置：

```python
#准备向量存储对象，此处采用 Chroma 向量库
vector_store = ChromaVectorStore(chroma_collection=collection)

#NEW: 构造基于向量存储的向量存储索引对象
storage_context = StorageContext.from_defaults(vector_store=vector_store)
```

```python
index = VectorStoreIndex(nodes, storage_context=storage_context)
```

再次打印索引结构，就能看到使用的向量存储类型已经发生变化，如图 6-6 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_179_0.png)

图 6-6

至此底层向量库就从默认切换成了 Chroma。

## 6.3.3 一行代码从文档建索引

再往上再抽象一层，连文档转 Node 列表的工作也在构造时自动完成。LlamaIndex 为 VectorStoreIndex 提供了最高层的封装，允许直接用 Document 对象创建索引，甚至可以不传任何额外参数。于是之前的写法可以压缩成：

```python
......
docs = [Document(text="智家机器人是一种人工智能家居软件，让您的家变得更智能，
让您轻松地掌控生活的方方面面。", metadata={"title": "智家机器人
"}, doc_id="doc1"),
Document(text="速达飞行者是一种飞行汽车，能够让您在城市中自由翱翔，
体验全新的出行方式。", metadata={"title": "速达飞行者"}, doc_id="doc2")]

#用文档构造向量存储索引对象
vector_index = VectorStoreIndex.from_documents(docs)
......
```

只需要一行。from_documents 方法就能快速把文档变成可用的向量存储索引，随后即可使用：

```python
query_engine = vector_index.as_query_engine()
response = query_engine.query(
    "请解释什么是区块链技术"
)
```

```python
print(response)
```

也就是说调用 from_documents 的过程中，框架已经自动完成了文档切分、向量化以及向量存储。精简后的整体处理流程如图 6-7 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_180_0.png)

虽然上层接口极大简化了构造过程，但在实际项目中往往需要对底层做个性化，比如模型、向量库、文本分割器、元数据抽取器等，具体怎么配置？

### 1. 模型
模型走全局 Settings 组件统一设置，涵盖大模型和嵌入模型。

### 2. 底层向量存储
参考 6.3.2 的做法，通过 storage_context 参数传入：

```python
storage_context =
StorageContext.from_defaults(vector_store=vector_store)
vector_index = \nVectorStoreIndex.from_documents(docs, storage_context =
storage_context)
```

借助自定义的 storage_context，就能控制最终使用的底层向量存储。

### 3. 文本分割器与元数据抽取器
from_documents 隐藏了切分细节。若要指定自己的分割器和抽取器，可通过 transformations 参数指定。例如：

```python
......
storage_context = \n
StorageContext.from_defaults(docstore=docstore,vector_store=vecto
r_store)

#定义文本分割器与元数据抽取器
mySplitter = SentenceWindowNodeParser(window_size=2)
myExtractor = TitleExtractor()

#将上述组件通过 transformations 参数传入
vector_index = VectorStoreIndex.from_documents(docs,
                        storage_context = storage_context,
transformations=[mySplitter,myExtractor])
```

这里传入了两个转换器，要求在索引构造前的数据摄取阶段使用它们处理文档并生成 Node 列表。

要验证是否生效，可以查询向量库中 Node 的信息：

```python
nodes =
vector_index._vector_store.query(VectorStoreQuery(query_str='速达
飞行者？',similarity_top_k=1)).nodes
pprint.pprint(nodes[0])
```

一切正常时可以看到如图 6-8 的输出。

```python
{
  'embedding': None,
  'end_char_idx': 42,
  'excluded_embed_metadata_keys': ['window', 'original_text'],
  'excluded_llm_metadata_keys': ['window', 'original_text'],
  'id_': '530b97b4-h193-42aa-b2f3-d6940ca83997',
  'metadata': {
    'document_title': '探索未来生活：智能家居机器人在新时代中的创新解决方案\n',
    'original_text': '智家机器人是一种人工智能家居软件，让您的家变得更智能，',
    'title': '智家机器人',
    'window': '智家机器人是一种人工智能家居软件，让您的家变得更智能，轻松掌'
  }
}
```

图 6-8

查询到的 Node 元数据里包含了 document_title 和 window 字段，正是 TitleExtractor 和 SentenceWindowNodeParser 这两个转换器产生的，证明配置生效了。

## 6.3.4 VectorStoreIndex 到底是怎么工作的

前面介绍了三种构造 VectorStoreIndex 的方式。

- 方法一：用向量存储构造
- 方法二：用 Node 列表构造
- 方法三：用文档直接构造

实现形式不同，本质相同，只是框架代劳的程度不同。下面从最高层的 from_documents 入手，简要拆解其工作过程：

```python
......
"""Create index from documents. """

# 接受传入的 storage_context 参数，或者使用默认的 storage 选项
storage_context = storage_context or StorageContext.from_defaults()
......
# 转换器，从输入参数中获得，或者使用默认的转换器
transformations = \n    transformations or transformations_from_settings_or_context(Settings, service_context)
......
# 运行转换器做数据摄取，生成要处理的 Node 对象
nodes = run_transformations(
```

```python
documents, # type: ignore
transformations,
show_progress=show_progress,
**kwargs,
)

#用生成的 Node 对象构造向量存储索引对象: 回到方法二
return cls(
    nodes=nodes,
    storage_context=storage_context,
    callback_manager=callback_manager,
    show_progress=show_progress,
    transformations=transformations,
    service_context=service_context,
    **kwargs,
)
```

核心逻辑如下。

（1）接收前面提到的 storage_context 参数与 transformations 参数，若未传入则使用默认的存储选项与数据转换器，向量存储默认 SimpleVectorStore，转换器默认 SentenceSplitter。
（2）调用 run_transformations 运行数据转换器，把输入的 Document 对象处理成 Node 对象。
（3）用生成的 Node 对象以及前面设定的 storage_context、transformations 等参数构造向量存储索引对象。注意，这里就回到了方法二。

再看方法二的流程：

```python
self._storage_context = storage_context or
StorageContext.from_defaults()
self._vector_store = self._storage_context.vector_store

with self._callback_manager.as_trace("index_construction"):
    if index_struct is None:
        nodes = nodes or []
```

```python
index_struct = self.build_index_from_nodes(
    nodes + objects  # type: ignore
)
self._index_struct = index_struct

......
```

继续追踪 build_index_from_nodes，最后会由内部函数 _add_nodes_to_index 实现关键逻辑：

```python
......
    for nodes_batch in iter_batch(nodes,
self._insert_batch_size):
        nodes_batch = \n            self._get_node_with_embedding(nodes_batch,
show_progress)
        new_ids = self._vector_store.add(nodes_batch,
**insert_kwargs)
......
```

该函数里使用 _get_node_with_embedding 与 vector_store.add，正是方法二中被隐藏的自动工作：生成嵌入向量、写入向量库。而这些工作在方法一里需要开发者自行完成，因此两种方法本质统一。

VectorStoreIndex 是依赖向量库的索引封装，提供向量检索的高层接口，可用向量存储、Node 列表或文档来构造。

图 6-9 展示了 VectorStoreIndex 与相关类型的关系，有助于理解 RAG 应用中最核心的索引。

![](img/7eddb68cd925518fdc310e52e94ba9a9_185_0.png)

图 6-9

## 6.4 别只盯着向量索引

向量存储索引是 RAG 的主流，但在很多场景下需要结合其他索引类型提升检索能力。下面介绍几种常见类型及其用法。

### 6.4.1 摘要索引实战

文档摘要索引的工作方式是借助大模型把传入的 Node 列表生成摘要 Node，并将其向量化后存入向量库，提供摘要 Node 的向量检索能力，同时支持从摘要 Node 回溯到原始 Document 和基础 Node。

与向量存储索引的最大区别在于，它不直接对基础 Node 做语义检索，而是先在摘要层检索，再映射回基础 Node。

对象类型为 DocumentSummaryIndex。最简示例：

```python
......这里省略了 text 中的大段内容......
docs = [Document(text="小麦智能健康手环是一款...",metadata={"title":"智家机器人"},doc_id="doc1"),Document(text="速达飞行者...",metadata={"title":"速达飞行者"},doc_id="doc2")]
doc_summary_index = DocumentSummaryIndex.from_documents(docs)
......
```

与向量存储索引一样，只需 from_documents 即可构造。构造完成后可用以下代码查询文档级摘要验证效果：

```python
pprint.pprint(doc_summary_index.get_document_summary("doc1"))
```

图 6-10 所示为输出的摘要。

![](img/7eddb68cd925518fdc310e52e94ba9a9_186_0.png)

框架会自动为传入的 Document 生成摘要：默认包含对文档的简要总结以及若干假设性问题。和向量索引一样，如果不想用默认底层库，可通过设置 storage_context 参数修改。此外，也可通过 summary_query 参数修改生成摘要的 Prompt、更换生成摘要使用的大模型等，设置示例可参考如下：

```python
# vector store
# 构造一个存储上下文对象，用于设置向量存储
storage_context = StorageContext.from_defaults(vector_store=vector_store)

# storage_context: 设置向量库等
# summary_query: 设置摘要生成提示
# llm: 设置生成摘要的大模型
# transformations: 设置数据摄取需要的转换器
doc_summary_index = DocumentSummaryIndex.from_documents(
    docs,
    storage_context=storage_context,
    summary_query="用中文描述所给文本的主要内容，同时描述这段文本可以回答的一些问题。"
)
pprint.pprint(doc_summary_index.get_document_summary("doc1"))
```

执行后即可看到，摘要已经按照新设定的 Prompt 规则生成，示例见图 6-11。

![](img/7eddb68cd925518fdc310e52e94ba9a9_187_0.png)

其余参数也能自行尝试调整。

## 6.4.2 对象索引

ObjectIndex 并不是只针对文本，它能够把任意 Python 对象纳入索引体系。正是这种泛化能力，使其在需要为大量结构化对象建索引并按条件直接取回完整对象时特别实用。典型场景有：

1. Text-to-SQL 构建时，模型要生成正确的 SQL 就必须拿到相关的表结构信息。但在超大数据库里，schema 整体过大容易超出上下文窗口导致推理失败，借助对象索引可以在执行瞬间只拉取必要的 schema 部分。

2. 在 AI Agent 开发中，Agent 面对海量工具需要自行规划并挑选合适的工具使用。利用对象索引可以先检索出任务相关的那一小撮工具，再把它们交给 Agent 处理。

下面我们先准备一批 Python 对象，来演示 ObjectIndex 的使用方式：

```python
# 构造一些不同类型的普通对象
obj1 = {"name": "小米", "cpu": "骁龙", "battery": "5000mAh", "display": "6.67 英寸"}
obj2 = ["iPhone", "小米", "华为", "三星"]
obj3 = (['A', 'B', 'C'], [100, 200, 300])
obj4 = "大模型是一种基于自然语言处理技术的生成式 AI 模型!"
objs = [obj1, obj2, obj3, obj4]

# 从普通对象到 Node 对象的映射，即生成嵌入所需要的 Node 对象
obj_node_mapping = SimpleObjectNodeMapping.from_objects(objs)
nodes = obj_node_mapping.to_nodes(objs)

# 构造对象索引
storage_context = StorageContext.from_defaults(vector_store=vector_store)
object_index = ObjectIndex(
    index=VectorStoreIndex(nodes=nodes, storage_context=storage_context),
    object_node_mapping=obj_node_mapping,
)

# 构造一个检索器，测试检索结果
object_retriever = object_index.as_retriever(similarity_top_k=1)
results = object_retriever.retrieve("小米手机")
print(f'results: {results}')
```

运行结果如图 6-12 所示，系统根据输入准确地把匹配的普通对象返回出来。

![](img/7eddb68cd925518fdc310e52e94ba9a9_188_0.png)

results: [{'name': '小米', 'cpu': '骁龙', 'battery': '5000mAh', 'display': '6.67英寸'}]

与常规向量存储索引不同，对象索引本质上是一种依赖型索引。它并不自己存储向量，而是借用向量存储索引等底层组件完成 Node 的嵌入与检索，这也是创建时必须传入一个 VectorStoreIndex 实例的原因。

为了让向量索引检索到的 Node 能对应回原始对象，需要一个 ObjectNodeMapping 来维护映射关系，主要负责两件事：

1. 把普通对象转成向量索引可以识别的 Node 形式。
2. 检索结束后，依据查到的 Node 反查出最初的 Python 对象并返回。

示例中使用了 SimpleObjectNodeMapping 来完成映射。它的转换思路很直接：先调用 `str` 把对象序列化成字符串，再基于该字符串构造一个 TextNode。

```python
def to_nodes(self, objs: Sequence[OT]) -> Sequence[TextNode]:
    return [self.to_node(obj) for obj in objs]

def to_node(self, obj: Any) -> TextNode:
    return TextNode(text=str(obj))
```

在初始化 SimpleObjectNodeMapping 时，字符串与原始对象的对应关系也会被一起保存下来：

```python
def __init__(self, objs: Optional[Sequence[Any]] = None) -> None:
    objs = objs or []
    for obj in objs:
        self.validate_object(obj)
    self._objs = {hash(str(obj)): obj for obj in objs}
```

有了这份映射，借助向量索引查到 Node 后，只需取出 Node 的文本内容计算哈希，就能通过映射定位并返回对应的原始对象！

图 6-13 展示了对象索引类型的技术原理。

![](img/7eddb68cd925518fdc310e52e94ba9a9_190_0.png)

图 6-13

前面为了更清楚地展现内部细节，采用了相对繁琐的写法。实际上 LlamaIndex 已提供更简便的构造方式，可直接用普通对象创建对象索引并指定底层索引配置，两种方式最终效果一致：

```python
......
storage_context = StorageContext.from_defaults(vector_store=vector_store)
object_index = ObjectIndex.from_objects(
    objs,
    index_cls=VectorStoreIndex,
    storage_context=storage_context
)
```

## 6.4.3 知识图谱索引

知识图谱是一种基于图结构的语义知识组织方式，用来描述实体、概念及其相互关系并存入图数据库，广泛用于搜索、问答、推荐等下游任务。相比纯自然语言存储，图谱更直观也更利于计算机处理。它通常从结构化或非结构化文档中抽取、融合信息，整理成“实体-关系-实体”的三元组形式，其中实体可携带多个属性，再用于存储与应用。

### 关于图与图数据库

图是用来表达对象及其关系的结构化数据，任意两个对象可以直接建立联系，因此很适合刻画复杂关系。一个图主要由节点和边构成。

1. Node：代表一个具体对象，例如社交网络中的一个用户。
2. 边：代表对象之间的关系，例如用户之间的关注关系。

图 6-14 展示了一个包含工厂、产品、仓库、城市等实体的图示例。

![](img/7eddb68cd925518fdc310e52e94ba9a9_191_0.png)

图 6-14

图数据库是专为存储和操作图结构数据而设计的数据库管理系统。与关系数据库不同，它以 Node、边、属性的方式组织数据，非常适合处理高度互联的数据，提供高效的复杂查询与遍历能力。特点如下：

1. 模型灵活：能便捷地表达复杂关系。
2. 查询高效：尤其在多跳关系查询上比关系数据库更占优。
3. 可扩展：可处理海量节点与边。

常见方案包括 Neo4j、Amazon Neptune、OrientDB、TigerGraph 等，已广泛应用于社交网络分析、推荐系统、金融反欺诈等领域。

在 RAG 场景里，知识图谱索引与向量存储索引完全不同，且与对象索引一样，其底层并不依赖向量索引，是一种相对独立的索引类型。LlamaIndex 的知识图谱索引使用 PropertyGraphStore 作为底层存储抽象，旧版本称为 GraphStore，与负责存向量的 VectorStore 相对应。

基于知识图谱索引的 RAG 在查询或对话时，通常会先对自然语言输入做关键词解析，抽取出关键信息，再在图谱中检索相关实体与关系，必要时融合原始文本，最后交给大模型生成回复。

下面用一个例子说明用法。我们先借助 AI 工具生成一段包含适合图谱表达知识的自然语言文本，如图 6-15 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_192_0.png)

图 6-15

随后用代码基于这段文字构建知识图谱索引并实现简易问答，核心代码如下：

```python
documents = SimpleDirectoryReader(
    input_files=["../../data/graph.txt"],
).load_data()

# 指定知识图谱的存储，这里使用内存存储
property_graph_store = SimplePropertyGraphStore()
storage_context = StorageContext.from_defaults(property_graph_store=property_graph_store)

# 构造知识图谱索引（这里进行了本地化存储）
if not os.path.exists(f"./storage/graph_store"):
    index = PropertyGraphIndex.from_documents(
        documents,
        storage_context=storage_context
    )
    index.storage_context.persist(persist_dir="./storage/graph_store")
else:
    print('Loading graph index...')
    index = load_index_from_storage(
        StorageContext.from_defaults(persist_dir="./storage/graph_store")
    )

# 构造查询引擎
query_engine = index.as_query_engine(
    include_text=True, similarity_top_k=2
)

response = query_engine.query(
    "介绍一下西京的城市信息吧",
)
print(f"Response: {response}")
```

对比可以发现，知识图谱索引的构建流程与向量索引很像，主要差异在：

1. 底层存储不再是 VectorStore，而是 PropertyGraphStore。示例中使用 SimplePropertyGraphStore，实际也能接入商业图数据库如 Neo4j，与向量库可替换的思路一致。
2. 构造的索引类型也不是 VectorStoreIndex，而是 PropertyGraphIndex，同样通过 `from_documents` 方法创建。

查看 LlamaIndex 底层逻辑，`from_documents` 会先用默认分词器把 Document 切成 Node 列表，再据此建索引。两种索引在此方法上的处理不同：

1. VectorStoreIndex 的处理：为每个 Node 生成向量，将内容、向量、元数据写入底层向量库如 Chroma，同时保存存储到原始 Node 的映射。
2. PropertyGraphIndex 的处理：借助大模型从 Node 内容中抽取实体与关系信息，通常形成实体-关系-实体的三元组，写入 PropertyGraphStore，并本地保存实体到原始对象的映射。若需定制抽取，可通过 `kg_extractors` 参数指定。

为了更直观地了解构建结果，我们打印 PropertyGraphStore 中 `graph` 属性的部分内容：

```python
# 查看构造的知识图谱索引（这里打印了保存的三元组）
graph = index.property_graph_store.graph
pprint.pprint(graph.triplets)
```

输出如图 6-16 所示，因大模型存在随机性，每次结果可能略有差异。

![](img/7eddb68cd925518fdc310e52e94ba9a9_194_0.png)

图 6-16

借助大模型将输入文本的知识抽取并转为三元组，这正是构建知识图谱索引的基本单元。该三元组存入 PropertyGraphStore 后形成图谱的节点与关系，在生成阶段会被检索作为上下文使用。

最后看一下该演示在查询后的输出，如图 6-17 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_195_0.png)

图 6-17

若用 Langfuse 追踪执行，可看到大模型生成前的上下文。检索时框架会在图谱中按算法找到相关实体与关系，并作为上下文传递给大模型，如图 6-18 所示，其中携带源文本是因为设置了 `include_text=True`。

```text
"Context information is below.
-------------------------
file_path: ../../data/graph.txt

Here are some facts extracted from the provided text:

城市 -> 是 -> 经济中心
城市 -> 是 -> 文化中心
西京 -> 位于 -> 东部地区
西京 -> 是 -> 文化名城
城市 -> 是 -> 教育中心
城市 -> 人口数量超过 -> 1000万
城市 -> 总面积约为 -> 5000平方公里
城市 -> 是 -> 科技中心

西京是一座历史悠久的文化名城,位于我国的东部地区。这座城市有着丰富的自然资源和人文景观,总
万。这座城市是我国重要的经济、科技、教育和文化中心之一。西京有一座著名的大学,成立于1950年
学、工程技术、人文社会科学、医学、艺术等多个领域的本科和研究生教育项目。这所大学致力于培养
国家的经济社会发展做出贡献。西京的科技产业发展迅速,拥有多个高新技术产业园区,吸引了众多国
息、生物医药、新材料、新能源等多个领域,为城市的经济发展注入了强大的动力。在西京的城市中心,
量珍贵的文物和艺术品,展示了这座城市从古至今的发展历程。此外,博物馆还定期举办各类展览和讲
学习。西京还有多个著名的旅游景点,如古老的城墙、庙宇、园林和现代化的购物中心、游乐场等。这
游,为城市的旅游业带来了繁荣。西京周边,还有多个农业产区,出产丰富的农产品,如水果、蔬菜、
在国内外市场上享有很高的声誉,为城市的农业产业发展提供了有力支持。
-------------------------
Given the context information and not prior knowledge, answer the query.
Query: 介绍下西京的城市信息吧
Answer: "
```

图 6-18

知识图谱的落地本身就是一个系统工程，从信息的抽取、生成，到存储、处理，再到最终应用，涉及的技术和工具非常多。引入知识图谱索引后，RAG 就能在底层存储层面直接兼容以图谱形态存在的结构化知识，这就把知识图谱在生成式 AI 中的应用边界拉得更开了。前面演示的 SimplePropertyGraphStore 属于基于内存的基础实现。到了真实的生产环境，存储通常会交给更成熟可控的图数据库，比如 Neo4j 来承载。

## 6.4.4 树状索引

树状索引的核心就是一棵树。构建时，系统会把输入的 Node 列表交给大模型做汇总和提炼，形成上层的父 Node，也叫 Parent Node，然后自底向上递归地长出整棵索引树，构造时可以通过参数控制，只有当叶子节点数量超过阈值才会生成上级节点。检索时可以根据不同策略从这棵树里拿到所需节点，可能是根节点 Root Node，也可能是叶子节点 Leaf Node。简单结构示意图如图 6-19 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_196_0.png)

图 6-19

下面用一个最小例子来搭建树索引进行验证：

```python
......省略构造 Document 对象......
SentenceSplitter = SentenceSplitter(chunk_size=200, chunk_overlap=0)
nodes = SentenceSplitter.get_nodes_from_documents(documents)
```

## 构造树索引

```python
index = TreeIndex(nodes, num_children=2)

# 打印索引结构
print_attrs(index.index_struct)
```

打印出来的索引结构可以看到，如图 6-20，体系里除了根节点和叶子节点，还保留了父子节点的映射关系 node_id_to_children_ids，也就是每个节点对应的叶子集合，借助简单的迭代就能拼出可用于检索的索引树。

![](img/7eddb68cd925518fdc310e52e94ba9a9_197_0.png)

树索引在检索时的默认行为是根据问题定位相关的叶子节点，但检索方式是可以指定的，比如用向量相似度来找，还是让大模型来判断相关性，可以取根节点也可以取叶子节点，还能控制返回的数量。具体用法会在 7.1 节展开。

## 6.4.5 关键词映射索引

再看一种更直观、也更常见的索引形态：关键词表索引。思路是为每个 Node 的内容抽取关键词，然后建立关键词到 Node 的映射作为索引。整体结构如图 6-21。

![](img/7eddb68cd925518fdc310e52e94ba9a9_198_0.png)

关键词表索引在构建和检索时最关键的决策是关键词怎么来。当前框架里提供三种主流做法，可按需选用。

- 1）借助大模型智能抽取内容关键词，对应组件是 KeywordTableIndex。
- 2）借助正则表达式抽取内容关键词，对应组件是 SimpleKeywordTableIndex，不过该实现目前靠空格切分词，所以暂时只适合英文。
- 3）借助 RAKE 这一轻量级的自然语言关键词提取库来抽取，对应组件是 RAKEKeywordTableIndex。

下面用一个简单例子演示关键词表索引的使用方式：

```python
......构造 Document 对象......
nodes = SentenceSplitter.get_nodes_from_documents(documents)

# 构造关键词表索引，用大模型智能提取内容关键词
index = KeywordTableIndex(nodes)

# 打印索引的内部结构（自定义方法）
print_attrs(index.index_struct)

# 测试
query_engine = index.as_query_engine()
response = query_engine.query(
    "文心一言的主要应用场景有哪些？",
)
print(f"Response: {response}")
```

打印内部结构可以看到，如图 6-22，索引保存的是这样的映射：每一个抽取出的关键词，比如“文心一言”“大语言模型”，都会指向一个 Node ID 集合。正是这一层映射，让检索时先从输入问题抽取关键词，再在索引里查到对应的 Node，排序后输出指定数量的结果。

```
Object of type: <class 'llama_index.core.data_structs.data_structs.KeywordTable'>
index_id:
    Object of type: <class 'str'>
        Value: 5ae15196-435f-4f35-8abe-6db4a071186a
summary:
    Object of type: <class 'NoneType'>
        Value: None
table:
    Object of type: <class 'dict'>
        百度研发:
            Object of type: <class 'set'>
                Item 0:
                    Object of type: <class 'str'>
                        Value: 41f37207-9a20-4352-99ed-6387a672df1e
        大语言模型:
            Object of type: <class 'set'>
                Item 0:
                    Object of type: <class 'str'>
                        Value: 41f37207-9a20-4352-99ed-6387a672df1e
        文心一言:
            Object of type: <class 'set'>
                Item 0:
                    Object of type: <class 'str'>
                        Value: 624b7e0b-cf8c-4605-bd3a-d264388ff3ec
                Item 1:
                    Object of type: <class 'str'>
                        Value: 41f37207-9a20-4352-99ed-6387a672df1e
                Item 2:
                    Object of type: <class 'str'>
                        Value: 8772537e-b5f0-4e08-8cb8-078e00a052c9
        人工智能:
```

图 6-22

## 第 7 章 检索、响应生成与 RAG 引擎

RAG 的本质是给大模型补充经过检索和过滤的外部知识，从而实现增强生成并降低幻觉，真正的“G”才是最终目标。在数据准备和索引搭建完成之后，就进入了 RAG 的实战阶段，即检索与响应生成。只要把检索和响应生成串起来，就形成了一个核心的 RAG 引擎，也就具备了搭建端到端大模型应用的基础。一个 RAG 引擎能够接收用户的提问，并依靠自身的检索与生成能力给出最终答案，如图 7-1。

![](img/7eddb68cd925518fdc310e52e94ba9a9_200_0.png)

图 7-1

从使用形态看，RAG 引擎可以分成两类基础类型。

- 1）查询引擎：最直接的形态。用户用自然语言提问，引擎经过检索、排序、生成等流程后输出答案。
- 2）对话引擎：在查询的基础上增加了连续、多轮、有状态的上下文能力，需要具备上下文记忆。

在搭建 RAG 引擎之前，先要搞清楚检索器和响应生成器这两个基础组件。

## 7.1 检索器

检索器是生成的基础。它负责根据用户输入找到最相关的知识上下文，并以 Node 列表形式返回。没有检索器，就谈不上增强生成。检索器一般是基于各类索引组件派生出来的，索引类型不同，对应的检索器也不一样。

### 7.1.1 快速搭建检索器

检索器可以直接从已准备好的索引组件派生。比如最快的方式是几行代码搭一个向量索引的检索器并完成一次检索：

```python
retriever = vector_index.as_retriever(similarity_top_k=1)
nodes = retriever.retrieve('文心一言的应用场景')
pprint.pprint(nodes)
```

最直接的派生方式是调用 `as_retriever`。构造时常用的配置有 `retriever_mode` 用来指定检索模式，`similarity_top_k` 用来控制语义最相似的返回数量等，这些参数一般在调用 `as_retriever` 时传入。

在 7.3 节介绍查询引擎时，还会看到一种隐式自动创建检索器的做法，例如下面这段构造查询引擎的代码就会先自动生成检索器：

```python
query_engine = index.as_query_engine()
```

检索器组件最核心的方法是 `retrieve`，输入是检索条件，输出是带相关性评分 Score 的 Node 列表。这个列表会送给后面的响应生成器。图 7-2 展示了 `retrieve` 的返回样例，注意返回类型是 Node 的增强类型 NodeWithScore。

![](img/7eddb68cd925518fdc310e52e94ba9a9_202_0.png)

图 7-2

和很多组件一样，也可以用底层 API 直接构造检索器。如下面的写法与使用 `as_retriever` 的效果完全一致：

```python
......
# 构造向量索引检索器
retriever = VectorIndexRetriever(
    index=vector_index,
    ... # 其他参数
)

# 构造摘要索引检索器
retriever = SummaryIndexLLMRetriever(
    index=summary_index,
    choice_batch_size=5,
)
```

这种方式需要明确指定检索器的具体类型，所以得先了解不同索引、不同检索模式对应的检索器实现。建议优先使用 `as_retriever` 来构造。

### 7.1.2 检索模式与参数解读

部分索引支持多种检索模式，也存在默认模式。不同模式在流程、依赖模型与工具、效果和性能上各有差异，需要结合应用需求、数据特点以及实际测试结果来选择。此外，各类索引常有一些特殊检索参数，掌握它们能让使用更高效。

#### 1. 如何指定检索模式

如果走 `as_retriever` 路线，可以通过 `retriever_mode` 参数来指定模式，例如：

```python
retriever = treeindex.as_retriever(
    retriever_mode="root",
)
```

如果直接实例化 retriever 对象，则需要先知道该模式对应的具体类型并传入 `index`。两种做法最终效果相同，例如：

```python
from llama_index.core.indices.tree.all_leaf_retriever import TreeAllLeafRetriever
# TreeRootRetriever 是检索模式 Root 对应的类型
retriever = TreeRootRetriever(index=treeindex)
```

#### 2. 不同类型的索引支持的检索模式

**1）向量存储索引 VectorStoreIndex**

RAG 中最常用的索引类型。向量存储索引只支持一种检索模式，即基于向量语义相似度检索，`as_retriever` 中的 `retriever_mode` 会被忽略，对应检索器为 `VectorIndexRetriever`。常用参数见表 7-1。

表 7-1

| 参数名 | 用途 |
| --- | --- |
| similarity_top_k | 返回相关性最高的前 N 个 Node，默认 2 |
| filters | 元数据过滤器，检索前先做元数据过滤 |
| vector_store_query_mode | 向量库查询模式，需底层向量库支持 |

该检索器的部分特性受底层向量库影响，需要参考具体向量库文档。

**2）文档摘要索引 DocumentSummaryIndex**

该索引会在 Document 层面生成摘要 Node 并嵌入索引，检索时先找相关摘要，再回溯到原始基础 Node。支持两种模式。

(1) `llm`：用大模型判断摘要与问题的相关性，取到最相关的摘要后返回对应基础 Node，对应检索器为 `DocumentSummaryIndexLLMRetriever`。常用参数见表 7-2。

表 7-2

| 参数名 | 用途 |
| --- | --- |
| choice_select_prompt | 判断摘要相关性的 Prompt 模板 |
| choice_top_k | 选择摘要 Node 的数量，注意是摘要数量而非最终返回数 |

(2) `embedding`：用嵌入模型和向量相似度判断摘要相关性，同样回溯到基础 Node，对应检索器为 `DocumentSummaryIndexEmbeddingRetriever`。常用参数见表 7-3。

表 7-3

| 参数名 | 用途 |
| --- | --- |
| similarity_top_k | 选择摘要 Node 的数量，注意是摘要数量而非最终返回数 |

**3）树索引 TreeIndex**

树索引把输入 Node 作为叶子，自底向上生成带摘要的父节点，多次迭代形成树。检索时可拿到父节点或叶子节点。支持的模式如下。

(1) `select_leaf`：从根节点逐层检索相关叶子节点，相关性判断由大模型完成，对应检索器为 `TreeSelectLeafRetriever`。常用参数见表 7-4。

表 7-4

| 参数名 | 用途 |
|---|---|
| query_template | 判断相关叶子节点使用的 Prompt 模板，单选 |
| query_template_multiple | 判断相关叶子节点使用的 Prompt 模板，多选 |
| child_branch_factor | 控制每层是单选还是多选 |

（2）`select_leaf_embedding`：同样从根逐层检索叶子节点，但相关性判断改用向量相似度，对应检索器为 `TreeSelectLeafEmbeddingRetriever`，属于前者的子类型，仅判断方式不同。

（3）`all_leaf`：直接返回全部叶子节点，此时索引树不产生作用，对应检索器为 `TreeAllLeafRetriever`。

（4）`root`：返回全部根节点，对应检索器为 `TreeRootRetriever`。

**4）关键词表索引 KeywordTableIndex**

该索引从 Node 列表中解析关键词并建立关键词到 Node 的映射。检索时先从问题中抽取关键词，再定位相关 Node。支持的模式如下。

（1）`default`：默认模式。借助大模型对问题做关键词解析，再通过关键词查找相关 Node，对应检索器为 `KeywordTableGPTRetriever`。常用参数见表 7-5。

表 7-5

| 参数名 | 用途 |
|---|---|
| query_keyword_extract_template | 大模型抽取关键词的 Prompt 模板 |

LlamaIndex 检索与合成机制详解

| max_keywords_per_query | 单次查询解析出的最大关键词数量 |

（2）`simple`：用简易正则对提问进行关键词抽取，再依据关键词去查表中的相关 Node，检索器实现为 `KeywordTableSimpleRetriever`。这种做法在中文场景下基本不可用。

（3）`rake`：借助 RAKE 库完成提问的关键词抽取，随后按关键词定位相关 Node，对应检索器为 `KeywordTableRAKERetriever`。

**5）对象索引（ObjectIndex）**

对象索引本质上是依附于其它索引的特殊类型，先把对象序列化后再交由底层索引来承载。因此它的检索行为完全由所依赖的底层索引决定。例如下面这种用法把向量存储当作底座，检索模式也就变成了向量检索：

```python
object_index = ObjectIndex.from_objects(
    objs,
    index_cls=VectorStoreIndex, storage_context=storage_context
)
```

**6）知识图谱索引（PropertyGraphIndex）**

知识图谱索引把结构化信息保存在图里，由大量 Node、Property、Relationship 构成，同时往往还会保存 Node 内容的嵌入向量。常用的检索方式有三类。

（1）Text-to-Cypher：借助大模型把自然语言转成图数据库可执行的查询语言，例如 Neo4j 的 Cypher，再执行检索。

（2）Vector Search（向量相似度检索）：构建图谱时为 Node 与关系生成向量，检索时通过向量相似度找出关联的 Node 与关系作为上下文，前提是图数据库支持向量检索。

（3）Keywords Search（关键词检索）：先用大模型从自然语言问题里抽关键词，再结合图数据库能力检索出相关的 Node 与关系作为上下文。

为了兼顾以上模式，LlamaIndex 内置了多种子检索器，构造检索器时可通过参数指定，见表 7-6。

表 7-6

| 参数名 | 用途 |
| --- | --- |
| sub_retrievers | 检索时使用的多个子检索器列表 |

以下是一个为知识图谱索引指定多个子检索器的示例:

```python
# 构造两个子检索器
synonym_retriever = LLMsSynonymRetriever(
    index.property_graph_store,
    llm=llm,
    include_text=False,
    output_parsing_fn=parse_fn,
    max_keywords=10,
    synonym_prompt=prompt,
    path_depth=1,
)

vector_retriever = VectorContextRetriever(
    index.property_graph_store,
    include_text=False,
    similarity_top_k=2,
    path_depth=1,
)

# 构造一个知识图谱检索器
retriever = PGRetriever(sub_retrievers=[synonym_retriever, vector_retriever])

# 也可以直接在查询引擎中指定子检索器
query_engine = index.as_query_engine(
    include_text=True,
    similarity_top_k=1, sub_retrievers=[synonym_retriever, vector_retriever]
)
```

## 7.1.3 初步认识递归检索

在 LlamaIndex 源码里，检索器的 `retrieve` 方法在拿到一批相关 Node 之后，还会执行一次递归检索，具体是调用图 7-3 所示的 `_handle_recursive_retrieval` 方法。

```python
with retrieve_event:
    nodes = self._retrieve(query_bundle)
    nodes = self._handle_recursive_retrieval(query_bundle, nodes)
    retrieve_event.on_end(
        payload={EventPayload.NODES: nodes},
    )
```

图 7-3

这正是之前介绍 Node 时提到的 IndexNode 的递归检索。IndexNode 内部保存了指向其它对象 Node / Retriever / QueryEngine 等的引用，可以实现向下钻取式的二次检索。指向对象的类型不同，二次检索的行为也不同，见图 7-4。

```python
"""Retrieve nodes from object."""
if self._verbose:
    print_text(
        f"Retrieving from object {obj.__class__.__name__} with query",
        color="llama_pink",
    )
if isinstance(obj, NodeWithScore):
    return [obj]
elif isinstance(obj, BaseNode):
    return [NodeWithScore(node=obj, score=score)]
elif isinstance(obj, BaseQueryEngine):
    response = obj.query(query_bundle)
    return [
        NodeWithScore(
            node=TextNode(text=str(response), metadata=response.metadata),
            score=score,
        )
    ]
```

图 7-4

- （1）如果指向其它 Node，则直接返回被指向的 Node，用于从摘要回溯到源内容。
- （2）如果指向查询引擎，则调用该引擎得到响应并组装成 Node 返回。
- （3）如果指向检索器，则调用该检索器做二次检索，返回检索出的 Node。

这些二次检索得到的 Node 会作为最终检索结果，供后续生成使用。

IndexNode 与递归检索的实战将在高级篇中展开。

## 7.2 响应生成器

检索器把相关上下文以 Node 列表的形式交出来后，就进入了响应生成阶段。LlamaIndex 里负责这一环的组件是 Synthesizer，也称响应生成器 / 合成器。它并不是简单地把问题和上下文拼接后让大模型一次性回答，而是提供了多种生成范式。因为在真实 RAG 场景里，直接一次性推理往往质量不足或难以满足需求，所以出现了不同的生成模式。Synthesizer 的作用就是把这些模式的流程抽象统一，框架提供多种实现并通过统一接口交给上层 RAG 引擎。

响应生成器组件与外部的关系大致如图 7-5 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_209_0.png)

图中涉及输入问题、检索到的 Node 列表、Prompt 模板、大模型，以及流式输出、输出格式等控制参数。可以看到响应生成器一定在检索之后才运行，生成结果；在一些迭代式 RAG 范式里，也可能多次调用。

### 7.2.1 构造响应生成器

构造方式主要是显式与隐式两种。显式构造是直接创建响应生成器后在 RAG 引擎中使用，最常用的是 `get_response_synthesizer` 方法并传入响应模式参数。

```python
# 构造一个响应生成器
response_synthesizer = get_response_synthesizer(
    response_mode=ResponseMode.COMPACT
)

# 测试：调用响应生成器生成结果
response = response_synthesizer.synthesize(
    "你的输入问题",
    nodes=nodes
)

# 后面使用：在调用 as_query_engine 方法时指定
query_engine = vector_index.as_query_engine(
    response_synthesizer=response_synthesizer
)

# 后面使用：或者在直接构造查询引擎时指定
query_engine = RetrieverQueryEngine(
    retriever=retriever,
    response_synthesizer=response_synthesizer
)
```

构造完成后可以直接调用 `synthesize` 方法做单次测试，更多情况是用来组装后续的查询引擎，比如上面代码中的用法。

隐式构造则是在创建 RAG 引擎时自动完成。例如下面这种方式会同时自动创建默认检索器和响应生成器，并在构造时指定生成模式参数：

```python
# 使用隐式构造方法自动构造检索器与响应生成器
query_engine = vector_index.as_query_engine(streaming=True,
                                              verbose=True,
                                              response_mode=ResponseMode.COMPACT)
```

### 7.2.2 响应生成模式

响应生成器的核心价值在于为多种生成模式提供统一接口，而模式本身通常由 `response_mode` 参数控制。LlamaIndex 内置了多种算法，切换参数即可切换模式。这些模式代表了使用上下文回答问题的方式不同，了解它们有助于优化 RAG 输出质量。

#### 1. refine 模式

refine 是一个迭代式生成模式，流程如下。

（1）用检索到的第一个 Node 的上下文和输入问题生成初始答案。
（2）把该答案、输入问题与第二个 Node 的上下文一起组装成 refine prompt，交给大模型生成细化后的答案。
（3）循环此过程，直到所有 Node 都被用于细化。
（4）若中途出现上下文窗口溢出，则对该 Node 内容进行切分，形成新 Node 加入处理队列。

refine 的生成过程如图 7-6 所示，query_str 为输入问题。

![](img/7eddb68cd925518fdc310e52e94ba9a9_212_0.png)

可以推断，若检索器返回 N 个 Node，响应生成器至少会让大模型处理 N 次，属于耗时较长、token 成本较高的模式，适合对答案完整度要求很高的场景。

下面用一个简化示例追踪 refine 下的大模型调用次数。为了不纠结具体答案，这里直接构造若干 Node 模拟检索结果交给合成器。下面是核心代码：

```python
# 此处使用内置的 LlamaDebugHandler 处理器进行跟踪 (也可以使用 Langfuse 平台跟踪)
llama_debug = LlamaDebugHandler(print_trace_on_end=True)
callback_manager = CallbackManager([llama_debug])
Settings.callback_manager = callback_manager

# 构造 refine 响应生成器
response_synthesizer = get_response_synthesizer(
    response_mode="refine")

# 模拟检索出的 3 个 Node
nodes = [NodeWithScore(node=Node(text="小麦手机是一款专为满足现代生活需求而设计的智能手机。它的设计简洁大方，线条流畅，给人一种优雅的感觉"),
score=1.0),
    NodeWithScore(node=Node(text="小麦手机采用了最新的处理器技术，运行速度快，性能稳定，无论是玩游戏、看电影还是处理工作，都能轻松应对"),
score=1.0),
    NodeWithScore(node=Node(text="小麦手机还配备了高清大屏，色彩鲜艳，画面清晰，无论是阅读、浏览网页还是观看视频，都能带来极佳的视觉体验"),
score=1.0)
]

# 把问题和 Node 交给响应生成器响应生成
```

```python
response = response_synthesizer.synthesize(
    "介绍一下小麦手机的优点，用中文回答",
    nodes=nodes
)

print(response)
```

代码逻辑很直观。再用一个工具函数打印每次大模型调用的消息：

```python
def print_events_llm():
    events = llama_debug.get_event_pairs('llm')

    # 发生了多少次大模型调用
    print(f'Number of LLM calls: {len(events)}')

    # 依次打印所有大模型调用的消息
    for i, event in enumerate(events):
        print(f'\n==========LLM call {i+1} messages==========')
        pprint.pprint(event[1].payload["messages"])
        print(f'\n==========LLM call {i+1} response==========')

    pprint.pprint(event[1].payload["response"].message.content)

print_events_llm()
```

上述样例的输出结果如图 7-7 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_213_0.png)

图 7-7

从输出可以看出 refine 的特点。

（1）共发生了 3 次大模型调用，与前面所述一致，refine 至少需要 N 次迭代，N 为输入 Node 数。
（2）第一次调用使用的 Prompt 模板与后续不同，因为后续调用需要把上一次生成的答案带入，而第一次没有前序答案。

研究默认 Prompt 模板可知，refine 模式在后续调用中使用的模板如图 7-8 所示，为 chat 模型。`{existing_answer}` 代表上一次生成的答案。大模型被要求：要么基于新 Node 的上下文对已有答案重写补充，要么在新上下文无用时保留原答案不变。

```python
"You are an expert Q&A system that strictly operates in two modes "
"when refining existing answers:\n"
"1. **Rewrite** an original answer using the new context.\n"
"2. **Repeat** the original answer if the new context isn't useful.\n"
"Never reference the original answer or context directly in your answer.\n"
"When in doubt, just repeat the original answer.\n"
"New Context: {context_msg}\n"
"Query: {query_str}\n"
"Original Answer: {existing_answer}\n"
"New Answer: "
```

图 7-8

#### 2. compact 模式

compact 模式是系统默认采用的响应合成策略。它的核心思路是先把从多个 Node 检索到的文本片段拼成一段更长的整合块，让上下文窗口得到更充分的利用，再基于这段整合内容走 refine 流程完成生成。通俗地说，就是先合并一次再精炼。实际上，compact 对应的生成器类型是直接继承自 refine 生成器的实现。

![](img/7eddb68cd925518fdc310e52e94ba9a9_215_0.png)

图 7-9

合并之后，compact 通常能显著减少大模型调用次数，但不保证只有一次调用。如果整合后的上下文仍会超出窗口上限，系统就会继续沿用 refine 的迭代生成机制。这样做既省性能也省成本。需要注意的缺点是，一次性带入的上下文过长，容易让最终输出相比纯 refine 模式显得不够完整、细节偏少。

我们只把 refine 案例中的响应模式改成 compact 重新跑一下：

```python
response_synthesizer = get_response_synthesizer(response_mode="compact")
```

继续用之前的调试代码观察大模型输入输出的变化，可见图 7-10：

```python
print_events_llm()
```

![](img/7eddb68cd925518fdc310e52e94ba9a9_215_1.png)

图 7-10

从 user 消息里能很清晰地看到，原来分散在多个 Node 的文本块被聚合成一个大块带入上下文，所以最终只需要一次大模型调用就能完成。

#### 3. tree_summarize 模式

这是比较常见的一种用于总结类问题的合成模式。假设检索到 N 个相关 Node，其执行流程大致如下。

- （1）把这 N 个 Node 合并，使其能够塞进最大上下文窗口。
- （2）合并后若只剩 1 个 Node，就直接用该 Node 的内容调用大模型生成唯一答案，流程结束。
- （3）若合并后仍有多个 Node，则并行调用大模型对这些 Node 分别生成多个中间答案。
- （4）把这些中间答案重新组装成新的 Node，再次执行合并到查询的循环，直到最终只剩一个答案即满足第（2）步。

整体处理像一棵树在不断收敛，因此得名 tree_summarize。

![](img/7eddb68cd925518fdc310e52e94ba9a9_216_0.png)

图 7-11

它的特点是能够对所有检索到的 Node 进行反复合并与递归式总结，最终收敛到一个答案，更适合处理需要综合归纳的输入问题。为了让效果更明显，不用手工造几个小 Node 这种会被直接合并的情况，而是用一段较长的文档来模拟 N 个检索结果：

```python
#读取文档
reader = SimpleDirectoryReader(
    input_files=["../../data/AI-survey-cn.pdf"]
)
docs = reader.load_data()

#分割成 Node
splitter = TokenTextSplitter(
    chunk_size=500,
    chunk_overlap=0,
    separator="\n",
)
nodes = splitter.get_nodes_from_documents(docs)

#模拟检索出的多个 Node，注意不能直接用上面的 Node
node_scores = [NodeWithScore(node=node, score=1.0) for node in nodes]

#调用响应生成器，输入问题与模拟检索出的 Node
response_synthesizer = get_response_synthesizer(response_mode="tree_summarize")
response = response_synthesizer.synthesize(
    "请使用中文，文中介绍了 AI Agent 哪些方面的内容",
    nodes=node_scores)
print(response)
```

从调试日志可以看到总共触发了 7 次大模型调用，如图 7-12 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_217_0.png)

图 7-12

再看最后一次调用的输入信息中 role=user 部分，会发现里面携带的都是前面几轮生成的中间答案，如图 7-13 所示。这正是 tree_summarize 不断迭代、逐步收敛到唯一答案的体现。

![](img/7eddb68cd925518fdc310e52e94ba9a9_218_0.png)

图 7-13

## 4. 更多的响应生成模式

除了前面提到的几种常用且相对复杂的模式，框架还内置了一些其它模式。了解它们的原理有助于在不同场景下灵活选用，即便换到其它 RAG 框架也能自行复现。

### 1）simple_summarize 模式

该模式会把检索到的 Node 内容合并以适配上下文窗口，超出部分直接截断丢弃，然后只做一次大模型调用生成结果。优点是速度快、实现简单，缺点是可能丢失有用信息。

值得注意的是，合并时并不是优先保留排名靠前的 Node、直接丢弃后面的，而是先整体计算，若需要截断就对每个 Node 的尾部溢出部分进行裁剪，如图 7-14 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_218_1.png)

图 7-14

### 2）accumulate 模式

该模式会对每一个检索到的 Node 单独调用大模型生成答案，再用分隔符把多个答案简单拼接输出。适用于需要对每个输入 Node 都产出结果并直接汇总、而不需要二次总结的场景。我们把之前的 refine 例子改成 accumulate 模式，输出如图 7-15 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_219_0.png)

可以看到，3 个 Node 分别触发了大模型调用，最后的结果就是把 3 个答案按默认的横线分隔符连起来输出。

### 3）compact_accumulate 模式

本质上是先合并再 accumulate，先做一次 Node 内容的打包合并，随后用 accumulate 方式生成。如果把例子改成该模式运行，结果如图 7-16 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_219_1.png)

这时只剩下一次大模型调用，因为多个 Node 被合并后一次性生成，大幅降低了调用次数。

下面还有两种作用比较特殊的模式。

### 1）no_text 模式

该模式不会产生真实的大模型响应，仅用于拿到检索得到的 Node 列表信息。

### 2）generation 模式

该模式直接让大模型回答输入问题，不带任何检索上下文。

## 7.2.3 响应生成器的参数

通过底层 API 构造响应生成器时，通常使用 get_response_synthesizer 方法，除了最基本的 response_mode，还会配置以下常见参数：

1. **llm**：使用的具体大模型。可在构造时显式指定，不指定则从全局 Settings 组件获取默认模型。

2. **template**：Prompt 模板，用于组装发给大模型的 Prompt。常见模板包括：
    1. text_qa_template：基础问答模板，用于 refine、compact、simple_summarize、accumulate、compact_accumulate 模式。
    2. refine_template：用于 refine 和 compact 模式。
    3. summary_template：用于 tree_summarize 模式。
    4. simple_template：用于不带检索上下文的 generation 模式。

Prompt 模板的自定义可参考第 4 章。以 tree_summarize 为例，传入自定义的 summary_template 参数示例：

```python
#这里给 Prompt 模板增加一个 language_name 参数
qa_prompt_tmpl = (
    "根据以下上下文信息：\n"
    "---------------------\n"
    "{context_str}\n"
    "---------------------\n"
    "使用{language_name}回答以下问题\n "
    "问题：{query_str}\n"
    "答案："
)
qa_prompt = PromptTemplate(qa_prompt_tmpl)

response_synthesizer = get_response_synthesizer(
    response_mode="tree_summarize",
    streaming=True,
    summary_template=qa_prompt)

......

#响应生成时，传入 language_name 参数
streaming_response = response_synthesizer.synthesize(
    "介绍一下小麦手机的优点",
    nodes=nodes,
    language_name="法语"
)
```

3. **streaming**：是否开启流式输出。流式体验更好但控制更复杂。设为 True 后生成器会从第一个 token 开始即时输出，不必等完整结果结束。在一次合成过程多次调用大模型时，只有最后一次调用会走流式。常见流式处理代码：

```python
for text in streaming_response.response_gen:
    #自行处理每个 text 变量的输出
    ......
```

或直接使用 streaming_response.print_response_stream 方法将结果打印到控制台。

4. **output_cls**：可指定结构化输出类型，要求生成器遵循特定格式输出。比如：

```python
class Phone(BaseModel):
    name: str
    description: str
    features: List[str]

response_synthesizer = get_response_synthesizer(
    response_mode="tree_summarize",
    summary_template=qa_prompt,
    output_cls=Phone)

......
streaming_response = response_synthesizer.synthesize(
    "介绍一下小麦手机",
    nodes=nodes,
    language_name="英文"
)
print(streaming_response)
```

最终结果会尝试借助大模型转换为符合类型定义的结构化内容，受模型不确定性影响仍可能失败，如图 7-17 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_222_0.png)

5. **structured_answer_filtering**：是否过滤与问题不相关的 Node 答案。该参数主要用于 refine 或 compact 模式，目前主要面向 OpenAI 的 GPT 系列模型。

## 7.2.4 实现自定义的响应生成器

可以自行派生 BaseSynthesizer 实现自定义响应生成器，并接入后续的查询引擎使用。自定义生成器需要实现必要的接口，示例代码如下：

```python
class FunnySynthesizer(BaseSynthesizer):

    my_prompt_tmpl = (
        "根据以下上下文信息：\n"
        "------------------------\n"
        "{context_str}\n"
        "------------------------\n"
        "使用中文且幽默风趣的风格回答以下问题\n "
        "问题: {query_str}\n"
        "答案: "
    )

    def __init__(
        self,
        llm: Optional[LLMPredictorType] = None,
    ) -> None:
        super().__init__(
            llm=llm
        )
        self._input_prompt = PromptTemplate(FunnySynthesizer.my_prompt_tmpl)

    #必须实现的接口
    def _get_prompts(self) -> PromptDictType:
        pass

    #必须实现的接口
    def _update_prompts(self, prompts: PromptDictType) -> None:
        pass

    #生成响应的接口
    def get_response(
        self,
        query_str: str,
        text_chunks: Sequence[str],
        **response_kwargs: Any,
    ) -> RESPONSE_TEXT_TYPE:

        context_str = "\n\n".join(n for n in text_chunks)

        #此处可以自定义任何响应逻辑
        response = self._llm.predict(
            self._input_prompt,
            query_str=query_str,
            context_str=context_str,
            **response_kwargs,
        )
        return response

    #响应接口的异步版本
    async def aget_response(
        self,
        query_str: str,
        text_chunks: Sequence[str],
        **response_kwargs: Any,
    ) -> RESPONSE_TEXT_TYPE:

        context_str = "\n\n".join(n for n in text_chunks)
        response = await self._llm.apredict(
            self._input_prompt,
            query_str=query_str,
            context_str=context_str,
            **response_kwargs,
        )
        return response

#使用自定义的响应生成器
response_synthesizer = FunnySynthesizer(llm=llm)
```

上述示例实现了一个简单的“幽默型”响应生成器，可在其中嵌入自己的 RAG 逻辑。

## 7.3 RAG引擎：查询引擎

在理解检索器与响应生成器及各合成模式后，就具备了搭建上层 RAG 引擎的基础。

查询引擎 Query Engine 是通过自然语言接口一次性获得响应内容的 RAG 引擎，广泛应用于自然语言搜索、问答、数据查询分析等场景。使用者可以是人，也可以是其它应用系统。本节将由简到繁介绍查询引擎的用法及其内部原理。

### 7.3.1 构造内置类型的查询引擎的两种方法

构造内置查询引擎主要有两种方式：使用高层 API 快速构造，或使用底层 API 组合构造。

#### 1. 使用高层 API 快速构造查询引擎

之前的示例为了演示效果，都采用了一行代码快速构造查询引擎的方式。只需一行，即可基于索引生成查询引擎。下面示例演示如何用一个向量索引快速构造查询引擎：

```python
......
#用向量索引构造查询引擎
query_engine = vector_index.as_query_engine()
response = query_engine.query('客户在没有交定金之前要求出具房地产证原件，怎么办？')
print(response)
```

想要获得流式响应时，直接在参数里加上 streaming 即可：

```python
......
#query_engine
query_engine = vector_index.as_query_engine(streaming=True)
response = query_engine.query('客户在没有交定金之前要求出具房地产证原件，怎么办？')
response.print_response_stream()
```

#### 2. 通过底层 API 手动拼装查询引擎

前面介绍的是借助高层 API 快速搭建查询引擎的思路，优点是上手快、代码少，但可定制空间会受限。另一种做法是调用底层 API 分步显式地组装查询引擎，这样能获得更精细的调参能力。核心思路就是把原本的一行 `query_engine = vector_index.as_query_engine()` 拆成若干步骤展开。下面这段实现与直接调用 `as_query_engine` 方法的效果完全等价：

```python
......
#以下代码等价于 query_engine = vector_index.as_query_engine()

#构造检索器
retriever = VectorIndexRetriever(
    index=vector_index,
    similarity_top_k=2,
)

#构造响应生成器
response_synthesizer = get_response_synthesizer(
    streaming = True #如果需要使用流式输出
)

#组合构造查询引擎
query_engine = RetrieverQueryEngine(
    retriever=retriever,
    response_synthesizer=response_synthesizer,
)
......
```

两种方式最终得到的效果一致，差异在于可配置度。后一种手工组装的方式允许你替换响应生成器为自定义类型等更灵活的调整。

引擎跑起来后，可以用 Langfuse 后台去追踪内部执行细节，尤其是喂给大模型的 Prompt 以及检索到的 Node 内容，这对评估检索命中率和生成质量、指导后续优化很有帮助，如图 7-18 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_226_0.png)

图 7-18

### 7.3.2 查询引擎内部构造与运行机制解析

弄清楚查询引擎的结构和原理，有助于更好地阅读前面的代码并做针对性优化。可以从索引类里的 `as_query_engine` 实现入手，看它背后到底做了什么，下面是该方法的一段核心源码：

```python
·····索引类中的 as_query_engine·····

def as_query_engine(
    self, llm: Optional[LLMType] = None, **kwargs: Any
) -> BaseQueryEngine:
    # NOTE: lazy import
    from llama_index.core.query_engine.retriever_query_engine
    import (
        RetrieverQueryEngine,
    )

    retriever = self.as_retriever(**kwargs)
    llm = (
        resolve_llm(llm,
        callback_manager=self._callback_manager)
        if llm
        else llm_from_settings_or_context(Settings,
        self.service_context)
    )

    return RetrieverQueryEngine.from_args(
        retriever,
        llm=llm,
        **kwargs,
    )
```

可以看出，这其实和用底层 API 手工组装是同一回事，`from_args` 内部先创建响应生成器再组装查询引擎。代码也明确了查询引擎主要依赖的两大组件。

1. **retriever**：检索器。RAG 的基础就是检索，检索器的任务是利用前面构建的索引——不一定是向量索引——从中召回与用户问题相关的上下文 Node 列表。

(2) llm：大模型。检索器拿到相关知识后，框架会用 Prompt 模板把知识和原问题拼起来，再交给大模型生成最终答案。

完整的答案生成还涉及响应生成模式的处理流程、Prompt 模板设计、输出解析等辅助环节。以最常见的向量索引查询引擎 `RetrieverQueryEngine` 为例，其内部构成及关联组件如图 7-19 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_228_0.png)

从图 7-19 可见，与查询引擎密切相关的关键部件有以下三项。

- (1) VectorIndexRetriever：向量索引检索器。基于索引完成知识检索，输出一组相关的 Node。

- (2) Synthesizer：响应生成器。负责 Prompt 的组装，并按响应的生成模式要求产出结果。

- (3) NodePostProcessor：节点后处理器。一般在检索完成后对 Node 列表做额外处理，比如重排。

因此，如果不走 `as_query_engine` 这类高层接口，也完全可以先用底层 API 分别构造上述组件，再组合成查询引擎。

## 7.3.3 自研查询引擎

内置的查询引擎在很多业务场景下并不够用。例如你想用一个微调过的本地大模型，配合向量索引实现 C-RAG、Self-RAG 等更复杂的检索生成流程，这时就可以继承 `CustomQueryEngine` 并实现 `custom_query` 接口，自行使用自定义查询引擎，调用方式与内置类型保持一致。

下面给出一个简易的自定义查询引擎演示。搭建一个查询引擎必备的是一个基于大模型的响应生成器和一个检索器，示例正是基于这两个组件来构造：

```python
class MyQueryEngine(CustomQueryEngine):
    response_synthesizer: BaseSynthesizer = Field(default=None,
              description="response_synthesizer")
    retriever: BaseRetriever = Field(default=None, description="retriever")

    def __init__(self, retriever: BaseRetriever,
                 response_synthesizer: BaseSynthesizer):
        super().__init__()
        self.retriever = retriever
        self.response_synthesizer = response_synthesizer

    # 实现必需的 custom_query 接口
    def custom_query(self, query_str: str):
        nodes = self.retriever.retrieve(query_str)
        response = self.response_synthesizer.synthesize(query_str, nodes)
        return response
```

该实现里完成了 `custom_query` 的必填逻辑，先用检索器拿到与问题相关的 Node 列表，再把 Node 与原问题一起交给响应生成器得到结果。

调用方式如下：

```python
# 先构造 vector_index 对象
retriever = vector_index.as_retriever(similarity_top_k=3)
synthesizer = get_response_synthesizer(llm=llm, streaming=True)

# 构造自定义的查询引擎
my_query_engine = MyQueryEngine(retriever, synthesizer)
response = my_query_engine.query('你的问题')
```

如果业务需要对生成过程做完全掌控，也可以直接使用底层大模型组件来搭建自定义查询引擎，从而更自由地控制生成细节：

```python
qa_prompt = PromptTemplate(
    "根据以下上下文回答输入问题: \n"
    "-------------------\n"
    "{context_str}\n"
    "-------------------\n"
    "回答以下问题，不要编造\n"
    "我的问题: {query_str}\n"
    "答案: "
)

class MyLLMQueryEngine(CustomQueryEngine):
    # 此处直接使用大模型组件，而不是响应生成器
    llm: Ollama = Field(default=None, description="llm")
    retriever: BaseRetriever = Field(default=None, description="retriever")

    def __init__(self, retriever: BaseRetriever, llm: Ollama):
        super().__init__()
        self.retriever = retriever
        self.llm = llm

    def custom_query(self, query_str: str):
        nodes = self.retriever.retrieve(query_str)

        # 用检索出的 Node 构造上下文
        context_str = "\n\n".join([n.node.get_content() for n in nodes])

        # 用上下文与查询问题组装 Prompt, 然后调用大模型组件响应生成
        response = self.llm.complete(
            qa_prompt.format(context_str=context_str,
                             query_str=query_str)
        )
        return str(response)
```

这个例子里，检索 Node、组织上下文、组装 Prompt 到调用大模型生成，全部由底层大模型组件完成。实际项目中可按需设计任意个性化的生成逻辑。

## 7.4 RAG 引擎：对话引擎

查询引擎的典型用法是每次针对知识库提出独立问题并直接作答，不保留历史。另一种常见场景是需要多轮连续对话来完成任务，比如客户的连环产品咨询。这类场景必须追踪过往对话上下文，才能更好地理解当前提问。由于大模型本身无状态，多轮对话靠携带历史记录实现。在 RAG 场景下，实现带上下文的连续检索增强对话会遇到几个难题：

- （1）历史对话在客户端的透明保存、加载与传递。
- （2）检索时如何基于上下文完成知识召回。
- （3）召回知识后选用何种响应生成模式来输出。

与查询引擎对应的就是对话/聊天引擎 `Chat Engine`，它本质上是有状态的查询引擎，部分对话引擎直接建立在查询引擎之上。

### 7.4.1 对话引擎的两种搭建方式

对话引擎的构建思路与查询引擎类似，既可以用索引快速生成，也可以用底层 API 精细组装。

#### 1. 基于索引一键创建对话引擎

与查询引擎相仿，索引也可以通过一行代码快速得到对话引擎：

```python
chat_engine = vector_index.as_chat_engine(chat_mode="condense_question")
print(chat_engine.chat('文心一言是什么？'))
```

不同于查询引擎，对话引擎支持会话 `Session` 概念，因此需要一个重置会话的手段。`reset` 接口即可实现复位：

```python
chat_engine.reset()
```

对话引擎还提供了便捷的方式进入多轮交互式会话：

```python
chat_engine.chat_repl()
```

#### 2. 用底层 API 精确组装对话引擎

若要更精确地控制构造过程，就需要使用底层 API 组合。不同模式的查询引擎靠传入不同的响应生成器区分，而不同模式的对话引擎则通过构造不同类型的对话引擎组件来区分。查询引擎的核心依赖是检索器和响应生成器，对话引擎则通常在此基础上增加记忆等能力。

下面演示如何搭建一种 `condense` 类型的对话引擎：

```python
custom_prompt = PromptTemplate(
    """\n请根据以下的历史对话记录和新的输入问题，重写一个新的问题，使其能够捕捉对话中的所有相关上下文。
<Chat History>
{chat_history}
<Follow Up Message>
{question}
<Standalone question>
"""
)

# 历史对话记录
custom_chat_history = [
    ChatMessage(
        role=MessageRole.USER,
        content="我们来讨论关于文心一言的一些问题吧",
    ),
    ChatMessage(role=MessageRole.ASSISTANT, content="好的"),
]

# 先构造查询引擎，这里省略了构造 vector_index 对象
query_engine = vector_index.as_query_engine()

# 再构造对话引擎
chat_engine = CondenseQuestionChatEngine.from_defaults(
    query_engine=query_engine,       # 对话引擎基于查询引擎构造
    condense_question_prompt=custom_prompt,  # 设置重写问题的 Prompt 模板
    chat_history=custom_chat_history,        # 携带历史对话记录
    verbose=True,
)

chat_engine.chat_repl()
```

可以看到，这里的组装方式与手工搭建查询引擎不同，这里创建的是 `condense_question` 模式的对话引擎。它会读取历史对话，把最新用户问题改写成语义更完整的表达，再输入查询引擎求解。因此该类型依赖的部件包括查询引擎、问题改写用的 Prompt 模板以及历史对话记录，即 `from_defaults` 的参数。

运行效果如图 7-20 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_233_0.png)

图 7-20

注意第二个提问“它的主要场景是什么？”本身依赖上下文。对话引擎将其改写为“‘文心一言’的主要应用场景有哪些”，语义更完整后交给查询引擎就能得到准确答案。

### 7.4.2 对话引擎内部结构与运行原理

前面例子显示，对话引擎在实现上是基于查询引擎构建的。深入理解其内部结构与运行机制，有助于选型和优化。从索引组件的 `as_chat_engine` 方法切入，下面仅展示核心部分来解析其内部构造与运行流程：

```python
def as_chat_engine(
    self,
    chat_mode: ChatMode = ChatMode.BEST,
    llm: Optional[LLMType] = None,
    **kwargs: Any,
) -> BaseChatEngine:
    ......
    # 先构造查询引擎
    query_engine = self.as_query_engine(llm=llm, **kwargs)

    # 再构造对话引擎
    if chat_mode in [ChatMode.REACT, ChatMode.OPENAI, ChatMode.BEST]:
        ......
        query_engine_tool = QueryEngineTool.from_defaults(query_engine=query_engine)

        return AgentRunner.from_llm(
            tools=[query_engine_tool],
            llm=llm,
            **kwargs,
        )
    elif chat_mode == ChatMode.CONDENSE_QUESTION:
        return CondenseQuestionChatEngine.from_defaults(
            query_engine=query_engine,
            llm=llm,
            **kwargs,
        )
    elif chat_mode == ChatMode.CONTEXT:
        return ContextChatEngine.from_defaults(
            retriever=self.as_retriever(**kwargs),
            llm=llm,
            **kwargs,
        )
    elif chat_mode == ChatMode.CONDENSE_PLUS_CONTEXT:
        return CondensePlusContextChatEngine.from_defaults(
            retriever=self.as_retriever(**kwargs),
            llm=llm,
            **kwargs,
        )
    elif chat_mode == ChatMode.SIMPLE:
        return SimpleChatEngine.from_defaults(
            llm=llm,
            **kwargs,
        )
    else:
        raise ValueError(f"Unknown chat mode: {chat_mode}")
```
示例代码把 chat_mode 当作开关，直观展示了如何拼装出不同的对话引擎。先行创建 query_engine 这一步，侧面印证了多数对话引擎在内部都会调用查询能力。

从各类型引擎的初始化参数就能反推出它们依赖了什么。SIMPLE 仅接收 llm，说明它完全不使用检索器或查询引擎，就是一场纯粹的大模型直连对话；CONTEXT 则同时传入 retriever 和 llm，表明它会先检索上下文再生成答案。

REACT、OPENAI、BEST 这三种对话引擎都是基于 AgentRunner 构建的。AgentRunner 是 LlamaIndex 搭建智能体的核心载体，所以这些引擎本质上是让智能体来调度查询引擎做推理。智能体除了大模型，最关键的就是工具，因此 query_engine 被封装成 QueryEngineTool 形式作为输入。

RAG 方式开发智能体的完整做法将在第 9 章展开。

LlamaIndex 中各类对话引擎与周边组件的协作关系如图 7-21 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_236_0.png)
图 7-21

总体来看，支撑对话引擎运行的基础设施主要分三类。

- （1）LLM：大模型。它的作用不局限于最终输出。比如 Agent 类型的引擎里，模型要依据历史记录和任务规划工具调用；在 Condense 类型的引擎里，模型要先根据历史和当前问题把问题改写成独立表达。
- （2）Query Engine 或者 Retriever：查询引擎或检索器。查询引擎本身已把检索器和多种响应生成策略打包，所以仅用检索器的引擎更轻，而依赖查询引擎的引擎能提供更丰富的底层生成模式。
- (3) Memory: 记忆体。这是对话引擎区别于查询引擎的明显特征。对话是有状态的服务，需要保存历史问答来维持上下文，Memory 组件就是负责这件事的。

### 7.4.3 不同对话模式怎么选
下面具体拆解各对话模式下引擎的类型、运行方式和参数。

#### 1. 模式、引擎与依赖组件对照
LlamaIndex 支持的对话模式、对应引擎类型及主要依赖见表 7-7。

表 7-7

| 对话模式 | 引擎类型 | 依赖的主要组件 |
| --- | --- | --- |
| simple | SimpleChatEngine | LLM |
| condense_question | CondenseQuestionChatEngine | QueryEngine,LLM |
| context | ContextChatEngine | Retriever,LLM |
| condense_plus_context | CondensePlusContextChatEngine | Retriever,LLM |
| react | ReActAgent | [Tool],LLM |
| openai | OpenAIAgent | [Tool],LLM |
| best | ReActAgent 或 OpenAIAgent | [Tool],LLM |

#### 2. simple 模式：纯大模型对话
simple 模式下用户直接和大模型聊天，不会调用查询引擎或检索器，所以不会去拉取知识上下文。也就意味着用 simple 模式不需要提前建索引：

```python
llm = Ollama(model='qwen:14b')
Settings.llm=llm
```
chat_engine = SimpleChatEngine.from_defaults()

chat_engine.chat_repl()
![](img/7eddb68cd925518fdc310e52e94ba9a9_238_0.png)
这样建出来的 chat_engine 使用全局默认大模型即可对话，结果如图 7-22 所示。

你可以连续进行有上下文的对话，而不只是单次问答。

## 3. condense_question 对话模式
此模式会先基于历史对话把当前输入改写成语义完整、独立的问题，再交给查询引擎去找答案。前面介绍对话引擎时就用过这种方式：

```
......
#构造查询引擎
query_engine = vector_index.as_query_engine()

#构造对话引擎
chat_engine = CondenseQuestionChatEngine.from_defaults(
    query_engine=query_engine,
    condense_question_prompt=custom_prompt,
    chat_history=custom_chat_history,
    verbose=True,
)
```
示例里还传入了自定义的问题重写 Prompt 模板，以及初始的历史记录。你也可以极简地快速创建：

```
......
chat_engine = CondenseQuestionChatEngine.from_defaults(
    query_engine=vector_index.as_query_engine(),
    verbose=True,
)
```
两次连续对话的效果如图 7-23 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_239_0.png)
借助 Langfuse 观察内部流程，第二个问题“有啥应用场景”的生成过程可以看到出现了两次大模型调用，见图 7-24。

![](img/7eddb68cd925518fdc310e52e94ba9a9_239_1.png)
# 查看第一次大模型调用的细节，如图 7-25 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_240_0.png)
图 7-25

这一次调用的任务是把当前输入的问题改写得更独立完整。第二次大模型调用的细节如图 7-26 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_240_1.png)
图 7-26

这一步是基于检索到的上下文去回答改写后的问题。这就验证了 condense_question 的工作流是先改写问题，再用查询引擎生成响应。

由于依赖查询引擎，也可以灵活指定不同的响应生成器，例如：

```
chat_engine = CondenseQuestionChatEngine.from_defaults(
    query_engine=vector_index.as_query_engine(response_mode="refine"),
    verbose=True,
)
```
condense_question 最大的优势是每次检索前都用历史记忆完善当前问题的语义，大幅提升召回相关性。因为在多轮对话里，单轮问题往往语义不全。这种模式很适合 RAG 场景下的连续对话。

代价是会增加大模型调用次数，不仅要重写问题，采用复杂生成策略的查询引擎本身也可能产生多次调用。

## 4. context 对话模式
context 模式会先用检索器从知识库找出相关片段，注入到 system 提示词里，再让大模型基于这些片段作答。这样模型能直接利用检索结果生成。

构造一个 context 模式的引擎，注意其输入参数：

```
# 也可以修改为
chat_engine=vector_index.as_chat_engine(chat_mode="context")
chat_engine = ContextChatEngine.from_defaults(
    retriever=vector_index.as_retriever(),
    llm=llm
)
chat_engine.chat_repl()
```
用前面相同的问题测试，对话一次的调用链是：先做一次知识检索，随后完成响应生成，如图 7-27 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_242_0.png)
图 7-27

查看生成阶段的输入输出细节，如图 7-28 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_242_1.png)
图 7-28

可以看出 system 提示里包含了检索出的上下文，模型据此给出了正确回答。

context 模式过程简洁、响应快，不走查询引擎的复杂生成环节。缺点是直接用当前问题检索，在多轮对话中问题语义不完整时容易召回无关内容，质量下降。

## 5. condense_plus_context 对话模式
名字已经点明，它是 condense_question 与 context 的融合：先结合历史把问题改写得更完整，再用检索器召回与新问题相关的上下文，最后交给大模型生成。下面构造这样一个引擎：

```
chat_engine = CondensePlusContextChatEngine.from_defaults(
    retriever=vector_index.as_retriever(similarity_top_k=1),
    llm=llm
)
```
采用与 condense_question 类似的问题进行测试，观察内部运行如图 7-29 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_243_0.png)
仍看第二个问题“有哪些场合可以使用它”的生成过程，如图 7-30 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_243_1.png)
这次处理共进行了两次大模型调用和一次向量检索。图 7-31 显示第一次大模型调用。

![](img/7eddb68cd925518fdc310e52e94ba9a9_244_0.png)
图 7-31

从 Prompt 可见，这是一次问题重写调用，输出了新的独立问题。

图 7-32 显示向量检索过程。

![](img/7eddb68cd925518fdc310e52e94ba9a9_244_1.png)
图 7-32

此时检索输入的是重写后的新问题，检索器输出了相关的 Node 信息。

图 7-33 显示第二次大模型调用的过程。

这里截取了 system 提示。可以看到上一步检索到的上下文被注入 Prompt，再交给大模型生成。

condense_plus_context 的特点是兼顾两者的优点：通过改写提升召回精度，同时保持生成路径简洁，不使用复杂的查询引擎生成。相应地，在响应生成模式上的灵活性会降低。

![](img/7eddb68cd925518fdc310e52e94ba9a9_245_0.png)
图 7-33

## 6. Agent 对话模式
react、openai、best 三种模式都归为 Agent 对话模式。它们本质上都是创建 Agent，把查询引擎作为工具交给 Agent，由 Agent 根据当前问题和历史记录自行规划并调用工具输出答案。三者的区别仅在于模型支持：若模型为 OpenAI 或支持函数调用，则创建 OpenAIAgent，否则为 ReActAgent。

下面构造一个 react 模式的对话引擎：

```
chat_engine = vector_index.as_chat_engine(chat_mode="react")
chat_engine.chat_repl()
```
如果你想更精细地控制构造过程，可以这样做，因为 react 模式底层是 ReActAgent，支持对工具的更多描述信息，帮助模型更好地推理调用方式。

```
......
#构造查询引擎
query_engine = vector_index.as_query_engine()

#把查询引擎"工具化"
query_engine_tool = QueryEngineTool.from_defaults(
    query_engine=query_engine,
    name="query_engine",
    description="用于查询文心一言的相关信息"
)

#将工具传入，开发一个 Agent
chat_engine = ReActAgent.from_tools(
    tools=[query_engine_tool]
)
chat_engine.chat_repl()
```
简单测试并观察内部运行，如图 7-34 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_246_0.png)
进入 Langfuse 控制台查看追踪信息，运行过程如图 7-35 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_246_1.png)
下面举一个相对复杂的 Agent 推理流程。

(1) 先调用大模型推理出下一步动作。打开详情可以看到如图 7-36 所示的推理结果。模型判断需要使用工具查询问题。

![](img/7eddb68cd925518fdc310e52e94ba9a9_247_0.png)
图 7-36

(2) 首轮推断完成后会得到 Action Input，也就是行动输入，用它去激活工具。工具指前面已搭建好的查询引擎。引擎在响应生成阶段默认采用 compact 模式，内部先执行检索获取相关知识，再发起第二次 LLM 调用，随后把模型输出作为结果返回。可以注意到模型调用时把检索得到的上下文一并带入，如图 7-37 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_247_1.png)

图 7-37

(3) 工具调用结束后，模型会基于返回结果再次推断。此时模型认为已有足够信息可以直接作答，于是结束流程并给出最终答案，如图 7-38 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_248_0.png)

图 7-38

总体来看，Agent 对话模式的对话引擎运行路径较为繁复。它的核心优势在于具备自我规划并调用外部能力，你可以把多种能力交给 Agent 统一调度，例如在不同知识库中查找信息的组件，并且这些能力可以自定义。所以这类引擎不仅能完成知识检索，还能承担更复杂的任务，比如按要求发送一封邮件。

当然，Agent 对话模式的代价是流程更繁琐、响应延迟更明显，而且很大程度上依赖 LLM 自身的推断水平，因此存在不确定性。

# 7.5 结构化输出

在 RAG 链路的多个环节，都需要 LLM 给出规范化的结果以便后续处理。例如：

1. 使用 TreeIndex 索引检索时，需要模型对树节点作出选择，并以 “ANSWER: (数字)” 的格式返回。
2. 在响应生成阶段，希望得到 JSON 格式的数据，方便下游解析与使用。

下面介绍两种常用的规范化输出处理手段。

1. 使用 output_cls 参数：通过提示词与自定义的 Pydantic 模型，要求模型按结构输出。
2. 使用输出解析器：在 llm 模块中加入解析器，对模型输出进行解析与结构化。

## 7.5.1 使用 output_cls 参数

若查询引擎的输出需要规范化，只需传入自定义的输出类型，就能直接拿到 Pydantic 对象，而无需额外再调用一次模型。

在先前搭建的简单查询引擎上做少量改动：

```
......准备数据与索引......
class Phone(BaseModel):
    """ Information & features of a phone."""

    cpu: str
    memory: str
    storage: str
    screen: str

query_engine = index.as_query_engine(
    llm=Ollama(model='llama3:8b'),
    response_mode="tree_summarize",
    output_cls=Phone
)
response = query_engine.query("小麦手机的主要参数是什么？")
......
```

调用 as_query_engine 方法时指定 output_cls，就能要求引擎在生成响应时做结构化输出。我们在 Langfuse 平台上追踪内部信息，可以看到 system 提示发生了变化，如图 7-39 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_249_0.png)

图 7-39

背后的原理是：当在引擎参数中指定 output_cls 时，调用模型生成响应时会自动化在 system 提示中插入结构化输出的指令，要求模型的输出先遵循 output_cls 的格式要求，随后把结果转换为对应类型的对象返回，如图 7-40 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_250_0.png)

图 7-40

如果模型输出无法通过 Pydantic 对象的类型校验，转换会失败并抛出异常，也就是模型没有遵守指令。

实际使用中，这种方式对所用模型指令遵循能力的依赖较大，模型的服从程度决定了能否按要求输出结构化结果。所以测试时若出现异常，通常需要更换模型，在必要时还需调整 Prompt 模板。

## 7.5.2 使用输出解析器

LlamaIndex 框架支持接入其他框架提供的输出解析器。可以借助这些解析器对模型输出进行解析与结构化。下面以 LangChain 框架的输出解析器为例，说明如何限制模型的生成。核心代码如下：

```
......
#定义响应的格式
response_schemas = [
    ResponseSchema(
        name="name",
        description="手机名称",
    ),
    ResponseSchema(
        name="cpu",
        description="手机处理器",
    ),
    ResponseSchema(
        name="memory",
        description="手机内存",
    ),
    ResponseSchema(
        name="features",
        description="手机特性",
        type="list",
    ),
]

#构造 LangChain 框架的输出解析器
lc_output_parser = StructuredOutputParser.from_response_schemas(response_schemas)
output_parser = LangchainOutputParser(lc_output_parser)

#设置大模型使用构造的输出解析器
llm = OpenAI(output_parser=output_parser)

#查询
query_engine = index.as_query_engine(llm=llm, verbose=True)
response = query_engine.query("小麦手机的主要参数是什么、其特性如何？")
print(response)
```

先按 LangChain 的规范构造一个输出解析器，然后把它插入到 llm 模块中，其余无需改动。输出效果如图 7-41 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_251_0.png)

你也可以调用输出解析器的 format 接口来查看 Prompt 的限制方式，例如传入一个默认的文本问答 Prompt 模板：

```
python
from llama_index.core.prompts.default_prompts import DEFAULT_TEXT_QA_PROMPT_TMPL
print(output_parser.format(DEFAULT_TEXT_QA_PROMPT_TMPL))
```

打印结果如图 7-42 所示，可以看到解析器会在原 Prompt 之后追加一段结构化输出的指令要求，从而实现对模型输出格式的约束。

```json
{
    "name": "string", // 手机名称
    "cpu": "string", // 手机处理器
    "memory": "string", // 手机内存
    "features": ["str"] // 手机特性
}
```

图 7-42

## 【基础篇小结】

从基础篇开始，我们正式进入基于大语言模型的 RAG 应用开发世界。

我们先通过一个入门级的 RAG 开发案例理解了 RAG 的核心原理，形成初步认知，并通过原生代码、LangChain 框架、LlamaIndex 框架三种实现方式体会到使用框架的价值。

随后基于 LlamaIndex 框架学习了经典 RAG 应用的主要开发阶段，掌握了相关组件与 API 的使用、组合与测试，并深入理解了部分关键组件的内部机制，有助于后续更好地使用和优化 RAG 应用。

基础篇最后重点讲解了面向最终应用的两种典型 RAG 引擎的构建方式，包括查询引擎与对话引擎，并对检索模式、响应生成模式与对话模式进行了深入剖析。

到这里，你已经具备开发经典乃至复杂度较高的 RAG 应用的能力。

## 高级篇

## 第8章 RAG 引擎高级开发

随着 RAG 应用在生产环境中的持续迭代，越来越多的组件、算法、优化流程与范式涌现，这些新技术让 RAG 应用的适应范围更广、生成精度更高，对构建知识密集型 AI 应用具有长远意义。本章聚焦于模块化 RAG 时代常见的进阶开发阶段与相关技术。

### 8.1 检索前查询转换

查询转换，也就是查询重写或查询分析，已成为大模型应用中一个重要的前置环节。查询转换属于检索前阶段，用来把输入问题改写为一种或多种更适合检索的形式。常见类型有：

1. 把输入问题改写为更利于嵌入的表达，提升知识召回的精准度。
2. 对输入问题做语义丰富与扩展，帮助生成更全面准确的答案。
3. 把初始问题拆解为多个子问题，分别检索后再合成答案。
4. 把初始查询拆分为可分步完成的子查询，通过逐步检索得到答案。

为什么要做查询转换？以一个典型场景为例，使用问答或搜索系统时用户习惯用单一问题查询，但单一问题往往不足以完整、细致地表达真实意图，导致检索到的知识覆盖不全。比如想了解“GPT-4 模型”，就可以扩展成“GPT-4 模型的基准测试表现”“GPT-4 模型的使用价格”“GPT-4 模型的 API 说明”等多个相关问题，这些问题能帮助检索到更全面的知识，并从不同角度生成更深入的答案。

实际应用中，查询转换可以是一次性的，即检索前对输入问题重写；也可以是多次迭代的，例如在新型 RAG 范式里，重写并检索生成后，会根据生成结果的质量评估再次触发查询转换，进行多次循环优化。

### 8.1.1 简单查询转换

借助强大的大模型，可以用 Prompt 对查询做简单重写，例如：

```python
from llama_index.core import PromptTemplate
from llama_index.llms.openai import OpenAI

prompt_rewrite_temp = """\n您是一个聪明的查询生成器。请生成与以下查询相关的{num_queries}个查询问题 \n
注意每个查询问题都占一行 \n
我的查询: {query}
生成查询列表:
"""
prompt_rewrite = PromptTemplate(prompt_rewrite_temp)
llm = OpenAI(model="gpt-3.5-turbo")

# 查询转换的方法
def rewrite_query(query: str, num: int = 3):
    response = llm.predict(
        prompt_rewrite, num_queries=num, query=query
    )

    # 假设大模型将每个查询问题都放在一行上
    queries = response.split("\n")
    return queries

print(rewrite_query("中国目前大模型的发展情况如何？"))
```

在这个简单函数里，可以按需自定义和完善 Prompt。

缺点是，在缺乏上下文环境与额外指令的情况下，容易产生较大不确定性，甚至偏离真实意图，因此不建议直接在查询前使用，更适合在数据准备与加载阶段使用。

（1）利用原始知识，尤其是问答类知识，生成相似问题来实现语义丰富。
（2）用于元数据抽取，用原始知识生成假设性查询问题以便嵌入。

### 8.1.2 HyDE 查询转换

HyDE（Hypothetical Document Embeddings，假设性文档嵌入）查询转换在很多场景下已被证明效果良好。其核心流程是根据输入问题先生成一个假设性答案，再对该假设答案进行嵌入与检索，可同时保留原问题，如图 8-1 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_257_0.png)

实现并不复杂，可通过提示工程完成。这里借助 LlamaIndex 框架中的 HyDE 查询转换器做演示：

```python
from llama_index.core.indices.query.query_transform import HyDEQueryTransform
from llama_index.llms.openai import OpenAI
from llama_index.core import PromptTemplate

#修改成中文 Prompt
hyde_prompt_temp = """\n请生成一段文字来回答输入问题\n
尽可能含有更多的关键细节\n
{context_str}
生成内容:
"""
```

```python
hyde_prompt = PromptTemplate(hyde_prompt_temp)

llm = OpenAI(model="gpt-3.5-turbo")
hyde = HyDEQueryTransform(llm=llm)
hyde.update_prompts({'hyde_prompt':hyde_prompt})

query_bundle = hyde.run("请介绍小麦手机的主要配置")
print(query_bundle.__dict__)
```

示例中使用 HyDEQueryTransform 对输入问题进行转换。需要注意，HyDE 转换器的结果是用于嵌入与检索的，并不会直接返回新的 query_str，而是把原问题放在 query_bundle 包装对象的 custom_embedding_strs 字段中。该字段会在查询时被用于嵌入与检索，如图 8-2 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_258_0.png)

图 8-2

为简化后续查询流程，通常建议使用 TransformQueryEngine 这个封装类，为已有查询引擎增加基于 HyDE 的查询转换能力，从而透明使用 HyDE 转换器，无需自行管理：

```python
......这里假设已经构造了一个城市信息查询引擎......
query_engine = create_city_engine('南京市') #城市信息查询引擎
hyde_query_engine = TransformQueryEngine(query_engine, hyde)

print('\nQuerying the city engine...')
response = query_engine.query('南京市的人口是多少？经济发展如何？')
pprint_response(response,show_source=True)

print('\nQuerying the HyDE city engine...')
response_hyde = hyde_query_engine.query("南京市的人口是多少？经济发展如何？")
pprint_response(response_hyde ,show_source=True)
```

这里用 TransformQueryEngine 完成查询，并与普通引擎做对比。

未加入 HyDE 转换时的输出如图 8-3 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_259_0.png)

加入 HyDE 转换后的输出如图 8-4 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_259_1.png)

两者差异明显：受 HyDE 生成的假设答案检索影响，召回知识的相关性提升，答案质量也随之提高。

示例中构建的城市信息查询引擎基于维基百科城市介绍内容搭建，后续会多次使用。

### 8.1.3 多步查询转换

对于较复杂的问题，直接用查询引擎回答往往因召回知识块的精确性或完整性不足而导致结果不理想。可采用多步查询转换思路：从初始复杂查询出发，经过多轮查询转换与检索生成，直至能完整回答输入问题，如图 8-5 所示。每一步转换都基于前一步的推理，提出下一步的子问题。

![](img/7eddb68cd925518fdc310e52e94ba9a9_260_0.png)

下面继续沿用前面的城市信息查询引擎，演示多步查询转换的使用方式。

```python
......构造一个简单的城市信息查询引擎，代码略......
query_engine = create_city_engine(['北京市','上海市'])

#转换 Prompt，此处用于更新默认的 Prompt
prompt_templ = """
我们有机会从知识源中回答部分或全部问题。知识源的上下文如下，提供了之前的推理步骤。
根据上下文和之前的推理，返回一个可以从上下文中回答的问题：
1. 这个问题可以帮助回答原问题，与原问题密切相关。
2. 可以是原问题的子问题，或者是解答原问题需要的一个步骤中需要的问题。
如果无法从上下文中提取更多信息，则提供“无”作为答案。下面给出了一个示例：
-----
问题：2020 年澳大利亚网球公开赛冠军获得了多少个大满贯冠军？
知识源上下文：提供了 2020 年澳大利亚网球公开赛冠军的名字
之前的推理：无
新问题：谁是 2020 年澳大利亚网球公开赛的冠军？
-----
我的问题：{query_str}
知识源上下文：{context_str}
之前的推理：{prev_reasoning}
新问题：
"""

#查询转换器
step_transformer = StepDecomposeQueryTransform(llm=llm_openai,
verbose=True)

#转换 Prompt
```

```python
new_prompt = PromptTemplate(prompt_templ)
step_transformer.update_prompts({'step_decompose_query_prompt':new_prompt})

#带有查询转换器的查询引擎
step_query_engine = 
MultiStepQueryEngine(query_engine=query_engine,

query_transform=step_transformer,index_summary='这是一个关于城市的知识库，用于回答与城市信息相关的问题')

print('\nQuerying the stepcompose city engine...')

response = step_query_engine.query("中国首都的城市人口有多少？和上海相比呢？")
pprint_response(response,show_source=True)
```

与 HyDE 查询转换的思路相近，多步查询转换同样提供了一套可直接调用的查询引擎封装。将现成的查询引擎与查询转换器组合起来，就能实例化出 MultiStepQueryEngine 类型的引擎。运行示例时能清晰看到，引擎在多步转换过程中会先拆解出若干子问题，这些子问题正是为更全面地覆盖原始问题的意图而产生的，具体效果可参考图 8-6。

![](img/7eddb68cd925518fdc310e52e94ba9a9_261_0.png)

在这类任务里，如果继续使用常规查询引擎，很难得到准确答案。问题的根源在于输入并非单一事实查询，而是需要汇聚多处知识点的复合问题；尤其是 top_K 取值偏小时，召回范围受限，既可能答不上来，也容易产生幻觉。

### 8.1.4 子问题查询转换

子问题查询转换的核心做法是，在问答过程中围绕原问题自动生成多条更具体的子问题，用来深化对问题的理解并支撑最终答案的生成，如图 8-7 所示。一种更落地的用法是借鉴 Agent 的思路，基于当前可用的工具集合，把输入问题拆成每个工具都能单独处理的子问题。与普通的多步转换相比，这种方式必须参考工具清单，约束更强，也适合在非 Agent 场景下实现带约束的子问题拆解。

![](img/7eddb68cd925518fdc310e52e94ba9a9_262_0.png)

图 8-7

下面给出一个完整示例：

```python
from llama_index.question_gen.openai import OpenAIQuestionGenerator
from llama_index.llms.openai import OpenAI
from llama_index.core import PromptTemplate, QueryBundle
from llama_index.core.tools import ToolMetadata
import pprint

llm = OpenAI()

question_gen_prompt_templ = """
你可以访问多个工具，每个工具都代表一个不同的数据源或 API。
每个工具都有一个名称和一个描述字段，格式为 JSON 字典。
字典的键(key)是工具的名称，值(value)是描述。
你的目的是通过生成一系列可以由这些工具回答的子问题来帮助回答一个复杂的用户问题。
在完成任务时，请考虑以下准则：
- 尽可能具体
- 子问题应与用户问题相关
- 子问题应可通过提供的工具回答
- 你可以为每个工具都生成多个子问题
- 工具必须用它们的名称而不是描述来指定
- 如果你认为不相关，就不需要使用工具

通过调用 SubQuestionList 函数输出子问题列表。

## Tools
{tools_str}

## User Question
{query_str}

"""

#rewriter
question_rewriter = OpenAIQuestionGenerator.from_defaults(llm=llm)

#转换 Prompt
new_prompt = PromptTemplate(question_gen_prompt_templ)
question_rewriter.update_prompts({'question_gen_prompt':new_prompt})

#可用的工具，注意这里只是提供工具的元数据，并未真正提供工具
tool_choices = [
    ToolMetadata(
        name="query_tool_beijing",
        description=(
            "用于查询北京市各个方面的信息，如基本信息、旅游指南、城市历史等"
        ),
    ),
    ToolMetadata(
        name="query_tool_shanghai",
        description=(
            "用于查询上海市各个方面的信息，如基本信息、旅游指南、城市历史等"
        ),
    ),
]

print('-------------------------')
```

```python
query_str = "北京与上海的人口差距是多少？它们的面积相差多少？"

#使用 generate 方法生成子问题
choices = question_rewriter.generate(
    tool_choices,
    QueryBundle(query_str=query_str))

pprint.pprint(choices)
```

示例代码先声明了多项可用工具的元信息，随后指示查询转换器依据这些工具对输入问题进行子问题生成，注意 Prompt 模板的设定。最终的生成结果如图 8-8 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_264_0.png)

从结果可以看出，转换器共产出了 4 个子问题，并分别对应两类可用工具，说明工具信息确实起到了对子问题生成的限制作用。

实际使用时并不需要真的构造可执行工具交给转换器，只需提供工具的名称和描述并封装成 ToolMetadata 即可。因此该模式具备很强的扩展性：工具不必是真实的查询引擎，也可以把用于引导拆解的描述“伪装”成工具，对 Prompt 做少量改动后即可生成更合规的子问题。这种做法有助于把复杂问题拆解清楚，后续再根据返回的工具名称做针对性处理，灵活性更高。

需要留意的是，OpenAIQuestionGenerator 的实现依赖大模型的函数调用能力，所以仅适用于支持函数调用的模型。若想换用其他模型生成子问题，可选用 LLMQuestionGenerator，两者在用法上保持一致。

针对子问题生成的场景，框架还提供了现成的查询引擎组件。通过搭建子问题查询引擎，可以在更高层统一调度多个查询引擎，例如为不同年份、不同地区分别构建独立引擎，再借助子问题查询引擎实现跨引擎的联合检索。

子问题查询引擎的运行流程与图 8-9 一致：

- 先借助查询转换器对输入问题进行判断与拆分。
- 再对拆分出的子问题逐一调用对应的查询引擎或工具进行检索。
- 最后把各次检索结果汇总为上下文，交给大模型生成最终答案。

![](img/7eddb68cd925518fdc310e52e94ba9a9_265_0.png)

图 8-9

这里默认使用的转换器是前面提到的 OpenAIQuestionGenerator 或 LLMQuestionGenerator，默认是前者；若模型不支持函数调用，则切换为 LLMQuestionGenerator。

下面用一个简易例子演示子问题查询引擎的调用方式：

```python
from llama_index.core.query_engine import SubQuestionQueryEngine
# 省略 create_city_engine 方法
# 构造两个城市信息查询引擎
query_engine_nanjing = create_city_engine('南京市')
query_engine_shanghai = create_city_engine('上海市')
# 查询引擎作为工具
query_engine_tools = [
    QueryEngineTool(
        query_engine=query_engine_nanjing,
        metadata=ToolMetadata(
            name="query_tool_nanjing",
            description="用于查询南京市各个方面的信息，如基本信息、旅游指南、城市历史等"
        ),
    ),
    QueryEngineTool(
        query_engine=query_engine_shanghai,
        metadata=ToolMetadata(
            name="query_tool_shanghai",
            description="用于查询上海市各个方面的信息，如基本信息、旅游指南、城市历史等"
        ),
    ),
]

# 构造子问题查询引擎
query_engine = SubQuestionQueryEngine.from_defaults(
    query_engine_tools=query_engine_tools,
    use_async=True,
)

# 查询
response = query_engine.query(
    "北京与上海的人口差距是多少？GDP 大约相差多少？使用中文回答"
)

print(response)
```

观察该例的输出，如图 8-10 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_266_0.png)

图 8-10

本例中，城市信息查询引擎只能处理单个城市的查询，原始问题显然无法由单一引擎直接回答。借助子问题查询引擎，问题被拆成 4 个子问题，分别由对应的工具完成检索，收集齐所有子答案后再综合生成最终结果，问题得以完整解决。

对比 Agent 会发现，子问题查询引擎与 Agent 在整体思路上很相似，都是把工具集交给一个调度器实现任务拆分和工具选择。关键差异在于规划方式：子问题查询引擎借助大模型一次性完成子问题的生成，而 Agent 如 ReAct 范式则在执行过程中动态规划，根据前一步的结果和目标实时决定下一步动作。因此 Agent 更贴近人类的执行习惯，灵活性更高，但不确定性也更大。

## 8.2 检索后处理器

在真实应用中，检索器返回结果到响应生成器生成答案之间，通常还需要额外加工，比如关键词筛选或重排。除了自行编写代码处理检索器返回的 Node 列表，LlamaIndex 还提供了更模块化的方案，即节点后处理器。

### 8.2.1 使用节点后处理器

节点后处理器负责对检索得到的 Node 进行转换、过滤或重排序，一般嵌入在查询引擎内部，位于检索器之后、响应生成器之前，如图 8-11 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_267_0.png)

框架内置了大量开箱即用的后处理器，也支持按接口自定义。与检索器类似，后处理器既可独立调用，也能作为参数注入查询引擎。

#### 1. 独立使用节点后处理器

可以直接实例化指定类型的后处理器，再调用 postprocess_nodes 方法对 Node 集合进行处理：

```python
# 先检索出 Node，假设保存在 nodes_with_scores 变量中
processor = SimilarityPostprocessor(similarity_cutoff=0.8)
filtered_nodes = processor.postprocess_nodes(nodes_with_scores)
# 将 filtered_nodes 变量用于响应生成
```

#### 2. 插入查询引擎中使用节点后处理器

构造查询引擎时，可像在数据摄取管道中添加转换器一样，传入多个节点后处理器，由引擎自动串联执行：

```python
# ...
vector_index = VectorStoreIndex(nodes)
query_engine = vector_index.as_query_engine(
    node_postprocessors=[
        SimilarityPostprocessor(similarity_cutoff=0.5)
    ]
)
# ...
```

### 8.2.2 实现自定义的节点后处理器

自定义后处理器可继承 BaseNodePostprocessor 并实现 _postprocess_nodes 方法，从而加入任意业务逻辑。例如借助正则表达式对检索内容进行过滤即可定义一个专用的后处理器：

```python
class MyNodePostprocessor(BaseNodePostprocessor):
    def _postprocess_nodes(
        self, nodes: List[NodeWithScore], query_bundle: Optional[QueryBundle]
    ) -> List[NodeWithScore]:
        pattern = r"过滤正则表达式"
        filtered_nodes = []
        for node in nodes:
            if not re.search(pattern, node.text):
                filtered_nodes.append(node)
        nodes = filtered_nodes
        return nodes
```

随后在构建查询引擎时使用：

```python
query_engine = vector_index.as_query_engine(
    node_postprocessors=[
        MyNodePostprocessor()
    ]
)
```

将其接入查询引擎后，检索完成后会自动触发该过滤逻辑。

### 8.2.3 常见的预定义的节点后处理器

下面介绍几类常用内置后处理器及其原理与用法。

#### 1. 相似度过滤处理器

相似度过滤是常见策略。召回 Node 后可按相似度分数设定阈值，比如只保留高于 0.7 的结果，以保证上下文高度相关。

(1) 处理器类型：SimilarityPostprocessor。
(2) 输入参数：similarity_cutoff，控制过滤的分数阈值。

#### 2. 关键词过滤处理器

8.2.2 节提到的自定义实现本质上就是关键词过滤的一种。关键词过滤处理器允许设定必须匹配的关键词和需要排除的关键词。

(1) 处理器类型：KeywordNodePostprocessor。
(2) 输入参数：required_keywords，表示必须出现的关键词；exclude_keywords，表示需要剔除的关键词。

注意：内置的关键词处理器并非简单字符串匹配，而是基于 spaCy 的 NLP 能力实现更灵活的过滤，例如英文词形还原与词干提取。若要处理中文，需要在构造时指定 lang 参数。

以下是一个关键词过滤处理器的简易使用示例：

```python
# ... 构造关键词过滤处理器 ...
processor = KeywordNodePostprocessor(
    required_keywords=["小麦手机"],
    exclude_keywords=[],
    lang='zh-Hans'
)
filtered_nodes = processor.postprocess_nodes(nodes_with_scores)
# ...
```

#### 3. 用元数据覆盖节点文本

元数据替换处理器的主要作用是把节点里某个键对应的元数据值拿来覆盖节点本身的文本内容。回想一下数据加载部分介绍的 SentenceWindowNodeParser 分割器，它在把 Document 拆成若干 Node 时，会先按句子切分，并在每个节点的元数据中保留设定窗口范围的上下文，检索后就能拿到更宽的上下文信息，这种扩展思路在图 8-12 中有示意。

![](img/7eddb68cd925518fdc310e52e94ba9a9_271_0.png)

图 8-12

要让检索结果直接展示完整的窗口，就可以通过元数据替换处理器把元数据中保存的窗口内容替换掉节点的原始文本。下面是一个演示代码：

```python
# ...
docs = [Document(text="小麦手机是小麦公司最新出的第十代手机产品。 \n采用了中国最先进的国产红旗 CPU 芯片。 \n采用了 6.95 寸的 OLED 显示屏幕与 5000 毫安的电池容量。")]

# 解析与分割文档
node_parser = SentenceWindowNodeParser.from_defaults(
    sentence_splitter=my_chunking_tokenizer_fn,
    window_size=3,
    window_metadata_key="window",
    original_text_metadata_key="original_text",
)
nodes = node_parser.get_nodes_from_documents(docs)
vector_index = VectorStoreIndex(nodes=nodes)
```

示例中使用了 SentenceWindowNodeParser 作为分割器，通过自定义函数 my_chunking_tokenizer_fn 让整段文本被切成三句话，设置 window_size=3，每个节点都保留前后三句的上下文并写入元数据字段 window。随后调用 get_nodes_from_documents 完成切分，三句话被生成为三个节点，第一个节点的形态如图 8-13。

```
Count of nodes: 3
-----
Node 0, ID: d513bf72-e549-476b-8ff9-1fa97f207834
text:小麦手机是小麦公司最新出的第十代手机产品
metadata:{'window': '小麦手机是小麦公司最新出的第十代手机产品 采用了中国最先进的国产红旗CPU芯片 采用了6.95寸的OLED显示屏幕与5000毫安的电池容量', 'original_text': '小麦手机是小麦公司最新出的第十代手机产品'}
-----
```

图 8-13

可以看出 text 只保留了第一句话，而 metadata 中的 window 则保存了更宽的上下文，后续替换就依赖这部分信息。先搭建一个不带节点后处理器的查询引擎，对一个测试问题进行提问：

```python
# 此时不指定节点后处理器
query_engine = vector_index.as_query_engine(
    similarity_top_k=1
)
window_response = query_engine.query(
    "小麦手机是哪个公司出品的，采用什么芯片？"
)
pprint_response(window_response, show_source=True)
```

输出如图 8-14 所示，仅匹配到一个节点，上下文量不足以完整回答。

```
-----
Final Response:
小麦手机是由小麦公司出品的。然而，关于具体的采用何种芯片的信息，在给定的上下文中并未提供。因此，这方面的细节需要参考其他来源或官方发布。
Source Node 1/1
Node ID: d513bf72-e549-476b-8ff9-1fa97f207834
Similarity: 0.7427610189177628
Text: 小麦手机是小麦公司最新出的第十代手机产品
-----
```

图 8-14

把代码改成在节点后处理阶段加入元数据替换处理器，并指定以 metadata.window 替换节点 text：

```python
query_engine = vector_index.as_query_engine(
    similarity_top_k=1,
    node_postprocessors=[
        MetadataReplacementPostProcessor(target_metadata_key="window")
    ]
)
```

再次查看输出后会发现节点内容被扩展为窗口级别的片段，信息足够，问题就能得到完整回答，如图 8-15。

![](img/7eddb68cd925518fdc310e52e94ba9a9_273_0.png)

图 8-15

#### 4. 按时间倒序的节点处理器

这类处理器可以指定元数据中表示时间的键，在处理时按时间类型进行倒序排列并返回指定数量的节点。例如下面代码让处理器依据元数据的 create_time 倒排，只返回最新的一条：

```python
query_engine = vector_index.as_query_engine(
    similarity_top_k=3,
    node_postprocessors=[
        FixedRecencyPostprocessor(top_k=1, date_key="create_time")
    ],
)
```

还有其他时间相关的实现，如 EmbeddingRecencyPostprocessor 会先利用嵌入模型判断相似度，把过于相似的老节点剔除后再做时间排序，具体可参考官方文档。

### 8.2.4 节点重排序处理器

在所有节点后处理器中，重排序是最常用也最关键的类型。其思路是对检索得到的节点列表重新排位，提升与用户问题相关性更高的节点排名，让大模型生成时优先参考更优质的内容。

既然已有向量索引和语义相似度检索，为什么还要重排？主要原因有三点：

- 一是 RAG 系统往往包含多种索引形态，并非全部基于语义向量，非向量索引的召回结果需要独立的重排来校正。
- 二是随着 RAG 架构日趋复杂，单一索引难以满足需求，混合检索、外部搜索、数据库查询等多源结果更需要统一重排。
- 三是即使是纯向量索引，也会因嵌入模型、相似算法、语言环境、领域特性等产生排序偏差，独立重排可以有效纠偏。

重排通常依赖专门的 Rerank 模型，下面介绍两种效果较好的方案。

#### 1. 调用 Cohere Rerank 服务

Cohere Rerank 是一个商业闭源的重排模型，能根据查询语义对多段文本排序，常用于提升关键词检索或向量检索的质量。

使用前需在官网注册并申请测试用的 API Key，测试额度免费，流程如图 8-16 所示。

## 2. 部署 bge-reranker-large 模型

bge-reranker-large 是智源研究院开源的重排模型，在多项评测中表现突出。我们通过 Hugging Face 的 TEI 在本地部署。以 macOS 为例，运行以下命令即可自动下载模型并启动服务：

```bash
> model=BAAI/bge-reranker-large
> text-embeddings-router --model-id $model --port 8080
```

部署后可先用浏览器验证接口。访问 http://localhost:8080/docs 进入 TEI 文档页，找到 /rerank 服务，点击 Try it out 输入简单参数测试，请求界面如图 8-19。

![](img/7eddb68cd925518fdc310e52e94ba9a9_277_0.png)

图 8-19

若返回结果与图 8-20 一致，则说明服务正常。

![](img/7eddb68cd925518fdc310e52e94ba9a9_278_0.png)

接下来需要为该服务封装一个自定义的 Rerank 节点后处理器，因为 LlamaIndex 目前未内置此模型。完整实现如下：

```python
import requests
from typing import List, Optional
from llama_index.core.bridge.pydantic import Field, PrivateAttr
from llama_index.core.postprocessor.types import BaseNodePostprocessor
from llama_index.core.schema import NodeWithScore, QueryBundle

class BgeRerank(BaseNodePostprocessor):
    url: str = Field(description="Rerank server url.")
    top_n: int = Field(description="Top N nodes to return.")

    def __init__(self, top_n: int, url: str):
        super().__init__(url=url, top_n=top_n)

    #调用 TEI 的 Rerank 模型服务
    def rerank(self, query, texts):
        url = f"{self.url}/rerank"
        request_body = {"query": query, "texts": texts, "truncate": False}
        response = requests.post(url, json=request_body)
        if response.status_code != 200:
            raise RuntimeError(f"Failed to rerank, detail: {response}")
        return response.json()

    @classmethod
    def class_name(cls) -> str:
        return "BgeRerank"

    #实现 Rank 节点后处理器的接口
    def _postprocess_nodes(
        self,
        nodes: List[NodeWithScore],
        query_bundle: Optional[QueryBundle] = None,
    ) -> List[NodeWithScore]:
        if query_bundle is None:
            raise ValueError("Missing query bundle in extra info.")
        if len(nodes) == 0:
            return []

        #调用 Rerank 模型
        texts = [node.text for node in nodes]
        results = self.rerank(
            query=query_bundle.query_str,
            texts=texts,
        )

        #组装并返回 Node
        new_nodes = []
        for result in results[0 : self.top_n]:
            new_node_with_score = NodeWithScore(
                node=nodes[int(result["index"])].node,
                score=result["score"],
            )
            new_nodes.append(new_node_with_score)
        return new_nodes
```

封装完成后使用方式与内置处理器一致，只需在原有 Cohere 示例上做少量修改：

```python
......
#构造自定义的节点后处理器
customRerank = BgeRerank(url="http://localhost:8080", top_n=2)

#测试处理 Node
rerank_nodes = customRerank.postprocess_nodes(nodes, query_str='百度文心一言的逻辑推理能力怎么样?')
......
```

也可以在构建查询引擎时直接注入：

```python
query_engine = vector_index.as_query_engine(
    similarity_top_k=3,
    node_postprocessors=[customRerank],
)
```

最后查看重排后的节点内容，正常情况下输出与 Cohere 版本相近，如图 8-21 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_280_0.png)

## 8.3 语义路由

### 8.3.1 语义路由是什么

常见需求是：针对不同知识库和应用特点构建多个查询引擎，它们面向不同领域并使用不同索引，如 VectorIndex 与 GraphIndex，但对外要保持统一体验，用户只需提问而无需关心后端实际调用了哪套引擎。

在模块化 RAG 中，通常在检索前加入路由模块，识别用户意图并把问题分发给合适的检索生成链路，如图 8-22 所示。路由模块借助大模型的语义判断能力，主要用于：

- （1）作为纯选择器，在多个备选项中决策。
- （2）在多个知识数据源间挑选查询目标。
- （3）在不同索引或响应类型间切换，例如事实问答与内容总结。
- （4）同时触发多个查询引擎并合并结果。

![](img/7eddb68cd925518fdc310e52e94ba9a9_281_0.png)

图 8-22

一个基于大模型的路由模块至少包含两部分：

- （1）Selector 选择器，通常基于大模型实现。LlamaIndex 提供了两种：基于 Pydantic 类型的选择器，依赖 OpenAI 函数调用完成路由；以及通用大模型选择器，把候选信息组装进 Prompt 后让模型按语义选择。
- （2）候选集合，提供给选择器的目标，可为多个查询引擎、多个检索器，或简单的字符串列表。

举例来说，一个带路由功能的查询引擎大致这样构造：

```python
query_engine = RouterQueryEngine(
    selector=LLMSingleSelector.from_defaults(),
    query_engine_tools=[······多个 tool······]
)
```

### 8.3.2 实战：路由查询引擎

下面用一个小例子演示如何把用户查询路由到不同引擎，实现按语义访问不同知识源。选用之前测试的两份文档 xiaomai.txt 与 yiyan.txt，分别构建独立的查询引擎：

```python
......
docs_xiaomai = SimpleDirectoryReader(input_files=["../../data/xiaomai.txt"]).load_data()
docs_yiyan = SimpleDirectoryReader(input_files=["../../data/yiyan.txt"]).load_data()

vectorindex_xiaomai = VectorStoreIndex.from_documents(docs_xiaomai)
query_engine_xiaomai = vectorindex_xiaomai.as_query_engine()

vectorindex_yiyan = VectorStoreIndex.from_documents(docs_yiyan)
query_engine_yiyan = vectorindex_yiyan.as_query_engine()
```

接下来我们就来搭建一个具备路由能力的查询引擎。路由层要能做决定，首先得感知有哪些候选方案，因此把待选的查询引擎全部包装成工具形态，再整体交给路由型查询引擎管理：

```python
......
#构造第一个工具
tool_xiaomai = QueryEngineTool.from_defaults(
    query_engine=query_engine_xiaomai,
    description="用于查询小麦手机的信息",
)

#构造第二个工具
tool_yiyan = QueryEngineTool.from_defaults(
    query_engine=query_engine_yiyan,
    description="用于查询文心一言的信息",
)
```

```python
#构造路由模块
query_engine = RouterQueryEngine(
    selector=LLMSingleSelector.from_defaults(), #选择器
    query_engine_tools=[ #候选工具
        tool_xiaomai, tool_yiyan
    ]
)

#像使用查询引擎一样使用即可
response = query_engine.query("什么是文心一言，用中文回答")
pprint_response(response, show_source=True)
```

这样一来，路由型查询引擎会依据用户的问题语义，自动挑选中后端的工具也就是不同的查询引擎来生成答案。这里的每个查询引擎对应一套独立的数据源。

同一个数据源也可以拆出多种查询引擎，通过路由来动态匹配。比如一套专攻事实细节，采用常规的 compact 式生成；另一套偏向概括总结，则使用 tree_summarize 式的生成器：

```python
......
#针对同一个索引构造不同的响应类型的查询引擎
query_engine_quesiton = vectorindex_xiaomai.as_query_engine(response_mode="compact")
query_engine_summary = vectorindex_xiaomai.as_query_engine(response_mode="simple_summarize")

#“工具化”查询引擎
tool_question = QueryEngineTool.from_defaults(
    query_engine=query_engine_quesiton,
    description="用于回答事实性与细节性的问题",
)
tool_summarize = QueryEngineTool.from_defaults(
    query_engine=query_engine_summary,
    description="用于回答总结性的问题",
)
```

![](img/7eddb68cd925518fdc310e52e94ba9a9_283_0.png)

# 8.3.3 路由式检索器

除了把查询引擎交给路由做选择，还能直接把检索器作为候选工具喂给路由，让路由决定用哪款检索器而不是哪款查询引擎。这种做法在只想做检索、不想直接回答的场景里特别实用：

```python
vector_index = VectorStoreIndex(nodes)
retriever_xiaomai = vector_index.as_retriever()

vector_index2 = VectorStoreIndex(nodes2)
retriever_yiyan = vector_index2.as_retriever()

tool_xiaomai = RetrieverTool.from_defaults(
    retriever=retriever_xiaomai,
    description="用于查询小麦手机的信息",
)
tool_yiyan = RetrieverTool.from_defaults(
    retriever=retriever_yiyan,
    description="用于查询文心一言的信息",
)

#构造带有路由功能的检索器
retriever = RouterRetriever(
    selector=LLMSingleSelector.from_defaults(),
    retriever_tools=[
        tool_xiaomai, tool_yiyan
    ]
)
```

```python
nodes = retriever.retrieve("什么是文心一言？")
print_nodes(nodes)
```

RouterRetriever 本质上仍然是一个检索器，它做不了直接作答，只能完成检索任务。路由会根据语义智能挑选最合适的检索器执行，并把检索到的多个 Node 原样返回。

# 8.3.4 独立选择器

当需要在多项备选里做抉择时，可以引入独立的选择器。选项的描述可以用 ToolMetadata 来定义，思路有点像查询转换里用 ToolMetadata 让模型推子问题，不过这里的输出是最终的选择结果。例如：

```python
choices = [
    "choice 1: 通过互联网查询当前实时的信息",
    "choice 2: 通过大模型查询非实时信息或者创作内容",
]
```

或者

```python
choices = [
    ToolMetadata(description="查询当前实时的信息",
                 name="web_search"),
    ToolMetadata(description="知识查询或内容创作",
                 name="query_engine")
]
```

写一段简单的测试代码感受一下效果：

```python
choices = [
    ToolMetadata(description="查询当前实时的信息",
                 name="web_search"),
    ToolMetadata(description="知识查询或内容创作",
                 name="query_engine")
]
```

```python
selector = LLMSingleSelector.from_defaults()
selector_result = selector.select(
    choices, query="写一个悬疑小故事?"
)
print(selector_result.selections)
```

跑这个例子，模型判断最合适的是第二个选项，索引为 1，具体见图 8-23。

![](img/7eddb68cd925518fdc310e52e94ba9a9_286_0.png)

图 8-23

# 8.3.5 多选路由查询引擎

有时我们并不想只选一个引擎，而是希望把一次提问同时派发给多个引擎，发挥不同索引的长处来提升答案质量。这就用得到多选路由查询引擎。它会依据语义挑选出若干可用工具，分别调用生成结果，最后再由大模型把多份结果融合，输出一份综合答案。不同索引、不同生成策略因此可以互相补位。

下面演示构建三种索引向量、关键词、摘要对应的查询引擎，再用多选路由查询引擎把问题同时分发到这三路进行联合生成：

```python
summary_index = SummaryIndex.from_documents(docs, chunk_size=100, chunk_overlap=0)
vector_index = VectorStoreIndex.from_documents(docs, chunk_size=100, chunk_overlap=0)
keyword_index = SimpleKeywordTableIndex.from_documents(docs, chunk_size=100, chunk_overlap=0)
#构造 3 个可用工具
```

```python
summary_tool = QueryEngineTool.from_defaults(
    query_engine=summary_index.as_query_engine(response_mode="tree_summarize"),
    description=(
        "有助于总结与小麦手机相关的问题"
    ),
)

vector_tool = QueryEngineTool.from_defaults(
    query_engine=vector_index.as_query_engine(),
    description=(
        "适合检索与小麦手机相关的特定上下文"
    ),
)

keyword_tool = QueryEngineTool.from_defaults(
    query_engine=keyword_index.as_query_engine(),
    description=(
        "适合使用关键词从文章中检索特定的上下文"
    ),
)

#构造可多选的路由查询引擎
query_engine = RouterQueryEngine(
    selector=LMMultiSelector.from_defaults(),
    query_engine_tools=[
        summary_tool, vector_tool, keyword_tool
    ], verbose=True
)
response = query_engine.query("小麦手机的屏幕特点和优势是什么")
pprint_response(response, show_source=True)
```

结果如图 8-24，路由这次同时选中了 engine0 和 engine1 两条路。

![](img/7eddb68cd925518fdc310e52e94ba9a9_287_0.png)

图 8-24

换一个问题再试试：

```python
response = query_engine.query("小麦手机的处理器是什么？")
```

这次路由偏向后两条引擎，具体见图 8-25。

![](img/7eddb68cd925518fdc310e52e94ba9a9_288_0.png)

图 8-25

# 8.4 SQL 查询引擎

前面聊的查询和对话引擎，更多是面向半结构化或非结构化数据的检索。到了生产环境，尤其是企业级场景，绝对主流的是结构化数据，通常躺在关系型数据库里。对这类数据，最省事的做法不是全量向量化，而是留在库里直接用 SQL 去查。SQL 本身是编程语言，若想用自然语言提问，就需要借助大模型完成 Text-to-SQL，进而搭建数据库的 SQL 查询引擎，原理示意图见图 8-26。

![](img/7eddb68cd925518fdc310e52e94ba9a9_288_1.png)

图 8-26

这里用本地 Postgres 做演示，新建一张简易的订单表 orders，包含订单 ID、区域、客户、产品、价格等字段，并填充若干测试数据。表结构见图 8-27，设计上做了简化，不追求严谨规范。

| Name | Data type |

|---|---|

| order_id | integer |

| region | character varying |

| customer_id | integer |

| product_name | character varying |

| price | integer |

| amount | integer |

| total_price | integer |

| create_time | date |

| status | character |

| sales_name | character varying |

| sales_depart | character varying |

图 8-27

# 8.4.1 NLSQLTableQueryEngine 快速上手

先用最省事的方案搭建订单查询，借助内置的 NLSQLTableQueryEngine 组件一步到位：

```python
from sqlalchemy import (
    create_engine,
    MetaData,
    Table,
    Column,
    String,
    Integer,
    select,
    text
)
from sqlalchemy.orm import sessionmaker
```

```python
#构造 SQL 查询引擎
engine = create_engine("postgresql://postgres:****@localhost:5432/postgres")

#构造 SQLDatabase 对象
sql_database = SQLDatabase(engine,
include_tables=["customers","orders"])

from llama_index.core.query_engine import NLSQLTableQueryEngine

#构造 SQLTable 查询引擎：sql_database、tables、llm 参数
llm_openai = OpenAI(model='gpt-3.5-turbo')

query_engine = NLSQLTableQueryEngine(
    sql_database=sql_database,
    tables=["customers","orders"],
    llm=llm_openai
)

#测试
response = query_engine.query("一共有多少个订单")
print(response)
```

该做法只需要准备好 SQLDatabase 对象、目标表名以及一个大模型，即可实例化查询引擎。建好之后就能用自然语言直接跟数据库对话，不懂 SQL 也能完成查询、统计乃至简单分析。示例输出见图 8-28。

![](img/7eddb68cd925518fdc310e52e94ba9a9_290_0.png)

技术上是把用户问题和表的结构、描述信息一起拼成 Prompt 喂给大模型，让模型生成对应的 SQL 并执行，执行结果再由模型总结成自然语言答案，流程如图 8-29。

![](img/7eddb68cd925518fdc310e52e94ba9a9_291_0.png)

图 8-29

# 8.4.2 动态表检索查询引擎

上面做法在小表上很顺手，但面对大型数据库就吃力了。你得把所有可能用到的表都塞进参数，表一多上下文很容易爆掉。根源在于 NLSQLTableQueryEngine 为了让模型正确生成 SQL，必须把表的 Schema 结构和描述一起塞进 Prompt。Langfuse 的追踪日志里也能看到图 8-30 那样的提示，表结构被原样嵌入其中。

![](img/7eddb68cd925518fdc310e52e94ba9a9_291_1.png)

图 8-30

一次查询就把全库 Schema 全灌进去，不仅会干扰模型判断，还会触发窗口溢出和 token 成本飙升。更合理的策略是先用问题去检索出真正相关的表，再基于这些表做 Text-to-SQL，这样每次只带相关 Schema，既省空间也更聚焦。

框架里提供的 SQLTableRetrieverQueryEngine 就能实现这种思路，通过 object_index 参数在查询时动态检索相关的 SQLTableSchema 对象。接下来看示例：

```python
engine = create_engine("postgresql://postgres:Unyep123!!@localhost:5432/postgres")
metadata_obj = MetaData()
sql_database = SQLDatabase(engine, include_tables=["customers", "orders"])

from llama_index.core.indices.struct_store.sql_query import (
    SQLTableRetrieverQueryEngine,
)

from llama_index.core.objects import (
    SQLTableNodeMapping,
    SQLTableSchema,
    ObjectIndex,
)
from llama_index.core import VectorStoreIndex

# 构造用于检索 SQLTableSchema 对象的对象索引
# table_node_mapping 变量用于给 SQLTableSchema 对象与向量存储索引的 Node 做映射
table_node_mapping = SQLTableNodeMapping(sql_database)
table_schema_objs = [
    SQLTableSchema(table_name="customers"),
    SQLTableSchema(table_name="orders"),
    SQLTableSchema(table_name="mystore")
]
```

```python
# 构造一个检索的对象索引，底层通过向量存储索引来实现语义检索
obj_index = ObjectIndex.from_objects(
    table_schema_objs,
    table_node_mapping,
    VectorStoreIndex,
)

# 传入 retriever 方法，而不是直接传入多个 SQLTableSchema 对象
# 此处为了演示效果，设置 similarity_top_k=1
query_engine = SQLTableRetrieverQueryEngine(
    sql_database, obj_index.as_retriever(similarity_top_k=1)
)

response = query_engine.query("所有订单总金额是多少？")
print(response)
```

运行结果与 8.4.1 节示例保持一致。发起查询前，可以先手动调用 retriever 查看检索到的 Schema，用来核对检索是否准确：

```python
table_retriever = obj_index.as_retriever(similarity_top_k=1)
tables = table_retriever.retrieve("所有订单总金额是多少")
print(tables)
```

当输出呈现如图 8-31 的形态时，就说明针对当前问题需要去 orders 表里查数。

![](img/7eddb68cd925518fdc310e52e94ba9a9_293_0.png)

图 8-31

# 8.4.3 SQL检索器组装方案

8.4.1 与 8.4.2 都是借助框架上层的 NLSQLTableQueryEngine 或 SQLTableRetrieverQueryEngine 直接搭建数据库查询引擎。若想走标准链路、用 RetrieverQueryEngine 来驱动 SQL 查询，就可以用 NLSQLRetriever 来替代完整的 SQL 查询引擎，构建思路与之前组合 API 搭建引擎的方式几乎一样：

```python
#一个检索器，类似于使用 index.as_retriever 方法生成的检索器
nl_sql_retriever = NLSQLRetriever(
    sql_database, tables=["customers","orders"], return_raw=True
)

#直接构造 RetrieverQueryEngine 查询引擎
query_engine = RetrieverQueryEngine.from_args(nl_sql_retriever)
response = query_engine.query(
    "所有订单总金额是多少？"
)
print(response)
```

这段代码跑出来的结果会与前面 SQL 查询引擎的输出完全对齐。

# 8.5 多模态文档处理

前面讲到的数据接入阶段，各种连接器最终都会把不同来源的内容收敛成 Node.text，用来做嵌入和生成。实际业务里经常碰到更复杂的知识载体，尤其是图文表格混排的 PDF。这类半结构化、非结构化的多模态材料在解析、分块和向量化上难度明显上升，需要不同的抽取手段，甚至引入专用模型来配合。

# 8.5.1 多模态文档处理架构

以最典型的多模态 PDF 为例，落地时通常会结合第三方 PDF 解析工具、多模态大模型以及递归检索。下面是较为通用的处理框架，见图 8-32。

![](img/7eddb68cd925518fdc310e52e94ba9a9_295_0.png)

图 8-32

(1) 用解析工具把 PDF 里的文本 Text、表格 Table、图片 Image 等不同形态内容分门别类抽出来；文本一般转成 Markdown，表格则保留成本地或网络文档形式。
(2) 对不同形态的内容采用差异化的索引与检索策略。
(1) 文本：与普通文本知识一致，构建向量存储并检索。
(2) 表格：直接做向量检索效果往往不佳，可先用大模型生成表格摘要再用于嵌入检索，能提升命中率并帮助模型理解表格含义。检索阶段再通过递归检索把原始表格拉回用于生成。
(3) 图片：常用多模态视觉大模型如 Qwen-VL、GPT-4V 结合 OCR 来理解，分两种情形。
a. 纯文字类图片：用 OCR 转成纯文本后按普通文本索引检索。
b. 其他类型图片：用多模态模型生成摘要用于索引检索，检索后需递归拉回原图供生成使用。
(3) 把检索到的相关知识交由大模型生成答案。注意若答案需要原图输入，最终生成需借助多模态大模型。

整个流程主要依赖三类技术。
(1) 文档解析与抽取。面向半结构化或结构化文档的解析，常用工具有：
① LlamaParse：LlamaIndex 提供的在线解析服务，擅长复杂 PDF 的抽取，与框架集成度高，提取时可借助大模型自动生成表格摘要，但需在线使用。
② Unstructured：非结构化数据处理平台，提供商业 API 与开源 SDK，支持 PDF/PPT 等复杂文档的清洗、语义分割、实体抽取等，功能强大但上手成本较高。OmniParse 属于同类方案。
③ Open-Parse：轻量级开源分块抽取库，支持语义分块与 OCR，易用且可与 LlamaIndex 集成，将抽取结果直接转为框架 Node。
（2）多模态视觉大模型。可选用在线的 Qwen-VL、GPT-4V，或本地部署的 LLaVA 等。若目标是提取图像文字，可走两条路：
① 使用自带 OCR 能力的多模态模型如 Qwen-VL。
② 使用专业 OCR 工具链，如加载模块后的 Unstructured、OmniParse。
（3）递归检索。7.1 节已有初步介绍，LlamaIndex 主要是通过 IndexNode 实现。检索到表格或图片的摘要 Node 后，可递归定位到原始表格或图片，再用于后续生成。

## 8.5.2 LlamaParse 实践解析

### 1. 认识 LlamaParse

LlamaParse 是 LlamaIndex 框架推出的一套高性能复杂文档在线解析 API，聚焦 PDF 这类复杂材料，能把文本、表格、图片等元素结构化抽取并封装成统一的 Node 对象，供后续嵌入与生成使用。

因其依赖在线服务，使用前需先申请官方 API Key，流程如下：
+ （1）在 LlamaIndex 官网注册登录，进入 LlamaParse 相关页。
+ （2）前往 API Key 管理区生成新 Key 并保存。
+ （3）可在官网直接上传 PDF 在线测试解析效果，见图 8-33。

![](img/7eddb68cd925518fdc310e52e94ba9a9_297_0.png)

图 8-33

下面介绍如何用 LlamaParse 的 SDK 配合其他组件完成复杂文档的导入处理。应用中使用前需要先安装独立模块：

```
> pip install llama-parse
```

### 2. 简单使用 LlamaParse

先准备一份中文 PDF，这里选用上市公司财务报告节选，包含正文与大量财务表格，如图 8-34 所示。

107,742 股、2,568,160 股，稀释每股收益在基本每股收益基础上考虑该因素进行计算。

#### 1.2.4 本集团 2023 年分季度主要财务指标

| 项目 | 2023 年<br>第一季度 | 2023 年<br>第二季度 | 2023 年<br>第三季度 | 2023 年<br>第四季度 |

| :--- | :--- | :--- | :--- | :--- |

| 营业收入 | 29,142.9 | 31,561.9 | 28,688.6 | 34,857.5 |

| 归属于上市公司普通股股东的<br>净利润 | 2,642.3 | 2,829.9 | 2,369.0 | 1,484.6 |

| 归属于上市公司普通股股东的<br>扣除非经常性损益的净利润 | 2,454.5 | 2,454.8 | 2,191.9 | 298.4 |

| 经营活动产生的现金流量净额 | 2,325.6 | 4,100.3 | 2,836.1 | 8,143.7 |

上述会计数据与本集团已披露季度报告、半年度报告相关会计数据一致。

#### 1.2.5 本集团近三年非经常性损益项目及金额

| 项目 | 2023 年 | 2022 年 | 2021 年 |

| :--- | :--- | :--- | :--- |

| 非流动资产处置收益 | 20.6 | 11.0 | 231.7 |

| 处置长期股权投资产生的投资收益 | 96.0 | (27.2) | 1,251.7 |

| 除同公司正常经营业务相关的有效套期保值业务外，持有交易性金融资产、衍生金融资产、其他非流动金融资产、交易性金融负债、衍生金融负债、其他非流动负债产生的公允价 | (337.0) | 37.7 | 7.5 |

![](img/7eddb68cd925518fdc310e52e94ba9a9_298_0.png)

为降低大模型波动影响，这里选用 OpenAI 模型与 Chroma 做底座，向量库与模型初始化代码如下：

```python
#模型
llm = OpenAI()
embedded_model = OllamaEmbedding(model_name="milkey/dmeta-embedding-zh:f16", )
Settings.llm=llm
Settings.embed_model=embedded_model

#向量存储
chroma = chromadb.HttpClient(host="localhost", port=8000)
```

使用 LlamaParse 与前面数据连接器的用法类似：先实例化解析器，再调用 load_data 把原始文档转成 Document 对象：

```
......
documents = LlamaParse(result_type="markdown", language='ch_sim').load_data("./../data/zte-report-simple.pdf")
print(f'{len(documents)} documents loaded.\n')

#打印并观察输出的 Document 对象结构
pprint.pprint(documents[0].__dict__)
```

LlamaParse 主要参数：
+ (1) result_type：指定 Document 内部内容格式，这里设为 Markdown。
+ (2) language：文档语言，这里指定为简体中文。
另外也可以把 parser 传入简单目录读取器作为自定义阅读器使用：

```
......
parser = LlamaParse(result_type="markdown", language='ch_sim')
#把parser对象作为简单目录阅读器加载时的一个自定义的文档阅读器
documents = SimpleDirectoryReader("./data",
file_extractor={".pdf":parser}).load_data()
```

查看解析后的 Document，可见 text 属性中带有明显的 Markdown 标记，如图 8-35 所示，这正是 PDF 中表格解析后的呈现形式。

```
'---\n'
'## 项目 \n'
'\n'
'| |2023年|2022年|同比增减|单位:百万元|2021年|\n'
'|---|---|---|---|---|---|\n'
'|每股计(元/股)| | | | |\n'
'|基本每股收益|1.96|1.71|14.62%| 1.47|\n'
'|稀释每股收益|1.96|1.71|14.62%| 1.47|\n'
'|扣除非经常性损益的基本每股收益|1.55|1.30|19.23%| 0.71|\n'
'|每股经营活动产生的现金流量净额|3.64|1.60|127.50%| 3.32|\n'
'|归属于上市公司股东的每股净资产|14.22|12.38|14.86%| 10.88|\n'
'|财务比率(%)| | | | |\n'
'|加权平均净资产收益率|15.19%|14.66%|上升 0.53 个百分点| 14.49%|\n'
'|扣除非经常性损益的加权平均净资产收益率|12.05%|11.19%|上升 0.86 个百分点| 7.03%|\n'
'|资产负债率|66.00%|67.09%|下降 1.09 个百分点| 68.42%|\n'
'\n'
```

图 8-35

该 Markdown 表格对应原 PDF 中如图 8-36 的内容。

| | 2023年 | 2022年 | 同比增减 | 2021年 |

| :--- | :--- | :--- | :--- | :--- |

| **每股计(元/股)** | | | | |

| 基本每股收益 | 1.96 | 1.71 | 14.62% | 1.47 |

| 稀释每股收益 | 1.96 | 1.71 | 14.62% | 1.47 |

| 扣除非经常性损益的基本每股收益 | 1.55 | 1.30 | 19.23% | 0.71 |

| 每股经营活动产生的现金流量净额 | 3.64 | 1.60 | 127.50% | 3.32 |

| 归属于上市公司股东的每股净资产 | 14.22 | 12.38 | 14.86% | 10.88 |

| **财务比率(%)** | | | | |

| 加权平均净资产收益率 | 15.19% | 14.66% | 上升 0.53 个百分点 | 14.49% |

| 扣除非经常性损益的加权平均净资产收益率 | 12.05% | 11.19% | 上升 0.86 个百分点 | 7.03% |

| 资产负债率 | 66.00% | 67.09% | 下降 1.09 个百分点 | 68.42% |

图 8-36

得到 Document 后，后续的节点切分、索引构建与查询引擎创建流程与普通文档一致：

PDF 查询失准与修复实战

```
#分割 Node，这里使用最简单的数据分割器
node_parser = SimpleNodeParser()
nodes = node_parser.get_nodes_from_documents(documents)

#嵌入与索引（用 Node 构造）
collection = chroma.get_or_create_collection(name="llamaparse_simple")
vector_store = ChromaVectorStore(chroma_collection=collection)
storage_context = StorageContext.from_defaults(vector_store=vector_store)
index = VectorStoreIndex(nodes=nodes, storage_context=storage_context)

#构造查询引擎
query_engine = index.as_query_engine(similarity_top=10, verbose=True)
```
先拿刚搭好的查询引擎做个小探路，用 PDF 里一段普通文本的概括性问题试试水，效果如图 8-37。

![](img/7eddb68cd925518fdc310e52e94ba9a9_301_0.png)
这一次返回的概括是基于文档原文的简单提炼，结论基本靠谱。接着把问题换成对 PDF 表格里具体数值的事实核查，如图 8-38。

![](img/7eddb68cd925518fdc310e52e94ba9a9_301_1.png)
引擎给出的结果是 1,431.2，而文档里标注的真实值如图 8-39。

![](img/7eddb68cd925518fdc310e52e94ba9a9_301_2.png)
很明显，答案偏了，问题到底出在哪？借助 Langfuse 看一下模型生成前实际收到的输入，如图 8-40。

```
1: {
  role: "user"
  content: "Context information is below. -------------------------- 2)|(43.2)|(177.2)|[其他符合非经常性损益定义的损益项目|2,353.6|1,556.8|1,827.7] |减：所得税影响额|339.2|338.2|617.3|[少数股东权益影响额(税后)|[(3.8)|2.8|(8.8)] |合计|1,926.2|1,913.4|3,567.8] 本集团对非经常性损益项目的确认依照《公开发行证券的公司信息披露解释性公告第 1 号—非经常性损益》(2023 年修订)的规定执行。其中，将规定中列举的非经常性损益项目界定 [项目|2023 年 原因|单位:百万元]|[----]|[----]|[----]|[软件产品增值税和退税收入]|经营性持续发生|11 431 ?] |代扣代缴个税手续费返还收入|经营性持续发生|30.9|
第二章 董事会报告 2023 年，本集团对绩在“连接+算力+能力+智力”技术进行高强度研发投,..."
  additional_kwargs: {
  }
}
```

```
Output
{
  role: "assistant"
  content: "The first quarter's operating income for ZTE Corporation in 2023 was 1,431.2 million yuan."
}
```
图 8-40

可以看出，真正喂给大模型的上下文里并没有 2023 年各季度财务指标，反而混进了别的表格信息，导致检索跑偏，如图 8-41。这正是召回不够精准引发的事实性问答错误。由此也说明，对这类复杂的 PDF，光靠简单解析是不够的。

```
1: {
  role: "user"
  content: "Context information is below.
--------------------------
2)|(43.2)|(177.2)|
|[其他符合非经常性损益定义的损益项目|2,353.6|1,556.8|1,827.7]
|减：所得税影响额|339.2|338.2|617.3|
|少数股东权益影响额(税后)|[(3.8)|2.8|(8.8)]
|合计|1,926.2|1,913.4|3,567.8]

本集团对非经常性损益项目的确认依照《公开发行证券的公司信息披露解释性公告第 1 号—非经常性损益》(2023 年修订)的规定执行。其中，将规定中列举的非经常性损益项目界定

|项目|2023 年 原因|单位:百万元|
|----|----|----|
|软件产品增值税退税收入|经营性持续发生|1,431.2|
|代扣代缴个税手续费返还收入|经营性持续发生|30.9|
|股权处置收益及公允价值变动收益|...|...|
|营业内业务|12|...|
```
图 8-41

要怎么让 PDF 里的表格具备更好的语义可检索性呢？这就需要在 8.5.1 节提到的表格处理思路上做文章。

## 8.5.3 多模态文档中的表格处理

PDF 里直接把解析出的 Markdown 原封不动嵌入，语义承载有限，后续检索很难命中。LlamaIndex 提供了专门处理复杂 Markdown 的 MarkdownElementNodeParser，能更细粒度地拆分 LlamaParse 得到的 Document 对象，把普通段落和表格分开，并对表格做语义增强。下面用一个案例演示用法，并与 8.5.2 节的结果做对比。

它跟常规切分器的区别在于，会调用大模型为表格生成摘要、描述、标题等辅助信息，并封装成索引节点 IndexNode，查询时先命中索引节点再拉取对应的表格节点一起生成。

在 8.5.2 节的例子上稍作调整：

```
省略借助 LlamaParse 解析 PDF 文档为 Document 对象的过程

#此处更改表格描述的 Prompt 模板
DEFAULT_SUMMARY_QUERY_STR = """
请用中文简要介绍表格内容。\n这个表格是关于什么的？给出一个非常简洁的摘要（想象你正在为这个表格添加一个新的标题和摘要），\n如果提供了上下文，那么请输出真实/现有的表格标题/说明。\n如果提供了上下文，那么请输出真实/现有的表格 ID。\n还要输出表格是否应该保留的信息。\n"""
node_parser = MarkdownElementNodeParser(summary_query_str=DEFAULT_SUMMARY_QUERY_STR)
nodes = node_parser.get_nodes_from_documents(documents)

#分离不同的文本 Node (TextNode) 与索引 Node (IndexNode)
base_nodes, objects = node_parser.get_nodes_and_objects(nodes)

省略此处构造 storage_context 变量的过程
index = VectorStoreIndex(
    nodes= base_nodes + objects,
    storage_context=storage_context
)
```
query_engine = index.as_query_engine(similarity_top=10, verbose=True)

主要改动是换成 MarkdownElementNodeParser 并配合索引节点的处理。内部流程大致如下，图片元素的处理另说。

1. 把 PDF 解析为 Document 对象，把正文和表格分别组织成普通文本节点 TextNode。
2. 对表格节点做特殊加工，用大模型加 Prompt 生成表格摘要、简介、标题等辅助内容，用来构造新的索引节点 IndexNode。
3. 用 get_nodes_and_objects 方法把表格内容节点分离出来，并让生成的 IndexNode 指向它们。
4. 以剩余的普通文本节点 base_nodes 和包含辅助信息的索引节点 objects 构建向量检索索引。
5. 检索时一旦命中索引节点，会自动递归取出它指向的表格内容节点参与答案生成。

因为检索时带上了由大模型生成的表格描述和摘要，命中 PDF 表格内容的精度和生成质量明显提升。再次用 MarkdownElementNodeParser 回答 8.5.2 节的财务数据问题。

这次拿到了正确结果，如图 8-42。通过 Langfuse 的执行追踪可以看到过程，如图 8-43。

![](img/7eddb68cd925518fdc310e52e94ba9a9_304_0.png)
图 8-42

![](img/7eddb68cd925518fdc310e52e94ba9a9_305_0.png)
图 8-43

注意：图中黑框里的内容并非文档原文，而是在解析阶段由大模型生成的表格摘要，属于 IndexNode 类型的节点，包含表格的总体介绍、标题以及列的说明。这些额外信息在检索阶段帮助更准地定位到表格节点，在生成阶段也能让模型更好理解表格，从而提升正确率。

## 8.5.4 多模态大模型的基础应用

下面简要介绍如何用多模态大模型处理图片。本次选用阿里巴巴的 Qwen-VL。模型在通用 OCR、视觉推理和中文文本理解上表现不错，适合对单张图片或多模态文档中的图片做理解与信息抽取。

### 1. 从图片到自然语言文本
先用简单例子感受一下图片理解与推理能力。LlamaIndex 对 Qwen-VL 的访问做了上层封装，可直接对本地或网络图片进行理解并返回结果。下面是一个可运行的示例：

```python
from llama_index.multi_modal_llms.dashscope import (
    DashScopeMultiModal,
    DashScopeMultiModalModels,
)
from llama_index.multi_modal_llms.dashscope.utils import (
    create_dashscope_multi_modal_chat_message,
    load_local_images
)
from llama_index.core.base.llms.types import MessageRole
from llama_index.core.multi_modal_llms.generic_utils import load_image_urls
import pprint
import os

#替换成自己的阿里巴巴 API Key
os.environ["DASHSCOPE_API_KEY"] = "sk-****"

#加载图片
image_documents1 = load_image_urls(["https://dashsco**.oss-cn-beijing.aliyuncs.com/images/dog_and_girl.jpeg"])
image_documents2 = load_local_images(["file:///Users/pingcy/本地开发/rag/data/xiaomi.png"])

#多模态大模型
dashscope_multi_modal_llm = DashScopeMultiModal(model_name=DashScopeMultiModalModels.QWEN_VL_PLUS)

#调用
chat_message = create_dashscope_multi_modal_chat_message(
    "请概括这两张图片中的信息",
    MessageRole.USER,
    image_documents1 + image_documents2
)
chat_response = dashscope_multi_modal_llm.chat([chat_message])
```

```python
# 打印结果
print(chat_response.message.content[0]["text"])
```
选用了两张图片，如图 8-44 所示，一张是阿里巴巴官方的测试图，一张是本地图。

![](img/7eddb68cd925518fdc310e52e94ba9a9_307_0.png)
图 8-44

代码要点说明：

1. 使用阿里巴巴多模态模型前，先在官网大模型服务平台申请 API Key，并把 DASHSCOPE_API_KEY 写入环境变量。
2. 构造 DashScopeMultiModal 对象，指定使用 Qwen-VL-Plus。
3. 通过 chat 接口对话，把加载好的图片对象和 Prompt 一并传入，多模态模型支持批量输入。

最终输出如图 8-45 所示，第二张图里模型很好地把文字识别出来，OCR 能力很关键。

第一张图是一位女士和她的狗在沙滩互动，两人坐在海边沙子上，女子伸手与狗狗击掌。

第二张图是小米手机 MIX4 的部分参数介绍：搭载骁龙 8 移动平台，内置第三代高通 AI 引擎；配备 6.67 英寸超视感屏，分辨率 3915x1440，支持 HDR；内置 4880mAh 电池，采用澎湃 P2+G1 快充；运行基于 Android 深度定制的小米 HyperOS 系统。

图 8-45

### 2. 从图片到结构化对象
如上所示，多模态模型用自然语言描述图片非常适合做嵌入与向量检索。实际使用中，还能按预设格式把图片内容结构化，比如识别图片后生成符合规范的 Python 对象。一种做法是在 Prompt 中限定输出格式，再自行转换。另一种做法是借助 LlamaIndex 的高层 API，我们仍以 Qwen-VL 演示：

```python
......
#多模态大模型
dashscope_multi_modal_llm = DashScopeMultiModal(model_name=DashScopeMultiModalModels.QWEN_VL_PLUS)

#输入图片
image_documents = SimpleDirectoryReader(input_files=["../../data/xiaomi.png"]).load_data()

#LlamaIndex 框架的部分版本在此处存在漏洞，特殊处理
for doc in image_documents:
    doc.image_url = doc.metadata["file_path"]

#定义输出的对象
from pydantic import BaseModel
class Phone(BaseModel):
    """定义对象结构"""
    name: str
    cpu: str
    battery: str
    display: str

from llama_index.core.program import MultiModalLLMCompletionProgram
from llama_index.core.output_parsers import PydanticOutputParser

#Prompt 模板
prompt_template_str = """\n{query_str}
请把结果作为一个 Pydantic 对象返回，对象格式如下：
"""
```

```python
#构造 MultiModalLLMCompletionProgram 对象
mm_program = MultiModalLLMCompletionProgram.from_defaults(
    output_parser=PydanticOutputParser(Phone),    #将对象类型传给输出解析器
    image_documents=image_documents,        #输入图片
    prompt_template_str=prompt_template_str,    #Prompt 模板
    multi_modal_llm=dashscope_multi_modal_llm,    #多模态大模型
    verbose=True,
)

#测试
response = mm_program(query_str="请描述图片中的信息。")
pprint.pprint(response.__dict__)
```
这里先定义了 Phone 对象，并在 Prompt 模板中要求模型按 Phone 格式输出，剩下的解析工作交给 MultiModalLLMCompletionProgram 封装对象来完成。执行时自动调用多模态模型 API 封装消息并生成响应，再通过输出解析器转为 Pydantic 对象。最终结果如图 8-46 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_309_0.png)

### 3. 直接嵌入图片

过去我们聊向量化，几乎都是把文字转成向量。实际上，图像、音频这些非文本模态同样可以做向量映射，只是需要专门的多模态嵌入模型来支撑。下面就以开源向量库 Chroma 内置的多模态嵌入接口为例，看看怎样把图片直接投射到同一个向量空间里：

```python
import chromadb
import pprint
import os
from chromadb.utils.embedding_functions import OpenCLIPEmbeddingFunction
from chromadb.utils.data_loaders import ImageLoader

# Chroma 向量库的多模态嵌入函数与图片加载器
embedding_function = OpenCLIPEmbeddingFunction()
image_loader = ImageLoader()

# Chroma 向量库的客户端，注意构造 collection 库时的区别
chroma_client = chromadb.HttpClient(host="localhost", port=8000)
chroma_client.delete_collection("multimodal_collection")
chroma_collection = chroma_client.get_or_create_collection(
    "multimodal_collection",
    embedding_function=embedding_function,
    data_loader=image_loader,
)

# 需要嵌入的图片列表
image_uris = sorted([os.path.join('./jpngs/', image_name) for image_name in os.listdir('./jpngs/')])
ids = [str(i) for i in range(len(image_uris))]

# 直接存储到 Chroma 向量库中，由 Chroma 向量库完成图片嵌入
chroma_collection.add(ids=ids, uris=image_uris)

retrieved = chroma_collection.query(query_texts=["很多辆汽车"],
                                    include=['data'], n_results=2)

print(retrieved['uris'])
```

下面这段示例是基于 Chroma 官方 API 编写的，用来演示其嵌入能力。落地时，你可以借此能力自建查询系统，把图片一起嵌入并检索，再把结果交给大模型生成。先准备几张要嵌入的图片，跑一下演示代码，确认输出是否符合预期即可。

## 8.5.5 多模态文档中的图片处理

多模态大模型具备看图理解的能力，所以知识文档里的图片也能被利用起来。本节介绍一种比较简便的做法：先用多模态大模型对图片进行识别并产出描述，再对描述文本做索引和检索。

我们打算按下面流程搭建一套完整的图文文档查询引擎。

1. 用 LlamaParse 深度解析 PDF，把里面的图片分离出来并存到本地。
2. 用 Qwen-VL 模型解读这些图片，把它们转成文字描述。
3. 将生成的描述文本做嵌入与索引，并组装成查询引擎。

下面分步说明这个案例的做法。因为沿用 Ollama 部署的本地大模型与嵌入模型，以及 Chroma 向量库，相关的初始化代码就省略了。测试用的 PDF 是一份图文并茂的手机介绍资料。

### 1. 解析 PDF 文档

第一步是深度解析原始 PDF，依然使用 LlamaParse 来完成，实际项目中也可以换成其他 Python 开源模块：

```python
# 将 PDF 文档解析成 json 对象与图片对象
def load_docs():
    parser = LlamaParse(language='ch_sim', verbose=True)
    json_objs = parser.get_json_result("../../../../data/xiaomi14.pdf")

    json_list = json_objs[0]["pages"]
    print(f'{len(json_list)} documents loaded.\n')

    image_list = parser.get_images(json_objs, download_path="pdf_images")
    print(f'{len(image_list)} images loaded.\n')

    return json_list, image_list
```

函数里采用了与之前不同的写法，通过 `get_json_result` 把 PDF 解析成若干 json 对象的列表，结果放在 dict 的 pages 字段中。`json_objs[0]["pages"]` 取出这个列表，记为 json_list。查看单个 json 对象的结构，大致如图 8-47 所示。

```
Key: page, Value Type: <class 'int'>
Key: text, Value Type: <class 'str'>
Key: md, Value Type: <class 'str'>
Key: images, Value Type: <class 'list'>
    Item Type Of List: <class 'dict'>
        Key: name, Value Type: <class 'str'>
        Key: height, Value Type: <class 'int'>
        Key: width, Value Type: <class 'int'>
Key: items, Value Type: <class 'list'>
    Item Type Of List: <class 'dict'>
        Key: type, Value Type: <class 'str'>
        Key: lvl, Value Type: <class 'int'>
        Key: value, Value Type: <class 'str'>
        Key: md, Value Type: <class 'str'>
```

图 8-47

可以看出，每个 json 对象都包含页码 page、文本内容 text、Markdown 格式 md、图片信息 images 以及明细项 items。

随后调用 `get_images` 方法，把解析得到的图片下载到本地目录，并返回图片对象列表 image_list，每个对象包含如图 8-48 的字段。

```json
{
    'height': 1001,
    'job_id': '32d299c1-0423-4552-9981-062b1701ad83',
    'name': 'img_p0_1.png',
    'original_pdf_path': '../..//data/xiaomi14.pdf',
    'page_number': 1,
    'path': 'pdf_images/32d299c1-0423-4552-9981-062b1701ad83-img_p0_1.png',
    'width': 1245
}
```

图 8-48

对象里带有下载后的真实路径，方便后续处理。

### 2. 处理文本

先把 PDF 文本部分生成 TextNode，供后面嵌入检索用。以下代码读取 json 对象里的文本并构造 TextNode：

```python
def get_text_nodes(json_list: List[dict]):
    text_nodes = []
    for idx, page in enumerate(json_list):
        text_node = \
            TextNode(text=page["text"], metadata={"page": page["page"]})
        text_nodes.append(text_node)
    return text_nodes
```

逐个取出 json 对象的 text 属性构造节点，最后把所有文本节点汇总成列表返回。

## 3. 处理图片

前面已经把 PDF 解析出的图片下载下来并生成了图片对象列表。接下来用多模态大模型逐张理解图片并产出详细描述，这些描述文本会转成图片对应的 Node，再和文本 Node 一样做嵌入索引：

```python
#定义图片转换为文本的方法(使用 Qwen-VL 模型,参考 8.5.4 节的内容)
def get_text_of_image(image_path):
    mm_llm = \
        DashScopeMultiModal(model_name=DashScopeMultiModalModels.QWEN_VL_PLUS)
    image = load_local_images(["file:///" + image_path])

    #调用多模态大模型
    chat_message_local = create_dashscope_multi_modal_chat_message(
        "请详细描述图片中的信息,包括图片中的文字和图像。",
        MessageRole.USER,
        image
    )
    chat_response = mm_llm.chat([chat_message_local])
    return chat_response.message.content[0]["text"]
```

```python
#生成图片对应的 TextNode
def get_image_text_nodes(image_list: List[dict]):
    img_text_nodes = []
    for idx, image in enumerate(image_list):
        response = ''

        #使用多模态大模型理解图片并生成文本
        response = get_text_of_image(image["path"])
        text_node = \
            TextNode(text=str(response), metadata={"path": image["path"]})
        img_text_nodes.append(text_node)
    return img_text_nodes
```

可以自行测试该函数，打印图片信息与生成的描述，对照图 8-49 的原图与文本，效果还是比较准确的。

![](img/7eddb68cd925518fdc310e52e94ba9a9_314_0.png)

## 4. 构造主查询引擎

文本和图片都转成 Node 后，就能按常规流程嵌入与索引。最直接的方式是用 Node 列表构建 VectorStoreIndex 类型的向量存储索引，再基于它创建默认查询引擎：

```python
def create_engine():

    #调用一次上面的方法解析文档、生成文本 Node、生成图片 Node
    (json_list,image_list) = load_docs()
```

```python
text_nodes = get_text_nodes(json_list)
img_text_nodes = get_image_text_nodes(image_list)

#定义向量存储
collection = chroma.get_or_create_collection(name="llamaparse_mm")
vector_store = ChromaVectorStore(chroma_collection=collection)
storage_context = StorageContext.from_defaults(vector_store=vector_store)

#用文本 Node 和图片 Node 生成索引
index = VectorStoreIndex(
    nodes=text_nodes + img_text_nodes,
    storage_context=storage_context
)

#构造查询引擎
query_engine = index.as_query_engine(similarity_top_k=5, verbose=True)
return query_engine
```

有了查询引擎，就能向原始 PDF 提问，并借助 Langfuse 平台追踪召回过程。下面是简单的测试程序：

```python
delete_collection()
query_engine = create_engine()

while True:
    query = input("\n输入你的问题 (or 'q' to quit): ")
    if query == 'q':
        break
    if query == "":
        continue
    response = query_engine.query(query)
    print(response)
```

图 8-50 显示了一次提问的返回结果。

输入你的问题 (or 'q' to quit): 介绍下小米手机的全等深微曲屏的性能参数和特点? 请用中文介绍。

******************************Response******************************

小米手机的全等深微曲屏具有以下性能参数和特点:

尺寸为6.73英寸AMOLED显示屏, 分辨率为2K超色准屏522 PPI。

峰值亮度达到3,000 nit, 使用了新一代C8发光材料。

此外, 该屏幕还具备徕卡光学全焦段四摄、Summilux镜头以及Xiaomi AISp—AI大模型计算摄影架构及全焦

![](img/7eddb68cd925518fdc310e52e94ba9a9_316_0.png)

通过 Langfuse 后台可以看到这次回答的引用上下文，如图 8-51。

![](img/7eddb68cd925518fdc310e52e94ba9a9_316_1.png)

大模型参考的上下文正是 PDF 中某张图片的描述文本，由多模态大模型生成，从而实现了对文档图片的提问与查询。案例里目前只处理了文本和图片，参照之前对 PDF 表格的处理方法，还可以进一步增强，以支持文本、表格、图片等多形态数据的检索。

# 8.6 查询管道: 编排基于Graph的RAG工作流①

> ① 在后续的 LlamaIndex 版本中, 查询管道将会逐渐被 Workflows 替代。

到现在，我们已经掌握了 RAG 流程的关键环节与组件，也能独立搭建查询或对话引擎。但在面对更复杂的应用场景时，如何用更简洁直观的方式编排 RAG 工作流是一个新问题。

本章要介绍的就是一个便捷的编排工具：查询管道 Query Pipeline。

## 8.6.1 理解查询管道

查询管道是 LlamaIndex 提供的一种声明式 API，允许用更精简的方式把不同模块串联起来，形成从简单到复杂的大模型工作流，完成查询任务。

如果你熟悉 LangChain，那么对用 LangGraph 搭建复杂大模型流程应该不陌生，查询管道的设计思路与之非常接近。

查询管道的核心抽象是 QueryPipeline。以它为中心，可以加载大模型、Prompt 模板、检索器、合成器等组件，再以“图”的方式连接起来，形成顺序链或有向无环图 DAG，从而实现基于大模型的 RAG 工作流。

图是计算机科学里的一种数据结构，可能你熟悉队列、栈、链表、树等。图则相对复杂：

- 图用于刻画多个元素及其关系，任意两个元素都可能直接关联，因此适合表达复杂关系。
- 图的基本构成是若干节点 Node/顶点及连接它们的边。
- 有向无环图指边有方向，且不存在从某节点出发经若干边回到自身的路径。

用可视化方式编排工作流会带来明显便利：

- 用更少的代码声明式定义流程，简洁且灵活。
- 代码可读性更高。
- 更容易与上层的低代码/无代码方案集成。

从 LlamaIndex 官方的一个 RAG 应用查询管道配置示例，如图 8-52，可以大致看出其运行模式。

![](img/7eddb68cd925518fdc310e52e94ba9a9_318_0.png)

这个演示里一共串联了 5 个任务模块。模块之间通过连线明确了数据流向和上下游关系，模块本身对应图的节点 Node，连线则对应边的 Edge，正是这种图化的表达让整个应用的模块构成和执行顺序一眼就能看清。

能够这么灵活地定义和编排工作流，根源在于模块化 RAG 的演进方向。从第一章就提到过，和传统 RAG 的线性流水线不同，面向复杂业务和性能优化的新范式不断涌现，于是出现了查询改写、路由分发、检索与索引、重排、多种大模型生成等更多类型的 RAG 单元。为此需要一种能自由挑选、组合和编排这些单元的机制，这就是查询管道 Query Pipeline 以及 LangGraph 类组件出现的由来。

## 8.6.2 查询管道的两种调用模式

LlamaIndex 里的查询管道提供两种使用方式，均可基于图结构搭建工作流。

### 1. 线性串行链

把若干模块按顺序排成一条链，后一个模块直接消费前一个模块的输出，例如检索器到生成器、大模型到输出解析器等。下面是一个最小示例：

```python
......
prompt_str = "请为产品设计一句简单的宣传语，我的产品是{product_name}"
prompt_tmpl = PromptTemplate(prompt_str)
llm = OpenAI()
p = QueryPipeline(chain=[prompt_tmpl, llm], verbose=True)
```

这里用 `[prompt_tmpl, llm]` 把提示模板和大模型串起来形成一条简单链，运行管道即可得到结果，输出示意如图 8-53。

![](img/7eddb68cd925518fdc310e52e94ba9a9_319_0.png)

从内部执行日志可以看到两个单元被依次触发：prompt_tmpl 先生成好 Prompt，随后 llm 拿着上游的提示继续生成最终结果。

### 2. DAG 模式

当 RAG 应用的流程变得复杂，就可以用 DAG 形态的查询管道来编排。以基于向量检索的 RAG 为例，演示如何用管道搭建查询引擎并跑通。

先按惯例准备好向量索引：

```python
......
#加载文档，构造向量存储索引
docs = SimpleDirectoryReader(input_files=["../data/xiaomai.txt"]).load_data()
index = VectorStoreIndex.from_documents(docs)
```

再用查询管道把输入 input、提示模板 prompt_tmpl、大模型 llm、检索器 retriever、生成器 summarizer 这些单元组织起来：

```python
#准备组件
input = InputComponent()
llm = Ollama(model='qwen:14b')
prompt_tmpl = PromptTemplate("对问题进行完善,输出新的问题:{query_str}")
retriever = index.as_retriever(similarity_top_k=3)
summarizer = get_response_synthesizer(response_mode="tree_summarize")

#构造一个查询管道
p = QueryPipeline(verbose=True)

#把上面构造的模块添加进来
p.add_modules(
    {
        "input": input,
        "prompt": prompt_tmpl,
        "llm": llm,
        "retriever": retriever,
        "summarizer": summarizer,
    }
)

#连接这些模块
p.add_link("input", "prompt")
p.add_link('prompt','llm')
p.add_link('llm','retriever')
p.add_link("retriever", "summarizer", dest_key="nodes")
p.add_link("llm", "summarizer", dest_key="query_str")

output = p.run(input='小麦手机的优势是什么')
```

代码可读性很高：先实例化一个管道，再通过 add_modules 把模块放进去，最后用 add_link 声明连线关系。连线部分清晰展示了各模块如何配合。运行过程与最终结果如图 8-54 所示。

```
> Running module input with input:
input: 小麦手机的优势是什么

> Running module prompt with input:
query_str: 小麦手机的优势是什么

> Running module llm with input:
messages: 请对以下问题进行改写与完善，输出新的问题：小麦手机的优势是什么

> Running module retriever with input:
input: assistant: 新问题：小麦手机与其他品牌相比有哪些独特的优点或特色？

> Running module summarizer with input:
query_str: assistant: 新问题：小麦手机与其他品牌相比有哪些独特的优点或特色？
nodes: [NodeWithScore(node=TextNode(id='ad4651b8-3b93-4217-959f-027c40d0cdbf', embedding=None, metadata={'file_': 'xiaomai.txt', 'file_type': 'text/plain', 'file_s...

小麦手机的独特优点和特色包括:
1. 环保材质: 采用环保材料, 注重减少对环境的影响。
```

图 8-54

图里完整呈现了管道的执行轨迹：按照编排的拓扑，模块按序被激活并交换数据，最终输出符合预期的答案。

## 8.6.3 查询管道内部如何运作

查询管道是构建在底层组件之上的声明式接口，它本身不做具体计算，更像一个指挥者，负责按预设的连接关系调度各个组件的输入输出，完成一次查询任务。相关组件的依赖关系见图 8-55。

![](img/7eddb68cd925518fdc310e52e94ba9a9_321_0.png)

图 8-55

这张图说明了几个关键点。

- (1) 进入管道的组件都会被统一封装成 QueryComponent。管道中所有单元都实现统一的 run_component 接口，供运行时调用。
- (2) 能够被接入管道的功能组件，如 LLM、PromptTemplate、Retriever、NodePostprocessor、Synthesizer，均需提供 as_query_component 方法，把自身转换为可被组装的 QueryComponent。
- (3) 编排时管道会维护一份 DAG 形式的内部结构。DAG 可视为一张工作流蓝图，由若干 QueryComponent 节点及节点间的连线构成。实际运行会依据这张蓝图推进。为简化图结构的操作，底层使用了 NetworkX 等图计算库。
- (4) 需要注意，查询管道本身也是一种 QueryComponent，因此可以把一个管道嵌套到另一个管道中，实现管道的层级调用。

了解上述关系后，运行机制就很清晰了。

- (1) 编排阶段：向管道添加功能组件后，组件被规范化为 QueryComponent 并暴露 run_component 接口，同时组件信息和连线关系被写入内部 DAG。
- (2) 运行阶段：管道根据 DAG 找到入口节点，调用其 run_component，随后按 DAG 指定的流向自动传递数据，直到到达末端节点或出现异常。

正因为底层用图来保存组件及其流转关系，查询管道具备很强的扩展性，未来可以承载更复杂的模块化 RAG 场景。

目前 LlamaIndex 可接入的组件已覆盖 RAG 的全流程，各组件的输入输出定义如下表 8-1，可直接参考使用，后续版本会持续扩充。

| 基础类型 | 说明 | 输入信息 | 输出类型 |
|---|---|---|---|
| LLM | LLM | prompt | CompletionResponse |
| PromptTemplate | 提示词模板 | 模板变量 | String |
| BaseQueryTransform | 转换器 | query_str | query_str |
| BaseRetriever | 检索器 | input | List[BaseNode] |
| BaseNodePostprocessor | 后处理器 | nodes/query_str | List[BaseNode] |
| BaseSynthesizer | 生成器 | nodes/query_str | Response |
| BaseOutputParser | 输出解析器 | input | 由输出解析器定义 |
| BaseQueryEngine | 查询引擎 | input | Response |
| QueryComponent | 其他查询管道 | input | 由查询管道定义 |

注意：没有单独的路由组件，路由能力已融合到检索器或查询引擎内部。

## 8.6.4 如何加入自研查询组件

当内置组件不满足需求时，可以自行开发查询组件并接入管道。LlamaIndex 提供两种做法：从 CustomQueryComponent 派生，或直接用 FnComponent 包装函数。

1. 基于 CustomQueryComponent 派生

这是最常用的自定义方式，需要实现三个接口。

（1）_input_keys：声明组件运行所需的输入参数名列表，通常在 add_link 时通过 dest_key 指定。
（2）_output_keys：声明组件运行后会输出的参数名列表。
（3）_run_component：实现具体逻辑，接收按 _input_keys 传入的 **kwargs，处理后返回符合 _output_keys 的结果。

下面用一个 output_parser 的例子说明。我们希望把最后生成的文本结构化，借助 LLmTextCompletionProgram 简化实现。完整代码如下：

```python
from llama_index.core.query_pipeline import CustomQueryComponent
from pydantic import Field,BaseModel
from llama_index.core.program import LLmTextCompletionProgram
from typing import List,Optional,Dict, Any

#自定义查询组件，可以将其插入查询管道中
class MyOutputParser(CustomQueryComponent):

    #输入校验，该方法可以不实现
    def _validate_component_inputs(
        self, input: Dict[str, Any]
    ) -> Dict[str, Any]:
        """校验组件的输入参数"""
        return input

    @property
    def _input_keys(self) -> set:
        """定义组件的输入 keys"""
        return {"response"}

    @property
    def _output_keys(self) -> set:
        """定义组件的输出 keys"""
        return {"output"}

    def _run_component(self, **kwargs) -> Dict[str, Any]:
        """定义组件的运行逻辑"""

        #在这个例子中，我们要求把查询的手机信息结构化成 Phone 类型的
        class Phone(BaseModel):
            name: str
            cpu: str
            memory: str
            storage: str
            screen: str
            battery: str
            features: List[str]

        #给大模型提示
        prompt_template_str = """\n根据以下内容提取结构化信息{input}\n"""

        #构造一个大模型的调用模块，指定 output_cls 参数
        program = LLMTTextCompletionProgram.from_defaults(
            output_cls=Phone,
            prompt_template_str=prompt_template_str,
            verbose=True,
        )

        #调用 program 变量，注意从输入中获取 key=response 的内容
        output = program(input=kwargs['response'])

        #返回对象中必须包含_output_keys 接口中定义的输出关键词
        return {"output": output}
```

定义好后即可把实例接入管道。改造前面的 DAG 示例：

```python
p = QueryPipeline(verbose=True)
p.add_modules(
    {
        "input": input,
        "prompt": prompt_tmpl,
        "llm":llm,
        "retriever": retriever,
        "summarizer": summarizer,
        'output_parser': MyOutputParser() #增加一个自定义的模块
    }
)

p.add_link("input", "prompt")
p.add_link('prompt','llm')
p.add_link('llm','retriever')
p.add_link("retriever", "summarizer", dest_key="nodes")
p.add_link("llm", "summarizer", dest_key="query_str")

#增加与自定义的模块的连接，输入的参数为 response
p.add_link("summarizer", "output_parser", dest_key="response")

output = p.run(input='小麦手机的优势是什么')
print(output)
```

新增自定义单元后，别忘了补齐连线让它参与流程。运行后查看调试信息，如图 8-56。

![](img/7eddb68cd925518fdc310e52e94ba9a9_326_0.png)

可以看到自定义的 output_parser 被正常调用：它接收 summarizer 的输出，经过处理后最终返回了 Phone 类型的结构化结果。

## 2. 用 FnComponent 快速构造

除了继承 CustomQueryComponent，还可以用更轻量的方式：用 FnComponent 把一个普通函数包装成查询组件。函数体即为运行逻辑，参数名和类型由开发者自行定义。

用 FnComponent 快速封装轻量组件

假设要基于 CustomQueryComponent 派生的自定义查询组件，在最终输出的对象上再补一些附加信息并以字符串形式呈现，可以先手写一个很小的工作函数来完成这个逻辑：

```python
from llama_index.core.query_pipeline import FnComponent

#定义一个组件函数
def addExtaInfo(phone: Phone) -> str:
    phone_info = f"Name: {phone.name}\nCPU: {phone.cpu}\nMemory: {phone.memory}\nStorage: {phone.storage}\nScreen: {phone.screen}\nBattery: {phone.battery}\nFeatures: {'，'.join(phone.features)}"
    extra_info = "Extra information: This phone has a great camera."
    phone_str = f"{phone_info}\n{extra_info}"
    return phone_str
```

写好函数后，用 FnComponent 的方式把它包装成组件，接入查询管道并与上游节点连通：

```python
......
p = QueryPipeline( verbose=True)
p.add_modules(
    {
        'output_parser': MyOutputParser(),
        #使用 FnComponent 方法添加一个新的查询组件
        'post_processor': FnComponent(fn=addExtaInfo,
        output_key="output")
    }
)
......
#增加连接
p.add_link("output_parser", "post_processor", dest_key="phone")
output = p.run(input='小麦手机的优势是什么')
```

执行后可在结果中看到自己定义的 post_processor 组件被顺利执行，并且内部的 addExtaInfo 方法确实被调用到，如图 8-57 所示。

```
> Running module post_processor with input:
phone: name='小米Pro' cpu='高通骁龙870' memory='12GB LPDDR5X' storage='256GB UFS3.1' screen='6.9英寸 AMOLED, 分辨率为3200x1440' batt
Name: 小米Pro
CPU: 高通骁龙870
Memory: 12GB LPDDR5X
Storage: 256GB UFS3.1
Screen: 6.9英寸 AMOLED, 分辨率为3200x1440
Battery: 5000mAh 大电池, 支持有线+无线双快充技术
Features: 环保材质, 健康护眼模式, 高性能处理器, 大容量电池及快速充电技术, 高像素后置摄像头 (4800万像素) 和前置摄像头 (3200万像素)
Extra information: This phone has a great camera.
```

图 8-57

用 FnComponent 来搭建查询组件的思路更精简，特别适合那种逻辑简单、追求快速验证的场景。

## 第9章 开发 Data Agent

如果你已经对 Agent 有一定了解，就会知道它是一种更进阶的应用形态，通常被视作生成式 AI 的终极形态。直观来说，Agent 是由大模型驱动，能够自主理解指令、制定计划、执行操作并最终完成任务的 AI 程序。Agent 与大模型的关系有点像人脑和整个人：大脑负责指挥行动，但真正执行任务的是完整的人。OpenAI 应用研究负责人 Lilian Weng 曾概括为 Agent = 大模型 + 记忆 + 规划能力 + 工具使用。我们可以用图 9-1 做一个简化的呈现。

![](img/7eddb68cd925518fdc310e52e94ba9a9_329_0.png)

也就是说，Agent 以大模型作“智慧大脑”，在此基础上补齐记忆、自我规划、使用工具等能力，从而形成具备自主认知与行动的完整智能体。

本章要讲的就是把 RAG 应用中搭建的模块延伸到 Agent 侧，重点介绍以数据为核心的 Data Agent 如何落地。

## 9.1 初步认识Data Agent

RAG 是一种以大模型为底座的知识密集型应用，核心形态是数据查询与对话。Data Agent 在 RAG 的能力之上加入了自我规划和工具调用，于是能承接由大模型驱动的、更丰富的读写类数据任务。它不仅能完成常规的数据检索，还能借助工具真正执行数据操作，显著拓宽了 RAG 的应用边界。

相对 RAG，Data Agent 额外具备：

- （1）保留 RAG 的检索与查询生成能力。
- （2）通过观察环境和任务目标，推导出下一步数据任务的执行步骤。
- （3）调用外部服务工具、API 等完成复杂任务并返回结果。
- （4）同时拥有长期记忆，如向量库，以及短期记忆，如单次任务内的交互历史。

因此，相较 RAG，Data Agent 的核心增强点有两方面。

- （1）任务规划与推理能力。
在 LlamaIndex 中主要由 Agent 组件承载，其借助大模型做循环推理，规划执行顺序、选择工具、生成工具参数并完成调用。常见范式是 ReAct，即推理-行动。

- （2）工具定义与使用能力。
在 LlamaIndex 中由 Tool 相关组件实现。每个工具通常是一个具备规范请求参数和返回参数的 API 或函数，请求参数多为结构化字段，返回可以是文本或其他格式。框架提供了便捷方式来定义 Agent 可用的工具，也支持把已有可运行组件包装成工具，例如查询引擎。

图 9-2 展示了 LlamaIndex 官方对推理组件与工具组件协作关系的示意。

![](img/7eddb68cd925518fdc310e52e94ba9a9_331_0.png)

图 9-2

当前版本主要支持的 Agent 类型：

（1）OpenAI Agent：面向具备函数调用能力的大模型。
（2）ReAct Agent：面向其他大模型，基于 ReAct 范式做规划与推理。

框架在工具侧支持的组件：

（1）FunctionTool：把普通函数转为 Agent 可用的工具。
（2）BaseTool 与 ToolMetadata：工具的基础抽象与元数据定义。
（3）QueryEngineTool：把查询引擎转为 Agent 可用的工具。
（4）SlackToolSpec：工具规格定义，可直接转为 Agent 可用工具。
（5）工具库：在 LlamaHub 上发布的众多开箱即用的外部工具。

## 9.2 构造与使用Agent的工具

工具相关组件的职责是把可被 Agent 调用且符合调用规范的各种能力封装起来。在 Agent 体系里，工具可以是：

（1）本地自定义函数。
（2）查询引擎。
（3）查询管道。
（4）检索器。
（5）另一个 Agent。
（6）企业内部应用的开放接口。
（7）第三方公开 API。

LlamaIndex 提供了大量“工具化”能力，让上述组件都能被 Agent 直接使用。

### 9.2.1 深入了解工具类型

工具的基础抽象是 BaseTool，核心要素包括：

（1）metadata：工具的元数据，包含名称、描述、接口规范、是否直接返回等。元数据是帮助大模型理解用途并推理调用的关键信息，部分字段在构造时可自动生成。
（2）call()：必须实现的调用接口，是工具执行的核心逻辑。

目前几种工具类型与对应组件的关系如图 9-3：

![](img/7eddb68cd925518fdc310e52e94ba9a9_332_0.png)

先做一个整体说明，后续会结合示例展开。

（1）FunctionTool：函数工具，把本地函数直接转成工具，是最简也是最灵活的类型，可实现任意无状态逻辑。
（2）QueryEngineTool：查询引擎工具，把已有的查询引擎发布为工具供 Agent 使用。
（3）RetrieverTool：检索工具，把已有的检索器发布为工具，并支持挂载节点后处理器。主要作用是按输入字符串从索引中检索相关 Node 并做后处理后输出内容。
（4）QueryPlanTool：查询计划工具，接收多个子工具作为输入，被调用时按传入的执行计划完成任务并返回结果。
（5）OnDemandLoaderTool：按需加载工具，借助指定文档加载器加载数据并构建索引，再根据问题查询相关数据。

### 9.2.2 函数工具

函数工具的使用非常直接：

```python
from llama_index.core.tools import FunctionTool

def add(a: int, b: int) -> int:
    return a + b

# Create a tool from the function
tool_add = FunctionTool.from_defaults(
    fn=add,
    name="tool_add",
    description="用于两个整数相加",
)
```

直接调用该工具验证效果：

```python
output = tool_add.call(a=1,b=3)
print(type(output))
print(f"Output: {output.__dict__}")
```

得到的结果类似：

```
<class 'llama_index.core.tools.types.ToolOutput'>
```

输出: {'content': '4', 'tool_name': 'add', 'raw_input': {'args': (), 'kwargs': {'a': 1, 'b': 3}}, 'raw_output': 4, 'is_error': False}

可看出调用后的类型为 ToolOutput，内部包含 content 即函数返回内容、tool_name 工具名、raw_input 输入参数、raw_output 原始输出等信息。

函数工具本质是把自定义函数打包成 Tool 对象，调用时把请求转为函数调用，并以标准 ToolOutput 形式返回结果。

## 9.2.3 查询引擎工具

下面以之前构建的一个查询引擎为例演示用法：

```python
......准备索引......

#构造查询引擎
query_engine = index.as_query_engine(response_mode="compact",verbose=True,text_qa_template=qa_prompt)

from llama_index.core.tools import QueryEngineTool, ToolMetadata

#构造查询引擎工具
tool_xiaomai = QueryEngineTool.from_defaults(
    query_engine=query_engine,
    name="tool_xiaomai",
    description="用于小麦手机信息查询",
    return_direct=False
)

#测试工具
print(tool_xiaomai.call(query_str="小麦手机采用了什么型号的CPU? "))
```

需要留意的是，LlamaIndex 中的 Agent 本身也继承自 QueryEngine，所以 Agent 也可以像查询引擎一样被包装成工具，供别的 Agent 调用，实现 Agent 间的互调。

## 9.2.4 检索工具

什么时候会用到检索工具？回想第 8 章的路由检索器，它本质上是一个简易 Agent，利用大模型判断问题并决定使用哪个检索器。此时检索器就需要先用检索工具打包，供路由检索器调用。下面是一个构造示例：

```python
vector_index = VectorStoreIndex(nodes)

#首先构造一个检索器
retriever_xiaomai = vector_index.as_retriever(similarity_K=2)

#用检索器构造一个检索工具
tool_retriever_xiaomai = RetrieverTool.from_defaults(
    retriever=retriever_xiaomai,
    description="用于检索小麦手机的信息",
)

print(tool_retriever_xiaomai.call(query_str="小麦手机采用了什么型号的CPU? "))
```

观察调用结果，会输出两个检索到的 Node 的元数据与内容，如图 9-4 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_335_0.png)

图 9-4

## 9.2.5 查询计划工具

查询计划工具是基于其他工具的上层调度工具。它接收一组子工具作为输入，调用时按照传入的执行计划，即工具调用的顺序与关系，完成任务。

配合大模型构建 Agent 后，可以让 Agent 根据已有工具自行生成执行计划，再交给查询计划工具执行；也可以提前设计好复杂计划，直接交给查询计划工具，减少大模型在多工具间自行规划带来的不确定性。

下面模拟构造一个包含两个子工具的查询计划工具，并传入一个执行计划进行调用：

```python
#省略数据加载
query_xiaomai = index1.as_query_engine(response_mode="compact")
query_ultra = index2.as_query_engine(response_mode="compact")

#构造两个子工具：简单的查询引擎工具
query_tool_xiaomai = QueryEngineTool.from_defaults(
    query_engine=query_xiaomai,
    name="query_tool_xiaomai",
    description="提供小麦手机普通型号 Pro/Max 的信息")

query_tool_ultra = QueryEngineTool.from_defaults(
    query_engine=query_ultra,
    name="query_tool_ultra",
    description="提供小麦手机 Ultra 的信息")

from llama_index.core.tools import QueryPlanTool
from llama_index.core import get_response_synthesizer
from llama_index.core.tools.query_plan import QueryPlan, QueryNode

#构造一个查询计划工具，并传入子工具与响应生成器
response_synthesizer = get_response_synthesizer()
```

```python
query_plan_tool = QueryPlanTool.from_defaults(
    query_engine_tools=[query_tool_xiaomai, query_tool_ultra],
    response_synthesizer=response_synthesizer,
)

#构造一个执行计划，执行计划由多个执行 Node 组成
nodes=[
    QueryNode(
        id=1,
        query_str="查询小麦手机普通型号 Pro 的信息",
        tool_name="query_tool_xiaomai",
        dependencies=[]
    ),
    QueryNode(
        id=2,
        query_str="查询小麦手机 Ultra 的信息",
        tool_name="query_tool_ultra",
        dependencies=[1]
    ),
    QueryNode(
        id=3,
        query_str="对比小麦手机普通型号 Pro 与小麦手机 Ultra 的配置区别",
        tool_name="vs_tool",
        dependencies=[1,2]
    )
]

#调用查询计划工具，并传入执行计划
output = query_plan_tool(nodes=nodes)
print(output)
```

本次演示的编排逻辑是先由两个查询引擎各自检索两款手机的基础资料，待这两个子任务均完成后再执行第三步的配置对比。实际运行的日志显示，查询计划组件严格按照预设顺序完成调度，并给出了符合预期的结果，详见图 9-5。

Selected Tool: ToolMetadata(description='提供小麦手机普通型号Pro/Max的信息', name='query_tool_xiaomai', fn_schema='llama_index.core.tools.types.DefaultToolFnSchema', return_direct=False)

Executed query, got response.

Query: 查询小麦手机普通型号Pro的信息

Response: 小麦手机普通型号Pro的核心参数如下：
- 屏幕为6.5英寸全面屏，分辨率达到2400×1080像素
- 采用高通骁龙870芯片
- 内存提供8GB/12GB LPDDR5选项
- 存储提供128GB/256GB UFS 3.1选项
- 电池容量4500mAh，支持33W快充
- 摄像头后置4800万像素主摄+800万像素超广角+200万像素微距，前置1600万像素自拍
- 系统基于Android 11的Magic UI 4.0

Selected Tool: ToolMetadata(description='提供小麦手机Ultra的信息', name='query_tool_ultra', fn_schema=<class 'ex.core.tools.types.DefaultToolFnSchema'>, return_direct=False)

Executed query, got response.

Query: 查询小麦手机Ultra的信息

Response: 小麦手机Ultra是一款智能手机，主要规格如下：
- 屏幕为6.9英寸AMOLED全面屏，分辨率3200×1440像素，刷新率支持120Hz
- 搭载高通骁龙8 Gen 1处理器
- 内存为12GB/16GB LPDDR5
- 存储为256GB/512GB UFS 3.1，可扩展至1TB
- 电池容量6000mAh，支持65W超级快充
- 摄像头后置1亿像素主摄+1600万像素超广角+500万像素微距+200万像素景深，前置3200万像素自拍
- 系统基于Android 13的Magic UI 6.0
- 其他特性包括IP68级防水防尘、5G网络、屏下指纹识别、面部识别、双扬声器、Hi-Res音质认证、无线充电与反向充电

Executed query, got response.

Query: 对比小麦手机普通型号Pro与小麦手机Ultra的配置区别

Response: 两款机型的配置差异可归纳为：
1. 屏幕尺寸与分辨率存在差异
2. 处理器型号不同
3. 内存容量不同
4. 存储容量及扩展能力不同
5. 电池容量与快充功率不同
6. 摄像头像素与组合不同
7. 系统版本不同
8. Ultra额外具备IP68防水防尘、5G支持、屏下指纹、面部识别、双扬声器、Hi-Res认证、无线充电与反向充电等功能

图 9-5

## 9.2.6 按需加载工具

按需加载工具的核心做法是调用时自动触发数据加载器读取文档，并顺带完成索引构建、查询引擎搭建直至返回查询结果。可以理解为一次调用即从原始文档开始完整搭建查询链路并输出答案。

由于该流程包含了即时的引擎构建，数据规模偏大时响应会明显变慢，所以仅适用于少量、偶发需要动态加载的场景。

以下以网页数据源为例进行验证：

```python
def _baidu_reader(
    soup: Any, url: str, include_url_in_text: bool = True
) -> Tuple[str, Dict[str, Any]]:
    #此处省略对 Web 网页解析的逻辑, 可参考第 5 章
    return text, {"title": soup.find(class_="post__title").get_text()}

# 构造一个数据加载器
web_loader = BeautifulSoupWebReader(website_extractor={"cloud.baidu.com": _baidu_reader})

# 用数据加载器构造一个按需加载工具
tool_xiaomai = OnDemandLoaderTool.from_defaults(
    web_loader,
    name="tool_xiaomai",
    description="用于查询本地文档中的小麦手机信息",
)

# 调用这个工具测试，由于 web_loader 对象也需要参数，因此需要增加 urls 参数
output = tool_xiaomai.call(
    urls=["https://cloud.baidu.com/doc/AppBuilder/s/6lq7s8lli"],
    query_str='百度云千帆 appbuilder 是什么？'
)

print(output)
```

web_loader 需要以URL列表作为输入，因此在调用 call 时需同时传入按需加载的数据源地址与具体查询问题。

## 9.3 基于函数调用功能直接开发智能体

不少大模型都具备函数调用能力，能依据输入自动判断是否需要调用函数，并返回函数名与参数等调用指令。将函数视作工具时，模型本身就拥有使用推理工具的能力，因此可以借助函数调用能力直接搭建简易智能体，无需额外组件。

下面以OpenAI大模型的函数调用为例演示构建过程，帮助理解其运行原理。

### 1. 准备工具

首先准备三个模拟工具，分别用于查询天气、发送邮件和查询客户信息，这三个工具后续会继续被使用。

```python
# 工具：搜索天气情况
def search_weather(query: str) -> str:
    """用于搜索天气情况"""
    # Perform search logic here
    search_results = f"明天晴转多云，最高温度 30℃，最低温度 23℃。天气炎热，注意防晒哦。"
    return search_results

tool_search = FunctionTool.from_defaults(fn=search_weather)

# 工具：发送电子邮件
def send_email(subject: str, recipient: str, message: str) -> None:
    """用于发送电子邮件"""
    # Send email logic here
    print(f"邮件已发送至 {recipient}，主题为 {subject}，内容为 {message}")

tool_send_mail = FunctionTool.from_defaults(fn=send_email)

# 工具：查询客户信息
def query_customer(phone: str) -> str:
    """用于查询客户信息"""
    # Perform creation logic here
    result = f"该客户信息为:\n姓名：张三\n电话：{phone}\n地址：北京市海淀区"
    return result

tool_generate = FunctionTool.from_defaults(fn=query_customer)
```

### 2. 开发智能体

下面搭建一个智能体，它会根据自然语言输入自动挑选合适的工具并执行调用：

```python
......
#定义一个 OpenAI 的 Agent
class MyOpenAIAgent:

    #初始化参数
    #tools: Sequence[BaseTool] = [], 工具列表
    #llm: OpenAI = OpenAI(temperature=0, model="gpt-3.5-turbo"), OpenAI 的大模型
    #chat_history: List[ChatMessage] = [], 聊天历史
    def __init__(
        self,
        tools: Sequence[BaseTool] = [],
        llm: OpenAI = OpenAI(temperature=0, model="gpt-3.5-turbo"),
        chat_history: List[ChatMessage] = [],
    ) -> None:
        self._llm = llm
        self._tools = {tool.metadata.name: tool for tool in tools}
        self._chat_history = chat_history

    #重置聊天历史
    def reset(self) -> None:
        self._chat_history = []

    #定义聊天接口
    def chat(self, message: str) -> str:
        chat_history = self._chat_history
        chat_history.append(ChatMessage(role="user", content=message))

        #传入工具
        tools = [
            tool.metadata.to_openai_tool() for _, tool in self._tools.items()
        ]
        ai_message = self._llm.chat(chat_history, tools=tools).message
        additional_kwargs = ai_message.additional_kwargs
        chat_history.append(ai_message)

        #获取工具调用的要求
``````python

tool_calls = additional_kwargs.get("tool_calls", None)

# 如果调用工具，那么依次调用

if tool_calls is not None:

    for tool_call in tool_calls:

        # 调用函数

        function_message = self._call_function(tool_call)

        chat_history.append(function_message)

        # 继续对话

        ai_message = self._llm.chat(chat_history).message

        chat_history.append(ai_message)

    return ai_message.content

# 调用函数

def _call_function(

    self, tool_call: ChatCompletionMessageToolCall

) -> ChatMessage:

    id_ = tool_call.id

    function_call = tool_call.function

    tool = self._tools[function_call.name]

    output = tool(**json.loads(function_call.arguments))

    return ChatMessage(

        name=function_call.name,

        content=str(output),

        role="tool",

        additional_kwargs={

            "tool_call_id": id_,

            "name": function_call.name,

        },

    )

```

上述代码把工具调用规格一并传给OpenAI模型进行推理，模型返回调用指令后执行对应函数，拿到结果再生成最终答案。

## 3. 与智能体连续对话

可通过以下代码实现多轮对话：

```python

while True:

    user_input = input("请输入您的消息: ")

    if user_input.lower() == "quit":

        break

    response = agent.chat(user_input)

    print(response)

```

最终输出如图 9-6 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_343_0.png)

图 9-6

对照工具定义可确认，最终答案确实融合了工具返回内容，说明智能体正确完成了工具推理与调用。

## 9.4 用框架组件开发智能体

借助封装好的智能体组件，可快速将工具与大模型组合成可直接使用的智能体。LlamaIndex框架中的智能体组件主要分为基于函数调用的OpenAIAgent与基于ReAct推理范式的ReActAgent。

### 9.4.1 使用 OpenAIAgent

9.3节使用原生OpenAI API搭建了智能体，若改用LlamaIndex封装的OpenAIAgent，代码会大幅简化：

```python

# ·····定义工具，同上省略·····

from llama_index.agent.openai import OpenAIAgent

from llama_index.llms.openai import OpenAI

llm = OpenAI(model="gpt-3.5-turbo")

agent = OpenAIAgent.from_tools(

    [tool_search, tool_send_mail, tool_generate], llm=llm,

    verbose=True

)

# ·····测试代码·····

```

沿用9.3节的测试方式，输出结果如图 9-7 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_344_0.png)

图 9-7

通过verbose参数可以观察智能体内部的决策过程，包括是否需要调用函数、调用的函数输入输出以及大模型的最终生成结果。可见OpenAIAgent的使用效果与基于函数调用的手工实现一致，但更简洁。

### 9.4.2 使用 ReActAgent

若所用模型不支持函数调用，也可采用基于ReAct推理范式的智能体组件来开发。

搭建ReActAgent类型智能体非常简便：

```python

from llama_index.core.agent import ReActAgent

from llama_index.llms.openai import OpenAI

# LlamaIndex Agent 工具调度与分步实践

让大模型在真实任务里发挥作用，核心在于把工具发现、推理推进和外部知识补充串成完整回路。下面通过几组典型实现，从最简 ReAct 到可人工干预的交互流程，展示常用的落地思路，方便直接复现。

**ReAct 基础连通**
先把大模型和可调用工具接起来，打开 verbose 就能看到每一步的推理过程，适合快速验证思路。
llm = OpenAI(model="gpt-3.5-turbo")

agent = ReActAgent.from_tools(

    [tool_search, tool_send_mail, tool_generate], llm=llm,

    verbose=True

)
```

只需要简单地把 9.4.1 节代码中的 OpenAIAgent 修改成 ReActAgent 即可，下面观察内部的跟踪信息，会发现在推理过程上有些不同，如图 9-8 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_345_0.png)

ReActAgent 在进行推理循环时，并非依赖大模型的函数调用功能，而是通过 ReAct 推理范式来决定使用的工具。这种推理范式需要的 Prompt 模板大致如下：

```text

......

您可以使用以下工具:

{tool_desc}
**Worker 分步执行**
当任务需要分多个阶段拆解并回溯中间结果时，结合 OpenAIAgentWorker 与 AgentRunner 能更清晰地控制每一步的进展。
```

要回答问题，请使用以下格式。

思考：我需要使用一个工具来帮助我回答这个问题。
行动：工具名称（{tool_names} 之一）
行动输入：工具的输入，采用表示 kwargs 的 JSON 格式（例如 `{"text": "hello world", "num_beams": 5}`）

请对操作输入有效的 JSON 格式。不要这样做 `{'text': 'hello world', 'num_beams': 5}`。
如果使用此格式，您将收到以下格式的响应：

观察：工具响应

......

基于这样的 Prompt 模板，大模型会遵循思考—行动—观察这样的推理方式来规划执行任务的步骤，并一步步地执行行动步骤（调用工具），直到完成任务（Data Agent 中的任务一般是能够完整与准确地回答输入的问题）。

### 9.4.3 使用底层 API 开发 Agent

LlamaIndex 框架中的 Agent 的基础类型是 AgentRunner。其作为顶级的协调器，可以构造任务、运行任务中的每一步或端到端地运行任务、保存状态并跟踪任务，而任务中每一步的真正执行者都是在 AgentRunner 内部构造的 AgentWorker 组件，它负责运行规划出来的任务步骤（step），并返回每一步的输出结果。AgentWorker 并不保存任务状态，只负责执行任务，而 AgentRunner 则负责调用 AgentWorker 并收集与聚合每一步的结果。LlamaIndex 官方给出的 AgentRunner 与 AgentWorker 之间的关系如图 9-9 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_347_0.png)

图 9-9

因此，你可以组合构造 AgentWorker 与 AgentRunner 这两个组件，并使用相关的辅助组件更细粒度地调度与运行 Agent 任务。这种方式带来的好处如下：分离任务的构造与执行、获得更精细的观察结果、控制与调试任务的每一步、及时地取消任务、定制 AgentWorker 等。

下面使用 AgentWorker 与 AgentRunner 组合构造一个 OpenAIAgent:

```python

......

from llama_index.core.agent import AgentRunner

from llama_index.agent.openai import OpenAIAgentWorker

llm = OpenAI(model="gpt-3.5-turbo")

openai_step_engine = OpenAIAgentWorker.from_tools(

    tools,

    llm=llm,

    verbose=True)

agent = AgentRunner(openai_step_engine)

......
**向量索引筛选工具**
工具规模变大时一次性传入列表不够高效。把工具转成对象索引，利用向量相似度在运行时动态召回，调用更轻便。
```

在这里的代码中，先构造了一个 OpenAIAgentWorker 类型的工作器组件，然后把它交给一个 AgentRunner，这样就成功地开发了一个 Agent。用这种方式开发 Agent 完全等价于直接用 OpenAIAgent 开发 Agent。

### 9.4.4 开发带有工具检索功能的 Agent

使用工具集 (Tools) 是 Agent 的必备能力。如果一个 Agent 携带的工具集过大，那么会怎么样呢？这可能会导致推理能力下降甚至工具使用错乱（这与大模型的推理能力相关）。因此，如果在使用工具之前，能够根据输入的任务语义对工具进行一次检索与过滤，缩小工具集，那么可以大大降低任务执行过程中对工具使用的推理发生错误的概率。

LlamaIndex 框架中提供了检索工具的方法，最常见的是给工具对象构造对象索引，在运行任务时动态检索相关的工具，然后在候选工具中进一步选择，这样就可以大大缩小选择工具的范围，降低推理的复杂性，从而降低推理的不确定性。

我们继续修改前面的 Agent 例子，这次不再把所有工具直接提供给 Agent，而是给工具对象构造对象索引，然后把索引的工具检索器提供给 Agent：

```python

# 准备 3 个工具：搜索天气情况、发送电子邮件、查询客户信息，省略……

tools = [tool_search, tool_send_mail, tool_customer]

# 构造对象索引，在底层使用向量检索

obj_index = ObjectIndex.from_objects(

    tools,

    index_cls=VectorStoreIndex,

)

# 大模型

llm = OpenAI(model="gpt-3.5-turbo")

# 开发 Agent，注意提供 tool_retriever (工具检索器)，而不是工具集

agent = OpenAIAgent.from_tools(

    tool_retriever=obj_index.as_retriever(similarity_top_k=2),

    verbose=True

)

# 测试：使用 agent_worker 对象检索工具集，查看结果

tools = agent.agent_worker.get_tools('发送电子邮件')

for tool in tools:

    print(f'Tool name: {tool.metadata.name}')

    print(tools)
查询“发送电子邮件”时的返回样例：
```

工具的真正使用者是 AgentWorker 组件，所以最后的测试代码使用 agent_worker 的 get_tools 方法模拟运行时的工具检索动作。输出结果如下：

```text

Tool name: send_email

Tool name: query_customer
换成“查询天气”时的召回结果：
```

通过索引检索出的第一个工具是 send_email, 这正是实际需要使用的工具。如果把检索的输入任务修改为 “查询北京明天的天气”, 就会看到输出结果发生了以下变化:

```text

Tool name: search_weather

Tool name: query_customer
对话调用示例：
```

检索出的第一个工具变成了 search_weather, 这间接证明了工具检索的有效性。

下面正常调用 Agent 以测试其能力:

```python

agent.chat('帮我查询 1865120××××的客户信息')
**上下文检索增强**
除了工具，还可以接入额外的上下文检索器，用来解释缩写或业务规则等隐性信息，提升理解能力。
```

从图 9-10 所示的输出结果中可以看到, Agent 成功地检索出 query_customer 工具并完成调用, 最后根据调用的结果输出了答案。

![](img/7eddb68cd925518fdc310e52e94ba9a9_349_0.png)

图 9-10

### 9.4.5 开发带有上下文检索功能的 Agent

通过对象索引可以缩小选择工具的范围, 从而方便 Agent 选择更准确的工具。在实际应用中, 还有另一种可能的情况: 工具的数量不太太多, 但是使用各个工具完成的工作内容容易混淆甚至相似，工具并不能简单地用元数据中的描述信息（Description）来区分，这可能会导致选择工具出错。这时，可以通过提供增强的上下文进行提示：给 Agent 提供一个独立的上下文检索器，使得 Agent 可以在推理行动时借助上下文检索器检索出与输入问题相关的上下文（Context），而这里的上下文可以帮助 Agent 选择正确的工具，从而能够降低选择工具出错的概率。

下面看一个简单的例子来理解上下文检索器的作用：

```python

# 对上下文建立索引，这里的上下文是一些对财务术语缩写的解释

texts = [

    "Abbreviation: X = Revenue",

    "Abbreviation: YZ = Risk Factors",

    "Abbreviation: Z = Costs",

]

docs = [Document(text=t) for t in texts]

# 一个上下文索引

context_index = VectorStoreIndex.from_documents(docs)

# 开发一个带有上下文检索功能的 Agent

context_agent = ContextRetrieverOpenAIAgent.from_tools_and_retriever(

    query_engine_tools,                     # 正常的工具列表

    context_index.as_retriever(similarity_top_k=1), # 传入上下文检索器

    verbose=True)
带缩写的提问也能得到正确解析：
```

这里开发的 Agent 与 9.4.4 节开发的带有工具检索功能的 Agent 类似，也传入了一个检索器，但是其作用是有区别的：工具检索器用于从多个工具中筛选出可用的相关工具，而上下文检索器用于检索出有助于选择工具的相关上下文知识。一个用于缩小选择范围，另一个用于增加提示信息。

这里的上下文中包含了一些对财务术语缩写的解释，因此如果输入问题中涉及财务术语的缩写，那么检索出的相关上下文就可以帮助大模型更准确地理解用户的输入问题，从而选择正确的工具，比如输入问题为：

```python

response = context_agent.chat("What is the YZ of March 2022?")
**任务拆分与循环控制**
对较长任务用 create_task 结合 run_step 的方式，可以自行决定循环节奏并查看每一步输出。
```

我们观察跟踪信息，可以看到，大模型在选择工具时的提示信息如图 9-11 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_351_0.png)

可以看到，上下文检索器检索出的提示信息（“Abbreviation: YZ = Risk Factors”）被插入输入的提示信息中，从而帮助大模型做出正确的选择，而最终产生的函数调用信息（Calling Function 部分）也证明了这里检索出的提示信息的作用。

## 9.5 更细粒度地控制 Agent 的运行

我们可以使用 LlamaIndex 框架中与 Agent 相关的组件快速开发一个 Agent，使其能够利用工具（在本书中主要是 RAG 查询工具）来完成复杂的查询任务，但仍然存在一个潜在的问题，就是 Agent 的任务执行过程由于被高度封装而缺乏透明度与可控性。在很多时候，Agent 的运行会给人一种“摸盲盒”的感觉，更多地依赖其背后的大模型的理解与推理能力。在 Agent 运行的过程中，你很难观察、纠正错误，甚至介入任务步骤。像编程一样，在很多时候，你需要一个“分步调试”的功能让 Agent 的运行更可控。

### 9.5.1 分步可控地运行 Agent

如果观察 agent.chat 接口的实现，那么可以看到 Agent 内部任务的运行过程，主要由 AgentRunner 与 AgentWorker 两个组件协调完成任务：AgentRunner 先构造任务（create_task），然后进入一步步运行任务的循环（run_step），而具体步骤的执行由 AgentWorker 主导完成，直至 AgentWorker 告诉 AgentRunner 已经运行结束（is_last=True）。因此，你可以调用这些方法来分步运行 Agent。下面继续用之前的 Agent 例子来演示如何分步运行 Agent，核心代码如下：

```python

# 省略构造工具的代码

tools = [tool_search, tool_send_mail, tool_customer]

agent = OpenAIAgent.from_tools(tools, llm=llm, verbose=True)

# 构造一个任务

task = agent.create_task("明天南京天气如何？")

# 运行这个任务

print('\n---------------')

step_output = agent.run_step(task.task_id)

pprint.pprint(step_output.__dict__)

# 循环，直到 is_last = True

while not step_output.is_last:

    print('\n---------------')

    step_output = agent.run_step(task.task_id)

    pprint.pprint(step_output.__dict__)

# 最后输出结果

print('\nFinal response:')

response = agent.finalize_response(task.task_id)

print(str(response))
完成后可以回溯全部步骤：
```

终端上的输出结果如图 9-12 所示。

## 第 9 章 Agent 开发

### 9.5.1 Agent 运行过程分析

我们可以很清楚地观察到 Agent 运行的内部过程 (由于使用了 OpenAI Agent，因此通过调用函数来使用工具)。

1. AgentWorker 推理出使用工具 `search_weather` with args。
2. 调用工具并获得了输出结果，且这时输出参数中 `is_last` 为 False，AgentRunner 要求继续运行。
3. AgentWorker 根据之前的调用工具结果判断可以回答问题，因此做出最终响应，设置 `is_last` 为 True 后返回。
4. AgentRunner 判断 AgentWorker 的输出参数中 `is_last` 为 True，结束循环，调用 `finalize_response` 方法获得最终结果。

你也可以用以下方法查看所有运行步骤的详细信息：

```python

steps = agent.get_completed_steps(task.task_id)

for i, step in enumerate(steps):

    print(f'\nStep {i+1}:')

    print(step.__dict__)
**可交互的人工介入流程**
城市查询工具可以按字典批量生成，AgentRunner 支持在中间步骤插入人工反馈，适合需要确认的场景。
```

打印出的运行步骤的详细信息如图 9-13 所示。

### 9.5.2 在 Agent 运行中增加人类交互

为了可控地运行 Agent，还可以在运行过程中增加人类交互，控制与修改任务步骤。下面的代码样例会演示人类交互的用法。在这个例子中，我们首先构造多个城市信息查询工具（查询引擎），然后用这些工具开发一个 Agent：

```python

citys_dict = {

    '北京市': 'beijing',

    '南京市': 'nanjing',

    '广州市': 'guangzhou',

    '上海市': 'shanghai',

    '深圳市': 'shenzhen'

}

# 开发城市信息查询工具

def create_city_tool(name: str):

    # ......根据城市名构造对应的查询引擎，并将其包装成查询引擎工具.....

    pass

# 先构造一组工具，再开发一个 Agent，也可以直接开发 Agent

query_engine_tools = []

for city in citys_dict.keys():

    query_engine_tools.append(create_city_tool(city))

# 开发 Agent

openai_step_engine = OpenAIAgentWorker.from_tools(

    query_engine_tools, verbose=True

)

agent = AgentRunner(openai_step_engine)

# 分步运行 Agent：并要求人类给出指令

task_message = None

while task_message != "exit":

    task_message = input(">> 你: ")

    if task_message == "exit":

        break

# 根据输入问题构造任务

task = agent.create_task(task_message)

response = None

step_output = None

message = None

# 任务执行过程中允许人类反馈信息

# 如果 message 为 exit，那么任务被取消，并退出

# 如果任务步骤返回 is_last=True，那么任务正常退出

while message != "exit" and (not step_output or not step_output.is_last):

    # 执行下一步任务

    if message is None or message == "":

        step_output = agent.run_step(task.task_id)

    else:

        # 允许把人类反馈信息传入中间的任务步骤

        step_output = agent.run_step(task.task_id, input=message)

    # 如果任务没结束，那么允许用户输入

    if not step_output.is_last:

        message = input(">> 请补充任务反馈信息（留空继续，exit 退出）：")

# 任务正常退出

# 检索效果快速验证

想判断检索能不能用，基本思路就是先把原文切成可检索的块并建索引，再用带标注的问答集做对照，最后用评估器算分。单条调试和整批打分都能覆盖。

先处理任务收敛的分支。

if step_output.is_last:

    print(">> 任务运行完成。")

    response = agent.finalize_response(task.task_id)

    print(f"Final Answer: {str(response)}")

# 任务被取消

elif not step_output.is_last:

    print(">> 任务未完成，被丢弃。")

下面把城市文本切块、编号并生成检索器，后续评估会用到。

```

代码的运行结果如图 9-14 所示。

>> 你：北京的人口是多少？
Added user message to memory: 北京的人口是多少？
=== Calling Function ===
Calling function: vector_tool_beijing with args: {"input":"population"}
Got output: The population of Beijing has been experiencing fluctuations in recent years. As of the end of 2022, the total population, those aged 15-59 make up 66.6%, and individuals aged 60 and above represent 21.3%. Additionally, issues like water, electricity, gas, and coal, leading to a need for emergency measures to meet the city's demands.
========================================
>> 请补充任务反馈信息（留空继续，exit退出）：与上海的人口做对比
Added user message to memory: 与上海的人口做对比
=== Calling Function ===
Calling function: vector_tool_beijing with args: {"input": "population"}
Got output: The population of Beijing has been experiencing fluctuations in recent years. As of the end of 2022, 15-59 make up 66.6%, and individuals aged 60 and above represent 21.3% of the total population. Additionally, the population size exceeding the environmental resource carrying capacity, leading to issues with water, electricity, the city's total fertility rate has been notably low since 1995, remaining below the replacement level and lower.
========================================
=== Calling Function ===
Calling function: vector_tool_shanghai with args: {"input": "population"}
Got output: 24870895
========================================
>> 请补充任务反馈信息（留空继续，exit退出）：
>> 任务运行完成。
Final Answer: 北京的人口为2184.3万，上海的人口为2487.1万。
>> 你：

这里输入的初始问题是“北京的人口是多少”，此时任务开始正常运行，并调用工具获得输出结果。然后，我们在给出反馈信息时，要求“与上海的人口做对比”，可以看到后面的任务进行了调整，重新获取了北京与上海的人口信息，最后给出了对比的信息。这里通过分步可控地运行 Agent，大大增加了任务执行过程的透明性与可控性，包括任务观察、任务取消、人工反馈与任务调整等。

除了可以更方便地控制任务，这种方式还可以更好地定制个性化的 AgentWorker。比如，要想实现不同于 ReAct 的推理范式，你只需要实现对应的 `run_step` 接口并处理输出即可。

设计与开发 Agent 是一个专业与复杂的课题。本书主要介绍基于 RAG 引擎的工具应用，并利用 Agent 的思想开发更强大与更灵活的知识型应用，仅展示了一部分 Agent 的形式与能力。感兴趣的读者可以在此基础上对 Agent 进行更多的研究。

## 第 10 章 评估 RAG 应用

在软件应用与生产之前，传统过程中一个必不可少的阶段是软件测试。这包括软件用例设计、测试工具开发、软件单元测试、集成测试、压力测试等。这是验证与衡量软件是否具备上线与生产条件的重要阶段。具体到 RAG 应用（包括 Agent），这个阶段仍然举足轻重，甚至比传统应用的这个阶段更重要。

### 10.1 为什么 RAG 应用需要评估

我们已经具备了开发一个完整的 RAG 应用的技术基础，可以在较短的时间内借助类似于 LlamaIndex 或者 LangChain 这样的成熟框架快速开发 RAG 应用。但是，当我们准备将一个基于大模型的 RAG 应用投入生产时，有一些问题是需要提前考虑与应对的。

1. 大模型输出的不确定性会带来一定的不可预知性。一个 RAG 应用在投入生产之前需要科学的测试以衡量这种不可预知性。
2. 在大模型应用上线后的持续维护中，需要科学、快速、可复用的手段来衡量其改进效果，比如回答的置信度是上升了 10% 还是下降了 5%?
3. RAG 应用的“外挂”知识库是动态的，在不断维护的过程中，可能会产生新的知识干扰。因此，定期检测与重新评估是确保应用质量的重要手段。
4. 由于 RAG 应用依赖基础大模型，那么如何在大量的商业与开源大模型中选择最适合企业的大模型或如何知道大模型升级一次版本对 RAG 应用产生了多大影响？

### 10.2 RAG 应用的评估依据与指标

基于大模型的 RAG 应用与传统应用有很大的不同：传统应用的输出大多是确定的且易于衡量的，比如输出一个确定的数值；RAG 应用的输入和输出都是自然语言，其输出一般是一段文字，无法通过简单的定量判断评估其相关性与准确性等，往往需要借助更智能的工具与评估模型。

RAG 应用的评估依据，即评估模块的输入一般包括以下要素。

1. 输入问题 (question): 用户在使用 RAG 应用时的输入问题。
2. 生成的答案 (answer): 需要评估的 RAG 应用的输出，即问题的答案。
3. 上下文 (context): 用于增强 RAG 应用输出的参考上下文，通常在检索阶段生成。
4. 参考答案 (reference_answer): 输入问题的真实的正确答案，通常需要人类标注。

基于这些评估依据，对 RAG 应用进行评估的常见指标见表 10-1。

**表 10-1**

| 名称 | 相关输入 | 解释 |
| :--- | :--- | :--- |
| 正确性 (Correctness) | answer、reference_answer | 生成的答案与参考答案的匹配度，往往涵盖了回答的语义相似度与事实相似度 |
| 语义相似度 (Semantic Similarity) | answer、reference_answer | 生成的答案与参考答案的语义相似度 |
| 忠实度 (Faithfulness) | answer、context | 生成的答案与检索出的上下文的一致性，即生成的答案能否从检索出的上下文中推理出来。或者说，是否存在幻觉问题 |
| 上下文相关性 (Context Relevancy) | context、question | 检索出的上下文与输入问题之间的相关性，即上下文中有多少内容与输入问题相关 |
| 答案相关性 (Answer Relevancy) | answer、question | 生成的答案与输入问题的相关性，即生成的答案是否完整且不冗余地回答了输入问题，不考虑生成的答案的正确性 |
| 上下文精度 (Context Precision) | context、reference_answer | 检索出的上下文中与参考答案相关的条目是否排名较高 |
| 上下文召回率 (Context Recall) | context、reference_answer | 检索出的上下文与参考答案之间的一致性，即参考答案能否归因到上下文 |

### 10.3 RAG 应用的评估流程与方法

RAG 应用的评估流程如图 10-1 所示。

1. 确定评估的目的、维度与指标。
2. 准备评估数据集，可以自行准备与标注，也可以使用大模型生成。根据评估指标，你可能需要准备不同的输入问题（question）与参考答案（reference_answer）。
3. 将评估数据集输入 RAG 应用，获得检索结果（即上下文，context）与生成的答案（answer）。
4. 将评估依据输入评估器，计算各类评估指标，分析 RAG 应用的整体性能。

对 RAG 应用的组件级评估通常侧重于检索与生成两个最关键的阶段。通过对这两个阶段单独评估，可以更细致地观察与分析问题，从而有针对性地优化与增强 RAG 应用。

### 10.4 评估检索质量

利用检索评估组件 `RetrieverEvaluator` 可以对任何检索模块进行质量评估。该评估的主要指标如下。

1. 命中率 (hit_rate): 表示检索出的上下文对期望的上下文的命中率。
2. 平均倒数排名 (mrr): 衡量检索出的上下文的排名质量。
3. Cohere 重排相关性 (cohere-rerank-relevancy): 用 Cohere Rerank 模型的排名结果衡量检索的排名质量。

评估检索质量的主要依据为输入问题与期望的上下文 (检索出的 Node)。

#### 10.4.1 生成检索评估数据集

对检索过程评估既可以通过 `evaluate` 方法对单次查询进行评估，也可以调用 `evaluate_dataset` 方法使用一个构造好的检索评估数据集进行批量评估。由于评估的是检索出的上下文与输入问题之间的相关性，因此需要一系列输入问题与参考上下文的数据对，所以我们首先利用 `generate_question_context_pairs` 方法借助大模型从已有的数据中生成一系列检索评估数据集 (也可以自行构造数据集):

```python

# 读取文档，构造 Node，用于生成检索评估数据集

documents = SimpleDirectoryReader(

    input_files=["../../data/citys/南京市.txt"]).load_data()

node_parser = SentenceSplitter(chunk_size=1024)

nodes = node_parser.get_nodes_from_documents(documents)

for idx, node in enumerate(nodes):

    node.id_ = f"node_{idx}"

# 准备一个检索器，后面使用

vector_index = VectorStoreIndex(nodes)

retriever = vector_index.as_retriever(similarity_top_k=2)

from llama_index.core.evaluation import (

    # ...

)

先把评测集读入，瞄一下前几条问句的格式是否正常。

```generate_question_context_pairs,
EmbeddingQAFinetuneDataset,
)

QA_GENERATE_PROMPT_TMPL = """
以下是上下文:
------------------
{context_str}
------------------
你是一位专业教授。你的任务是基于以上的上下文，为即将到来的考试设置
{num_questions_per_chunk} 个问题。
这些问题必须基于提供的上下文生成，并确保上下文能够回答这些问题。确保每一行都只
有一个独立的问题。不要有多余解释。不要给问题编号。"
"""

print("Generating question-context pairs...")
qa_dataset = generate_question_context_pairs(
    nodes,
    llm=llm_ollama,
    num_questions_per_chunk=1,
    qa_generate_prompt_tmpl=QA_GENERATE_PROMPT_TMPL
)

print("Saving dataset...")
qa_dataset.save_json("retriever_eval_dataset.json")

在这段代码中，手工设置了中文的 Prompt，一方面有助于了解生成原理与调试生成结果，另一方面用于生成中文问题。最后，把生成的检索评估数据集保存到本地的 JSON 文档中，以减少不必要的重复生成，后面只需要从本地文档中加载即可。

在运行这段代码后，可以看到已经生成了本地的 JSON 文档。现在，可以借助代码读取并加载这个生成的 JSON 文档中的评估问题，比如：

```python

......

print("Loading dataset...")

qa_dataset = EmbeddingQAFinetuneDataset.from_json("retriever_eval_dataset.json")

eval_querys = list(qa_dataset.queries.items())

for eval_id, eval_query in eval_querys[:10]:

    print(f"Query: {eval_query}")

进入正式评分。从 JSON 恢复问答数据，实例化 RetrieverEvaluator 并指定 MRR 与 hit_rate 两个指标，对少量样例逐条输出期望文档和评分结果。

```

你需要看一下生成的评估问题，确保没有问题，如图 10-2 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_362_0.png)

当然，为了能够评估检索过程，生成的检索评估数据集中除了输入问题，还有一个很重要的数据，即这个输入问题期望关联的文档，它被放在生成结果的 relevant_docs 字段中，在后面的评估中也需要使用。

## 10.4.2 运行评估检索过程的程序

我们已经构造了一个检索器，并且借助大模型生成了检索评估数据集。下面构造与运行检索评估器。基本的流程如下。

- （1）加载检索评估数据集。
- （2）构造检索评估器（RetrieverEvaluator 对象），设置评估指标。
- （3）调用 evaluate 方法，查看评估结果。

在 10.4.1 节代码的基础上，做以下开发测试：

```python

print("Loading dataset...")

# 从保存的 JSON 文档中加载检索评估数据集

qa_dataset = EmbeddingQAFinetuneDataset.from_json("retriever_eval_dataset.json")

querys = list(qa_dataset.queries.items())

# 构造一个检索评估器，设定两个评估指标

from llama_index.core.evaluation import RetrieverEvaluator

metrics = ["mrr", "hit_rate"]

retriever_evaluator = RetrieverEvaluator.from_metric_names(

    metrics, retriever=retriever

)

# 简单评估前10个评估用例

for eval_id, eval_query in eval_queries[:10]:

    expect_docs = qa_dataset.relevant_docs[eval_id]

    print(f"Query: {eval_query}, Expected docs: {expect_docs}")

    # 评估，输入评估问题与预期检索出的 Node

    eval_result = retriever_evaluator.evaluate(query=eval_query, expected_ids=expect_docs)

    print(eval_result)

如果不想逐条打印，直接让评估器跑完整套问答集，得到汇总分数即可。

```

打印的结果如图 10-3 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_363_0.png)

从打印的结果中可以看到评估指标，你可以对这些评估指标进行汇总计算，得出检索评估器的质量评估结果。如果需要对整个检索评估数据集直接进行评估，那么可以使用更简单的方式：

```python

eval_results = retriever_evaluator.evaluate_dataset(qa_dataset)

复现时最简的文档加载写法如下。

```

直接在检索评估数据集上调用 evaluate_dataset 方法，其效果与逐个循环调用 evaluate 方法的效果是一致的。

## 10.5 评估响应质量

响应质量是 RAG 应用评估的重点，因为这关系到端到端的客户体验。你可以对单个指标、单次响应过程进行评估，也可以在一个批量数据集的基础上自动化运行多个响应评估器，以获得整体的评估结果。

### 10.5.1 生成响应评估数据集

与评估检索过程一样，我们仍然可以借助大模型来生成响应评估数据集（也可以根据格式要求自行标注）。响应评估数据集与检索评估数据集不一样，这是因为评估响应质量除了需要输入问题与检索出的上下文，还需要大模型生成的答案，甚至参考答案。使用 generate_dataset_from_nodes 方法可以让大模型轻松地批量生成响应评估数据集。

用以下代码生成响应评估数据集：

```python

# build documents

docs = SimpleDirectoryReader(input_files=['../../data/citys/南京市.txt']).load_data()

# RAG 评测实战通关

想给检索增强生成做一次可复用的体检，先让模型基于原文自己造题，再用统一的指标和业务规则打分，最后把结果汇总。整套流程可以串成一条从出题到复检的闭环。

## 用文档自行造题

通过 RagDatasetGenerator 把原始资料拆成节点后，让大模型为每个节点提一个可被原文证实的问题。生成指令要求问题必须有上下文依据、每行单独成句、无序号、无额外说明。只跑一次就把问答对落盘，后续直接读取复用。

# define generator, generate questions

dataset_generator = RagDatasetGenerator.from_documents(

    documents=docs,

    llm=llm_ollama,

    num_questions_per_chunk=1,  # 设置每个 Node 都生成的问题数量

    show_progress=True,

    question_gen_query="您是一位老师。您的任务是为即将到来的考试设置 {num_questions_per_chunk}个问题。这些问题必须基于提供的上下文生成，并确保上下文能够回答这些问题。确保每一行都只有一个独立的问题。不要有多余解释。不要给问题编号。"

)

# 以下代码只需要运行一次

print('Generating questions from nodes...\n')

rag_dataset = dataset_generator.generate_dataset_from_nodes()

rag_dataset.save_json('./rag_eval_dataset.json')

# 从本地文档中加载并查看

print('Loading dataset...\n')

rag_dataset = LabelledRagDataset.from_json('./rag_eval_dataset.json')

for example in rag_dataset.examples:

    print(f'query: {example.query}')

    print(f'answer: {example.reference_answer}')

导出后的 json 里保存了 query 与 reference_answer 的对应关系，后续评估可以直接加载 LabelledRagDataset 使用。

## 单条响应的多维体检

查询引擎就绪后，用同一条问句得到回答，再依次计算忠实度、整体相关度、上下文相关度、答案相关度、正确性以及基于嵌入的语义相似度。正确性和语义相似度需要提供人工参考答案。

```

在运行代码后，你可以在当前目录中看到一个 rag_eval_dataset.json 数据集文档，打开该文档，可以看到生成的每个评估用例的数据格式。其中：

- （1）query：生成的问题。
- （2）reference_contexts：检索出的参考上下文。
- （3）reference_answers：参考答案。

这些内容都可能被输入到后面的响应评估器中，作为评估响应质量的输入依据，如图 10-4 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_365_0.png)

图 10-4

### 10.5.2 单次响应评估

要想对 RAG 应用的某一次查询的响应过程进行不同维度的评估，那么只需要构造对应的评估器组件（Evaluator），然后输入必需的数据，即可获得评估结果。单次响应评估的输入参数有以下几个。

- （1）query：输入问题。
- （2）response：RAG 应用的响应结果。如果使用 evaluate_response 方法评估，那么可以直接输入 response；如果使用 evaluate 方法评估，那么需要从 response 中提取上下文与文本内容，将其分别作为参数输入。
- （3）reference：参考答案。它在正确性与相似度评估中会用到，对应收应评估数据集中的 reference_answer 字段。

```python

# ......这里省略构造查询引擎的过程......

query_engine = _create_doc_engine('Nanjing')

# 两个重要的输入参数

query = "南京的气候怎么样？"

response = query_engine.query(query)

# 评估忠实度的评估器

evaluator = FaithfulnessEvaluator()

eval_result = evaluator.evaluate_response(query=query, response=response)

print(f'faithfulness score: {eval_result.score}\n')

# 评估相关性的评估器（综合了上下文相关性与答案相关性）

evaluator = RelevancyEvaluator()

eval_result = evaluator.evaluate_response(query=query, response=response)

print(f'relevancy score: {eval_result.score}\n')

# 评估上下文相关性的评估器

evaluator = ContextRelevancyEvaluator()

eval_result = evaluator.evaluate_response(query=query, response=response)

print(f'context relevancy score: {eval_result.score}\n')

# 评估答案相关性的评估器

evaluator = AnswerRelevancyEvaluator()

eval_result = evaluator.evaluate_response(query=query, response=response)

print(f'answer relevancy score: {eval_result.score}\n')

# 评估正确性的评估器，注意输入了 reference

evaluator = CorrectnessEvaluator()

eval_result = evaluator.evaluate_response(query=query, response=response,

                                          reference='南京的气候属于较典型的北亚热带季风气候。这里四季分明，冬夏温差较大，年平均气温为16.4℃，最冷月（1月）平均气温为3.1℃，最热月（7月）平均气温为28.4℃。南京降水丰富，年平均降水量约为1144毫米，且全年有大约112.9天的降雨日。冬季受西伯利亚高压或蒙古高压控制，盛行东北风；夏季则分为初夏多雨的梅雨季节和盛夏的伏旱天气两段。')

print(f'correctness score: {eval_result.score}\n')

# 评估答案与标准答案的语义相似度（基于 embedding）的评估器，注意输入了 reference

evaluator = SemanticSimilarityEvaluator()

eval_result = evaluator.evaluate_response(query=query, response=response,

                                          reference='南京四季分明，冬夏温差较大，冬季受西伯利亚高压或蒙古高压控制，盛行东北风；夏季则分为初夏多雨的梅雨季节和盛夏的伏旱天气两段。')

print(f'semantic similarity score: {eval_result.score}\n')

## 用批量器加速评分

单条适合调试，上线时用 BatchEvalRunner 并行处理。把常用的评估器注册进 runner，开启 4 个 worker 异步跑前十条样例，最后用 pandas 汇总分数并计算平均值。

```

评估过程非常简单，各个评估指标可参考表 10-1 中的说明。必须再次强调，有的评估器需要输入参考答案（正确性与语义相似度），否则会出现异常。在正常运行评估代码后，可以看到输出的评估结果，如图 10-5 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_367_0.png)

图 10-5

### 10.5.3 批量响应评估

你可以借助批量评估器，在评估数据集的基础上并行运行多个响应评估器，并通过计算与统计获得综合的性能评估结果。以 10.5.1 节生成的响应评估数据集为基础，对构造的查询引擎进行综合评估：

```python

......这里省略构造查询引擎的过程......

query_engine = _create_doc_engine('Nanjing')

# 构造多个响应评估器

faithfulness_evaluator = FaithfulnessEvaluator()

relevancy_evaluator = RelevancyEvaluator()

correctness_evaluator = CorrectnessEvaluator()

similarity_evaluator = SemanticSimilarityEvaluator()

# 加载数据集

rag_dataset = LabelledRagDataset.from_json('./rag_eval_dataset.json')

from llama_index.core.evaluation import BatchEvalRunner

import asyncio

# 构造一个批量评估器

runner = BatchEvalRunner(

    {"faithfulness": faithfulness_evaluator,

     "relevancy": relevancy_evaluator,

     "correctness": correctness_evaluator,

     "similarity": similarity_evaluator},

    workers=4

)

# 为了提高性能，采用异步并行的评估方法，调用批量评估器

# 输入：查询引擎、批量的 query，批量的 reference

# 这里对响应评估数据集中的前十个评估用例进行评估

async def evaluate_queries():

    eval_results = await runner.aevaluate_queries(

        query_engine,

        queries=[example.query for example in rag_dataset.examples][:10],

        reference=[example.reference_answer for example in rag_dataset.examples][:10],

    )

    return eval_results

eval_results = asyncio.run(evaluate_queries())

# 打印评估结果

import pandas as pd

def display_results(eval_results):

    data = {}

    for key, results in eval_results.items():

        scores = [result.score for result in results]

        scores.append(sum(scores) / len(scores))

        data[key] = scores

    data["query"] = [result.query for result in eval_results["faithfulness"]]

    data["query"].append("【Average】")

    df = pd.DataFrame(data)

    print(df)

display_results(eval_results)

## 业务规则自检

除了通用指标，还能用 GuidelineEvaluator 校验业务规范。每条准则会给出是否通过以及反馈意见，便于发现表达不清或数据缺失的问题。

```

借助 BatchEvalRunner 组件，在调用 aevaluate_queries 方法进行批量评估时可以设置 workers 参数并行运行，从而缩短评估的时间。最后，我们把评估结果用表格的形式展示，以便更直观地观察（也可以输出 Excel 文档），输出结果如图 10-6 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_369_0.png)

图 10-6

# 10.6 基于自定义标准的评估

前面的评估都基于一些固定的、通用的指标，比如生成的答案对上下文的遵循程度、生成的答案与参考答案的相似度等。有时候，我们希望制定一个评估指南与标准，并对 RAG 应用进行评估，那么可以借助 GuidelineEvaluator 这样的评估器，设置个性化标准。这非常适合企业级应用中存在特殊响应要求的场景。下面看一个简单的评估样例：

```python

GUIDELINES = [

    "答案应该完全回答了输入问题。",

    "答案应该避免模糊或含糊不清的用词。",

    "答案应该在可能时使用明确的统计数据或数字。"

]

evaluators = [

    GuidelineEvaluator(guidelines=guideline,

                       eval_template=experts.MY_GUILD_EVAL_TEMPLATE)

    for guideline in GUIDELINES

]

for guideline, evaluator in zip(GUIDELINES, evaluators):

    eval_result = evaluator.evaluate_response(

        query="南京有多少人口？南京的气候怎么样？",

        response=response

    )

    print("==============================")

    print(f"Guideline: {guideline}")

    print(f"Pass: {eval_result.passing}")

    print(f"Feedback: {eval_result.feedback}")

## 一次运行的日志片段

```

在上面的代码中，我们构造了 3 个指导标准，并分别构造了评估器，然后对查询过程进行评估，用于确保在应用投入生产之前符合相关业务要求或原则。输出结果如图 10-7 所示。

```text

Starting to create document agent for [Nanjing] ...

Loading vector index...

========================================

Guideline: 答案应该完全回答了输入问题。

Pass: True

Feedback: 该答案完全正确地回答了原始问题。它提供了南京的气候类型、

========================================

Guideline: 答案应该避免模糊或含糊不清的用词。

Pass: True

Feedback: 答案详细地描述了南京的气候类型、四季变化和降水情况，数据

========================================

Guideline: 答案应该在可能时使用明确的统计数据或数字。

Pass: True

Feedback: 答案提供了南京的气候描述，包括温度、降水量和季节变化。但

从自动出题、指标打分到规则复核，整条 RAG 评估链路可以在同一工程内闭环执行。

切分粒度一变，RAG 的快慢与准度就变了

在检索增强里，模型本身固然重要，但文本怎么切往往更直接地决定体验。片段太碎，检索容易带噪；片段太长，一次召回的负担就会拖慢响应，质量和效率就此拉扯。下面用一组可复现的对比，把耗时与忠实、相关、正确三项质量并排看，帮助找到切分长度的合理区间。

实验搭法很清晰。检索后端用 Chroma 做矢量存储，知识基取自 MiniTruthfulQA 的原始文档，评测问题用同源带标注的数据集。评测维度准备了忠实度、相关度、正确度三个打分器。随后让 128、1024、2048 三种粒度跑同一批问答，记录每种粒度的平均耗时和三项指标均值，做横向对照。

```

图 10-7

## 第11章 企业级RAG应用的常见优化策略

我们可以在10分钟甚至更短的时间内构建一个原型应用，但是如果希望它是一个足够健壮、性能卓越、能适用于企业知识应用需求与多元数据环境的“生产就绪(Production Ready)”的系统，却不太可能。我们可能会面临以下常见的问题。

- （1）很难用简单一致的方式处理多个形态、多个来源、多个特点的数据。
- （2）企业内的海量知识文档带来更高的精确检索要求。
- （3）有更复杂的业务需求，任务形态并不总是简单的事实性知识问答。
- （4）有更高的工程化要求，对响应时间、回答的准确性、输出的合规性等要求高。

有的问题可以借助前面介绍的一些高级开发技巧解决，更多的需要考虑其他的优化策略。本章将参考LlamaIndex框架在构建关键RAG应用中的一些建议与指导原则，深入探讨一些常见的RAG应用优化策略，结合已有的组件，希望能够对企业级RAG应用的优化提供一定的指导建议。

## 11.1 选择合适的知识块大小

### 11.1.1 为什么知识块大小很重要

这是一个相对简单，但容易被忽视的基础参数问题。

无论我们是使用 LlamaIndex 框架还是使用 LangChain 框架构建 RAG 应用，在将外部知识特别是文档进行向量化存储时，都会使用 chunk_size 这个决定把原始知识分割成多大块（Chunk）的简单参数，而知识块（在 LlamaIndex 框架中对应的是 Node）也是后面从向量库中检索上下文知识的基本单位。因此 chunk_size 在很大程度上会影响后面的检索与响应质量。

- (1) chunk_size 越小，产生的知识块越多、粒度越小。尽管知识块越小，语义越精确，但是风险是携带的上下文越少，可能导致需要的重要信息不出现在检索出的顶部知识块中（特别是当 top_K 比较小时）。
- (2) chunk_size 越大，携带的上下文越完整，但也带来语义不精确的隐患。此外，过大的 chunk_size 可能导致性能下降、携带的上下文过多，从而导致上下文窗口溢出及 token 成本升高（这个问题在出现具有超大上下文窗口的大模型后会有所缓解）。
- (3) 知识块大小是否合适有时候与 RAG 应用需要完成的任务类型有关。对于常见的事实性问答，你可能只需要少量特定的知识块，而对于摘要、总结、对比之类的任务，你可能需要更大的知识块甚至全部知识块。

因此，确定最合适的知识块大小本质上就是取得某种“平衡”。在不牺牲性能的条件下，你要尽可能捕获最重要的知识信息。在企业级应用场景中，最好的办法就是使用应用评估框架与评估数据集来评估不同的 chunk_size 下的各种性能指标。比如，使用独立的评估框架 RAGAS，或者 LlamaIndex 框架的 Evaluation 模块，得到不同的 chunk_size 下的评估结果，进而做出最优的选择。

### 11.1.2 评估知识块大小

如何进行这样的评估呢？我们从 LlamaHub 网络平台上获取 MiniTruthfulQADataset 数据集来测试在不同的 chunk_size 下的评估结果，并重点考察忠实度、正确性、相关性这几个评估指标，同时跟踪不同情况下的响应延迟情况。

#### 1. 下载测试数据集

从 LlamaHub 网络平台上搜索到 MiniTruthfulQADataset 数据集，然后使用 llamaindex-cli 命令行工具或者 Python 函数 download_llama_dataset 将其下载到本地的 data 目录。

#### 2. 主评估程序

主评估程序代码如下：

```python

......

#设置模型(略)与准备向量库

......

vector_store = ChromaVectorStore(chroma_collection=collection)

#准备需要的知识文档

eval_documents = SimpleDirectoryReader("../../../data/MiniTruthfulQADataset/source_files/").load_data()

#准备用于评估的数据集

eval_questions = LabelledRagDataset.from_json("../../../data/MiniTruthfulQADataset/rag_dataset.json")

#准备评估组件

faithfulness = FaithfulnessEvaluator()

relevancy = RelevancyEvaluator()

correctness = CorrectnessEvaluator()

for chunk_size in [128,1024,2048]:

    avg_time, avg_faithfulness, avg_relevancy, average_correctness = evaluate_response_time_and_accuracy(chunk_size)

    print(f"Chunk size {chunk_size} \n

    Average Response time: {avg_time:.2f}s \n

    Average Faithfulness: {avg_faithfulness:.2f}\n

    Average Relevancy: {avg_relevancy:.2f}\n

    Average Correctness: {average_correctness:.2f}")

评估函数内部先按当前粒度做切分。SentenceSplitter 的重叠设为 0，减少重复信息，切完得到节点后基于 Chroma 构建索引，查询时取前 3 条。随后遍历前 100 道带参考答案的问题，记下单题耗时并依次用三个打分器评分，累加后再折算成均值返回。

```

在上面的代码中，先设置需要的本地大模型与嵌入模型、向量库（考虑到评估过程需要较多的 token，建议测试时使用本地大模型），然后针对 128、1024、2048 三种不同的 chunk_size 循环调用评估方法 evaluate_response_time_and_accuracy 获得评估结果。

#### 3. 评估方法

下面实现 evaluate_response_time_and_accuracy 这个评估函数：

```python

......

def evaluate_response_time_and_accuracy(chunk_size,num_questions=100):

    total_response_time = 0

    total_faithfulness = 0

    total_relevancy = 0

    total_correctness = 0

    #构造查询引擎

    node_parser = SentenceSplitter(chunk_size=chunk_size,

    chunk_overlap=0)

    nodes = node_parser.get_nodes_from_documents(eval_documents,

    show_progress=True)

    storage_context = StorageContext.from_defaults(vector_store=vector_store)

    print(f"\nTotal nodes: {len(nodes)}")

    vector_index = VectorStoreIndex(nodes,storage_context=storage_context)

    query_engine = vector_index.as_query_engine(top_K=3)

    #对指定问题集进行一次评估

    for index, question in enumerate(eval_questions.examples[:num_questions]):

        print(f"\nStart evaluating question 【{index}】:

{question.query}")

        start_time = time.time()

        response_vector = query_engine.query(question.query)

        #计算响应时间

        elapsed_time = time.time() - start_time

        #评估忠实度

        faithfulness_result = faithfulness.evaluate_response(

            query=question.query, response=response_vector

        ).score

        #评估相关性

        relevancy_result = relevancy.evaluate_response(

            query=question.query, response=response_vector

        ).score

        #评估正确性, 此处需要提供参考答案

        correctness_result = correctness.evaluate_response(

            query=question.query,response=response_vector,

            reference = question.reference_answer

        ).score

        total_response_time += elapsed_time

        total_faithfulness += faithfulness_result

        total_relevancy += relevancy_result

        total_correctness += correctness_result

        print(f"Response time: {elapsed_time:.2f}s, Faithfulness:

{faithfulness_result}, Relevancy: {relevancy_result}, Correctness:

{correctness_result}")

        #计算平均得分

        average_response_time = total_response_time / num_questions

        average_faithfulness = total_faithfulness / num_questions

        average_relevancy = total_relevancy / num_questions

        average_correctness = total_correctness / num_questions

        return average_response_time, average_faithfulness,

        average_relevancy,average_correctness

数据构造的细节也会影响结果。以 CSV 问答为例，问题文本作为节点主体，答案写入元数据，并把 answer 加入 excluded_embed_metadata_keys，避免答案在嵌入阶段泄漏。text_template 把正文与元数据拼起来，于是可以分别看到 EMBED 模式下参与检索的文本，和 LLM 模式下模型实际可见的完整上下文。

```

这样就完成了整个评估过程。在终端运行, 等待一段时间后, 可以看到类似于图 11-1 所示的持续的输出结果, 这表示已经开始对问题进行逐个评估。

![图 11-1](img/7eddb68cd925518fdc310e52e94ba9a9_377_0.png)

图 11-1

在所有的 chunk_size 运行完成后，可以观察到各个评估指标的统计结果。根据这些统计结果，你就可以做出最优选择，选择最合适自己的 chunk_size。

## 11.2 分离检索阶段的知识块与生成阶段的知识块

### 11.2.1 为什么需要分离

虽然你可以尝试用最佳的 chunk_size 参数来试图在检索精确性与语义丰富性之间取得平衡，一劳永逸地解决问题，但企业数据环境的复杂性有时候决定了一个统一的知识块策略很难完全奏效。比如：

- (1) 一个原始知识块包含了大模型生成所需要的详细信息，但也包含了一些可能使向量产生偏差的关键词信息，从而导致无法被精确检索。
- (2) 在一个问答型应用中，用户的提问方式可能相对简单，比较适合精确检索少量知识块，但是在生成时需要参考更多的上下文才能形成完整答案。

因此，一种常见的优化策略是，分离检索阶段（Retrieve）的知识块与生成阶段（Generation）的知识块，即给大模型输入的知识块不一定总是直接检索出的最相关的前 K 个知识块。

### 11.2.2 常见的分离策略及实现

“经典” RAG 应用的检索阶段与生成阶段的知识块一致（使用相同的知识块），如图 11-2 所示。

![图 11-2](img/7eddb68cd925518fdc310e52e94ba9a9_378_0.png)

图 11-2

你可以考虑以下几种常见的策略，以分离检索阶段的知识块与生成阶段的知识块。

#### 1. 从问题扩充到完整的问答对

如果原始知识是大量结构化的问答对，那么非常适合只对问题做向量嵌入。在精确检索出问题以后，将问题加入相关的答案一起输入大模型中用作生成，如图 11-3 所示。如果你的问答知识的语义不够丰富，或者你的知识本身并不是问答格式的，那么可以通过以下方式预处理。

- （1）利用大模型生成更多问题的相似问法做嵌入，以提高语义召回能力。
- （2）利用大模型生成文档的假设性问题和答案，形成问答后做嵌入，模拟问答对。

![图 11-3](img/7eddb68cd925518fdc310e52e94ba9a9_378_1.png)

图 11-3

由于 LlamaIndex 框架中的 Node 在进行嵌入与大模型生成时，可以灵活地通过 Node 中的参数来设置不同的 Node 输出内容，因此处理这种结构化的问答知识比较简单：

把问题作为 Node 内容进行嵌入与生成向量，并把答案放在元数据中；只基于问题向量嵌入与检索，而在生成时则将元数据中的答案一起取出并输入大模型中进行生成：

```python

......

def read_csv_file(file_path):

    nodes = []

    with open(file_path, 'r') as file:

        csv_reader = csv.reader(file)

        for row in csv_reader:

            question = row[0]

            answer = row[1]

            node = TextNode(text=question,

metadata={"answer":answer})

            #嵌入时不带入答案

            node.excluded_embed_metadata_keys = ["answer"]

            node.text_template = "{content}\n{metadata_str}\n"

            nodes.append(node)

    return nodes

# Example usage:

csv_file_path = "../../data/questions.csv"

nodes = read_csv_file(csv_file_path)

#打印嵌入内容与大模型生成的内容

for node in nodes:

    print('Embed content:')

    print(node.get_content(metadata_mode=MetadataMode.EMBED))

    print('LLM content:')

    print(node.get_content(metadata_mode=MetadataMode.LLM))

检索完成后再加一步后处理，把窗口信息回写到节点元数据里，方便后续追溯和排查问题。

```

在最后的输出结果中，可以看到输出的嵌入内容是问题（question），而大模型生成的内容则包含了答案（answer）。所以，简单地借助 Node 自身的参数就可以实现在大模型生成时对检索出的 Node 内容进行扩展。

## 2. 扩充知识块所在的上下文窗口

对于常见的连续文档型知识，在检索出相关知识块后，要对其进行内容的扩展，将指定窗口大小内的上下知识块也同时输入大模型中，如图 11-4 所示。比如，如果设置窗口大小为 3，则在检索出某个知识块后，通过读取该知识块的关系信息与元数据，获得与其相关的前后各 3 个知识块，将其一起输入大模型中。很显然，这种方案可以把每个知识块的粒度都相对减小，以提高召回的精确性，但同时又能提供足够的上下文给大模型。

![](img/7eddb68cd925518fdc310e52e94ba9a9_380_0.png)

图 11-4

这种上下文窗口的动态扩展可以基于之前介绍的 SentenceWindowNodeParser 来实现（参考第 5 章）。这种数据分割器会在生成的 Node 的元数据中存储扩展后的上下文窗口。因此，只需要在检索出相关的 Node 以后，借助一个用于替换元数据的节点后处理器，即可动态地替换 Node 内容：

```python

......

node_postprocessors=[

    MetadataReplacementPostProcessor(target_metadata_key="window")

],

# 长文档问答：分层拆分 + 自动合并

面对南京市这种篇幅很长的城市档案，如果只做细碎切分很容易断掉上下文，只做大块切分又会定位不准。实操里常用的是先建三层语义结构，最细那一层负责向量相似度召回，召回命中后再自动把父层的背景信息补回来；同时可以在入库时给每个片段贴上主题，便于后续筛选和解释。

先把原文读入，用分层解析器产出三档节点，2048、512、128，零重叠，这样从骨架到细节的树形结构就完整了。所有节点先进 SimpleDocumentStore，Chroma 里建一个叫 auto_retrieve 的集合作为向量后端，索引只建立在最小粒度的节点上，相似度取前 1。随后把这个叶子检索器包进 AutoMergingRetriever，打开日志并把合并阈值设为 0.1，命中后会自动沿父链回填上下文。

```

具体的节点后处理器的使用方法可以参考第 8 章。

## 3. 从知识摘要扩充到知识内容

在这种模式下会构造两种类型的知识块，一种用于保存知识摘要，另一种用于保存原始知识。检索基于知识摘要进行，在命中相关的摘要块后，通过连接获得相关的一个或者多个知识块（内容块），并把其作为大模型生成的上下文，如图 11-5 所示。这种方案提供了一种在更高层检索知识的手段，而非直接检索知识中的事实，更适合原始知识有较多细节，但问题输入相对概括且单一的场景。

![](img/7eddb68cd925518fdc310e52e94ba9a9_381_0.png)

图 11-5

从文档摘要（摘要块）连接到对应的文档块（内容块）可以借助之前介绍的 DocumentSummaryIndex（文档摘要索引）来实现。DocumentSummaryIndex 的用法与普通的 VectorStoreIndex 的用法基本一致，区别在于构造 DocumentSummaryIndex 时会借助大模型生成文档级别（Document）的摘要，并构造摘要 Node 进行嵌入与索引，而在检索时首先检索出摘要 Node，然后通过保存的对应关系，根据 doc_id 找到所有原始文档 Node 用于生成。需要注意的是，DocumentSummaryIndex 类型的索引检索出的原始文档 Node 是这个 Document 的全部 Node，并不会做二次检索。如果需要做二次检索，就需要借助递归检索。我们将在 11.4 节介绍递归检索。

DocumentSummaryIndex 在检索摘要 Node 时支持两种模式：一种是 embed 模式，即通过输入问题与摘要向量的相似度检索；另一种是 llm 模式，即让大模型来判断输入问题与文档摘要的相关性，从而检索出最相关的摘要 Node。DocumentSummaryIndex 的使用方法可以参考第 6 章。

## 4. 从小知识块扩充到大知识块

这是一种多层知识分割与嵌入方案，即对相同的知识内容在多个不同的粒度上（多个 chunk_size）进行分割并嵌入，在不同的语义粒度上提供多层的检索能力，比如在 chunk_size 为 128、512 两个粒度上进行分割与嵌入，同时保存大小知识块之间的关系信息。在检索出相关的小知识块（128）后，小知识块根据关系信息合并成包含更丰富内容的大知识块（512）输入大模型中，如图 11-6 所示。其好处主要是提升召回知识的丰富性与连续性。

![](img/7eddb68cd925518fdc310e52e94ba9a9_382_0.png)

下面介绍利用 LlamaIndex 框架中的两种组件实现从小知识块扩充到大知识块。

- （1）HierarchicalNodeParser：分层 Node 解析器。这种解析器会根据设置的多个 chunk_size 自动解析多层的 Node，即把文档解析成多个不同 chunk_size 的 Node 列表。
- （2）AutoMergingRetriever：自动合并检索器。在分层 Node 解析器的基础上，对检索出的 Node 进行自动合并，比如把 128 大小的 Node 合并成对应的 512 大小的 Node，从而达到从小知识块扩充到大知识块的目的。

以下是一个简单例子的核心代码：

```python

reader = SimpleDirectoryReader(input_files=["../../../../data/citys/南京市.txt"])

documents = reader.load_data()

# 使用分层 Node 解析器在 3 个不同粒度上解析

node_parser = HierarchicalNodeParser.from_defaults(

    chunk_sizes=[2048, 512, 128],

    chunk_overlap=0

)

nodes = node_parser.get_nodes_from_documents(documents)

print(f'{len(nodes)} nodes created.\n')

# 可以用以下代码查看生成的叶子 Node(128)和根 Node(2048)的数量

# from llama_index.core.node_parser import get_leaf_nodes, get_root_nodes

# leaf_nodes = get_leaf_nodes(nodes)

# root_nodes = get_root_nodes(nodes)

# print(f'leaf nodes: {len(leaf_nodes)}')

# print(f'root nodes: {len(root_nodes)}')

# 使用 Chroma 向量库

collection = chroma.get_or_create_collection(name="auto_retrieve")

vector_store = ChromaVectorStore(chroma_collection=collection)

# 注意此处使用文档存储组件把解析出的所有 Node 全部添加到 docstore 对象中

docstore = SimpleDocumentStore()

docstore.add_documents(nodes)

# 在叶子 Node 层构造向量存储索引

storage_context = StorageContext.from_defaults(vector_store=vector_store, docstore=docstore)

leaf_index = VectorStoreIndex(

    nodes=leaf_nodes,

    storage_context=storage_context

)

# 在叶子 Node 层构造检索器

leaf_retriever = leaf_index.as_retriever(similarity_top_k=1)

# 在叶子 Node 检索器之上构造自动合并检索器

retriever = AutoMergingRetriever(leaf_retriever,

                                 storage_context,

                                 verbose=True,

                                 simple_ratio_thresh=0.1)

拿同一个问题做对照实验，先看最细粒度检索器返回什么，再看带合并逻辑的检索器会给出怎样的补全。

```

这里的代码对检索器进行了特殊处理。

1. 为了能够生成多个粒度下的 Node，使用分层 Node 解析器，并传入多个 chunk_size 作为参数。
2. 在默认的情况下，在构造向量存储索引时会自动构造一个 DocumentStore 对象并存储索引对应的 Node 内容（DocumentStore 是 LlamaIndex 框架中区别于 VectorStore 的另一种内部存储组件，用于存储原始 Node 内容，通常用于在向量检索后重建原始 Node 内容）；此处之所以需要放入所有的 Node，是因为这里的索引只基于叶子 Node 构造，但是在进行合并时，需要获得更大粒度的 Node 内容，这时就可以从 DocumentStore 对象中获取。
3. 自动合并检索器是基于叶子 Node 检索器之上的一个抽象，其主要功能是在叶子 Node 检索器检索出叶子 Node 后，把检索出的 Node 转换成更大粒度的 Node。

自动合并检索器在默认的情况下并不是简单地小粒度 Node 转换成大粒度 Node，而是需要判断检索出的小粒度 Node 在其对应的大粒度的父 Node 中所占的比例，只有所占的比例达到一定的阈值才会合并输出更大粒度的父 Node，并删除小的子 Node。比如，一个 2048 大小的 Node，有 16 个 128 粒度的子 Node，如果本次检索出这 128 个子 Node 中的 10 个，那么会合并输出一个 2048 的大 Node，并删除 10 个小 Node，因为这里 10/16 超过了默认的 50%。在上面的例子中，simple_ratio_thresh=0.1，仅为了展示这个参数的作用。

下面用这两个检索器进行检索，比较其区别：

```python

......

leaf_nodes = leaf_retriever.retrieve("南京市有哪些主要的旅游景点")

print('\n----------------leaf nodes----------------\n')

print_nodes(leaf_nodes)

nodes = retriever.retrieve("南京市有哪些主要的旅游景点")

print('\n----------------nodes----------------\n')

print_nodes(nodes)

最细粒度的命中非常集中，只吐出一条评价性表述。切分规模上，叶子约 313 条，根部约 17 条，说明三层粒度的配置是合理的。

```

在输出结果中，首先看到如图 11-7 所示的叶子 Node 检索器的检索结果（为了演示效果，这里只检索一个 Node），显然这个 Node 中的内容不够丰富。

```text

leaf nodes: 313

root nodes: 17

----------------leaf nodes----------------

Count of nodes: 1

----

Node 0, ID: a698eeb8-e84d-4296-aa0f-e834398f93a6

text:被评为中国首批优秀旅游城市,名列2013年福布斯“中国大陆旅游业最发达城市”第1名

metadata:{'file_path': '../data/citys/南京市.txt', 'file_name': '南京市.txt', 'fied_date': '2024-05-27'}

Score: 0.6840043773521616

----

打开合并日志，可以清晰看到命中后向上拼接父节点的过程。检索触发了父节点的合并，得到了秦淮灯会、金陵刻经印刷技艺等更完整的段落，不是孤立的一句话。

```

![](img/7eddb68cd925518fdc310e52e94ba9a9_385_0.png)

接下来，使用自动合并检索器进行检索，会看到如图 11-8 所示的输出结果，有部分 Node 被合并到了父 Node 中并输出了内容（因为 simple_ratio_thresh 参数设置得很小），而父 Node 的内容明显更丰富并且是连续的。

```text

> Merging 1 nodes into parent node.

> Parent node id: d100aba9-06fd-4bdb-9376-6d63c2000ff6.

> Parent node text: === 秦淮燈會 ===

秦淮燈會是南京歷史悠久的民俗文化活動,又稱金陵燈會,每年於春節至元宵節期間舉行,2006年被列入國

== 旅游景观 ==

...

> Merging 1 nodes into parent node.

> Parent node id: e93d2a70-ffd2-48f6-ae28-bdd751a964e9.

> Parent node text: === 金陵刻经印刷技艺 ===

金陵刻经印刷技艺是南京的傳統手工藝,以寫樣、刻版、印刷及裝幀四道手工程序製作,又細分上樣、刻字

如果希望检索结果更容易归类，可以在数据摄取阶段为每个片段打上主题。设计一个分类提示，让模型把文本归入基本、历史、经济、文化、交通、旅游、其他七类之一，只输出类名字符串，再用一个转换组件把类别写入节点的元数据里。

```

![](img/7eddb68cd925518fdc310e52e94ba9a9_385_1.png)

以上几种分离“检索知识块”与“生成知识块”的策略虽然在实现方法上各有差异，但基本思想是一致的：用相对小的知识块提升检索的精确度，同时扩展到更大的知识块，以保证大模型有更丰富的上下文用于生成答案。在实际应用中，这几种基础方法往往会结合其他的优化策略灵活使用，不能一概而论。

# 11.3 优化对大文档集知识库的检索

如果你只是用一个简单的 PDF 文档来构建经典的 RAG 应用，那么可能永远不会优化对大文档集知识库的检索。在企业复杂的知识密集型应用中，你可能会面临几百个不同来源与类型的知识文档。虽然你可以通过多级管理简化对它们的管理与维护，但是在向量存储与检索的方法上，如果只是简单地依赖传统的文本分割与 top-k 检索，就会产生精度不足、知识相互干扰等问题，从而导致效果不佳，而最主要的问题发生在检索阶段。一个重要的优化方法是在大文档集下“分层”过滤与检索。你可以考虑采用以下 3 种不同的分层检索方案。

## 11.3.1 元数据过滤 + 向量检索

### 1. 原理与架构

这种方案在构造向量库时根据文档信息对分割的每个知识块做元数据（比如地区、类型）标识，然后做向量存储，如图 11-9 所示。所以，检索时的流程变为：

- （1）利用大模型推理出输入问题的元数据。
- （2）借助向量库的元数据过滤定位到部分文档的知识块。
- （3）结合向量检索进一步定位到最相关的前 K 个知识块。

这种方案的好处是简单地借助了向量库的能力，可以快速地自动过滤并检索，但其缺点如下：

- （1）需要设计元数据标识。
- （2）借助大模型推理出输入问题的元数据存在一定的不确定性。
- （3）元数据只能精确匹配，不能用语义检索。
- （4）需要借助向量库。

![](bbox=[130, 118, 868, 448])

### 2. 实现方案

我们需要使用的 LlamaIndex 框架的组件是 VectorIndexAutoRetriever。这是一个在已有的向量索引组件基础上的检索器类型的组件。其主要的能力是，根据输入的自然语言问题，借助大模型与输入的元数据描述信息（VectorStoreInfo），推理出一组元数据过滤器，然后利用向量库的元数据过滤功能，在元数据过滤的基础上进行语义检索，从而达到元数据过滤+向量检索分层检索的目的。

本节实现简单的基于元数据的文档过滤样例，这个样例中的基本处理流程如下。

#### 1）定义生成元数据的方法

在实际应用中，可以根据数据情况灵活地选择这一步骤。如果你的数据本来就是结构化的信息，比如分类的问答信息，那么可以直接自行构造 Node 信息，并设置元数据；如果你的数据是大量分割清楚的普通文档，那么可以在加载完一个文档后直接设置 Document 对象的元数据；对于其他情况，需要自行生成元数据，比如借助大模型提取元数据，下面的例子演示了这种方式（请根据实际情况调整 Prompt）：

```python

catalog_prompt_temp = """\n你是一个聪明的内容分类器。请把我的内容归类到以下类别之一:

-------

基本

历史

经济

文化

交通

旅游

其他

-------

我的内容是: {text}

直接输出类别，不要有多余说明。

"""

catalog_prompt = PromptTemplate(catalog_prompt_temp)

# 构造简单的函数生成一个元数据

def get_catalog(text: str):

    catalog = llm.predict(

        catalog_prompt, text=text

    )

    return catalog

# 定义一个用于数据摄取的转换器，把这个元数据插入 Node 中

class MetadataRicher(TransformComponent):

    def __call__(self, nodes, **kwargs):

        for node in nodes:

            node.metadata["catalog"] = get_catalog(node.text)

        return nodes

打标完成之后，可以把切分和打标封装进摄取流水线。这里用 500 字、零重叠的句子切分器做分块，后面接上刚才的打标组件。第一次跑就把索引持久化到 ./storage/vectorindex/autoretrieve，后续直接加载，避免重复构建。

```

#### 2）嵌入并存入向量库

使用数据摄取管道，加载文档，生成元数据，然后将其嵌入并存入向量库：

```python

city_docs = SimpleDirectoryReader(input_files=["../../../../data/citys/南京市.txt"]).load_data()

# 构造普通的向量索引，注意这里生成了元数据

def create_vector_index():

    collection = chroma.get_or_create_collection(name=f"autoretrieve")

    vector_store = ChromaVectorStore(chroma_collection=collection)

    if not os.path.exists(f"./storage/vectorindex/autoretrieve"):

        # 加载数据

        pipeline = IngestionPipeline(

            transformations=[

                SentenceSplitter(chunk_size=500, chunk_overlap=0),

                MetadataRicher(),  # 插入自定义转换器

            ]

        )

        nodes = pipeline.run(documents=city_docs)

        storage_context = StorageContext.from_defaults(vector_store=vector_store)

        vector_index = VectorStoreIndex(nodes, storage_context=storage_context)

        vector_index.storage_context.persist(

            persist_dir=f"./storage/vectorindex/autoretrieve")

    else:

        print('Loading vector index...\n')

        storage_context = StorageContext.from_defaults(

            persist_dir=f"./storage/vectorindex/autoretrieve",

            vector_store=vector_store)

        vector_index = load_index_from_storage(storage_context=storage_context)

总体思路是先用层级切分保证可达性和上下文完整性，再在最细层做精准向量检索，最后借助自动合并把背景补全，配合轻量的主题元数据提升可解释性，在城市百科、长篇资料问答等场景里效果比较稳。

# 摘要先筛细节再验：城市文档双层检索

多份城市资料同时在线时，直接对全部分块做向量匹配容易拖慢定位且来源易混。更稳妥的思路是先为每份材料生成摘要、搭建上层索引做快速锁定，命中后再通过 IndexNode 内部的过滤检索器下探到原始分块进行精准核验。这样既能把候选范围迅速缩小，也能让最终答案具备可追溯性。下面是一套在 LlamaIndex 里的落地流程。

    return vector_index

index = create_vector_index()

retriever = index.as_retriever()

```

#### 3）构造自动检索器

为了能够实现基于元数据的自动过滤与检索，需要使用 VectorIndexAutoRetriever 组件，并传入已经构造的向量索引用于检索：from llama_index.core.retrievers import VectorIndexAutoRetriever
from llama_index.core.vector_stores.types import MetadataInfo, VectorStoreInfo

vector_store_info = VectorStoreInfo(
    content_info="中国城市各方面的信息与介绍",
    metadata_info=[
        MetadataInfo(
            name="catalog",
            type="str",
            description=(
                """
                信息目录,只能是以下之一:基本、历史、经济、文化、交通、旅游、
其他。
                """
            ),
        )
    ],
)

auto_retriever = VectorIndexAutoRetriever(
    index,
    vector_store_info=vector_store_info, verbose=True, similarity_top_k=3
)

nodes = auto_retriever.retrieve("介绍一些关于南京经济情况的信息")
print_nodes(nodes)

运行代码后可以看到类似于图 11-10 所示的输出结果。在检索时, 首先通过大模型推理出元数据的过滤条件 (‘catalog’, ‘==’, ‘经济’)。这个过滤动作将通过向量库（Chroma）的元数据过滤功能来实现。同时，使用输入问题进行语义检索，从而达到元数据过滤结合语义检索的分层检索。

![](img/7eddb68cd925518fdc310e52e94ba9a9_391_0.png)

## 11.3.2 摘要检索+内容检索

### 1. 原理与架构

这种方案吸收了从“小知识块”到“大知识块”的检索思想，对每个文档都做摘要提取，在摘要与文档内容两个级别上分别做嵌入与向量存储，并做好两者的关联，如图 11-11 所示。检索时的流程如下。

- （1）在摘要级别检索，获得相关的摘要块。
- （2）根据摘要块的关联，可以对原始文档的内容块进行检索。
- （3）递归检索出原始文档的内容块，找到关联的上下文。

![](img/7eddb68cd925518fdc310e52e94ba9a9_391_1.png)

这种方案的好处是在两个级别上都可以进行语义检索。其缺点如下。

- （1）需要借助大模型生成摘要块，较麻烦且增加成本。
- （2）查找摘要时如果语义匹配错误，那么后面无法得到有效的关联上下文。

### 2. 实现方案

在上面设计的架构中，在摘要层与文档内容层都需要进行语义检索，这与 11.3.1 节介绍的元数据过滤 + 向量检索是不一样的。我们希望在摘要层检索出对应的摘要 Node 后，能够自动地找到文档内容层的二级检索器进行检索，最终检索出相关的内容 Node 用于生成。本节将一步步构建这样一个应用的案例，这需要用到前面介绍过的一种 Node 类型：IndexNode。

如果我们希望在文档的摘要 Node 被检索出来后，能够自动执行递归检索，那么只需要在摘要 Node（IndexNode 类型）中放入二级检索器的引用。

用城市信息查询的例子来演示这种检索方案的应用。

#### 1）准备原始文档

准备原始文档，将其加载成 Document 对象作为后面处理的基础：

```python

#加载原始文档

print('Loading documents...\n')

city_docs = SimpleDirectoryReader(input_files=[

    "../../../data/citys/南京市.txt",

    "../../../data/citys/北京市.txt",

    "../../../data/citys/上海市.txt",

    "../../../data/citys/广州市.txt"]).load_data()

#设置固定的 index_id，后面用于构造对应的摘要 Node

for doc in city_docs:

    doc.metadata['index_id'] = os.path.splitext(doc.metadata["file_name"])[0]

```

#### 2）生成摘要

使用 SummaryIndex 这个组件和 tree_summarize 类型的生成器给原始文档生成摘要。使用这种方式生成的摘要相对全面（你也可以使用大模型快速生成摘要）。

为了避免每次运行时都重复生成摘要，这里对生成的摘要进行了持久化存储。

```python

#给每个 Document 对象都生成一个摘要 (summary)，用于构造摘要 Node

def generate_docs_summary():

    #持久化存储

    def save_summary_txt(doc_id, summary_txt):

        summary_dir = "./storage/summary_txt"

        if not os.path.exists(summary_dir):

            os.makedirs(summary_dir)

        summary_file = os.path.join(summary_dir, f"{doc_id}.txt")

        with open(summary_file, "w") as f:

            f.write(summary_txt)

    #加载

    def load_summary_txt(doc_id):

        summary_dir = "./storage/summary_txt"

        summary_file = os.path.join(summary_dir, f"{doc_id}.txt")

        if os.path.exists(summary_file):

            with open(summary_file, "r") as f:

                return f.read()

        return None

    #借助 SummaryIndex 组件生成摘要，也可以用大模型快速生成摘要

    def generate_summary_txt(doc):

        summary_index = SummaryIndex.from_documents([doc])

        query_engine = summary_index.as_query_engine(

            llm=llm_dash,

            response_mode="tree_summarize")

        summary_txt = query_engine.query("请用中文生成摘要")

        summary_txt = str(summary_txt)

        save_summary_txt(doc.metadata["index_id"], summary_txt)

        return summary_txt

    #给每个 Document 对象都生成摘要，将其保存到元数据中，且不参与嵌入和大模型输入

    print('Generate document summary...\n')

    for doc in city_docs:

        doc_id = doc.metadata["index_id"]

        summary_txt = load_summary_txt(doc_id)

        if summary_txt is None:

            summary_txt = generate_summary_txt(doc)

            #这个摘要在原始文档 Node 中不参与嵌入与生成

            doc.metadata["summary_text"] = summary_txt

            doc.excluded_embed_metadata_keys = ["summary_text"]

            doc.excluded_llm_metadata_keys = ["summary_text"]

generate_docs_summary()

```

这里把生成的摘要放在 Document 对象的元数据的 summary_text 键中用于后面使用。

#### 3）构造原始文档索引与检索器

这个步骤与构造普通向量索引没有区别，索引将用于后面的二级检索：

```python

#构造二级向量索引，针对所有 Document 对象，并持久化存储，避免重复构造

def create_vector_index():

    splitter = SentenceSplitter(chunk_size=500, chunk_overlap=0)

    nodes = splitter.get_nodes_from_documents(city_docs)

    collection = chroma.get_or_create_collection(name=f"details_citys")

    vector_store = ChromaVectorStore(chroma_collection=collection)

    #构造向量索引，通过持久化存储避免重复构造

    if not os.path.exists(f"./storage/vectorindex/allcitys"):

        print('Creating vector index...\n')

        storage_context = StorageContext.from_defaults(vector_store=vector_store)

        vector_index = VectorStoreIndex(nodes, storage_context=storage_context)

        vector_index.storage_context.persist(persist_dir=f"./storage/vectorindex/allcitys")

    else:

        print('Loading vector index...\n')

        storage_context = StorageContext.from_defaults(

            persist_dir=f"./storage/vectorindex/allcitys",

            vector_store=vector_store)

        vector_index = load_index_from_storage(storage_context=storage_context)

    return vector_index

docs_index = create_vector_index()

```

#### 4) 构造摘要 Node

首先, 准备一个用于构造文档对应的摘要 Node 的函数:

```python

#辅助函数: 针对单个 doc, 构造一个基于摘要的 IndexNode (索引 Node)

def create_doc_index_node(doc):

    #取出摘要内容

    summary_txt = doc.metadata["summary_text"]

    filters = MetadataFilters(

        filters=[

            MetadataFilter(

                key="index_id",

                operator=FilterOperator.EQ,

                value=doc.metadata["index_id"]

            ),

        ]

    )

    #构造单个文档的摘要 Node, 注意此处的 obj 是用于进行递归检索的检索器

    #摘要 Node 为 IndexNode 类型的

    index_node = IndexNode(

        index_id = doc.metadata["index_id"],

        text = summary_txt,

        metadata = doc.metadata,

        obj = docs_index.as_retriever(filters = filters)

    )

    return index_node

```

在上面的代码中, 获取了保存在原始文档中的摘要, 作为 IndexNode 对象的 text 属性; 同时, 把已经构造的原始文档索引检索器的引用保存在 IndexNode 对象的 obj 属性中。因此, 在这个摘要 Node 被检索后, 将会自动调用 obj 属性指向的检索器进行递归检索 (原始文档 Node), 从而达到通过两级检索定位到相关知识块的目的。

需要特别说明的是 MetadataFilters 的用法, 这是一个元数据过滤器, 也是前面介绍过的元数据自动化检索的底层组件。元数据过滤器的作用是在检索器进行向量检索时, 同时通过元数据信息进行 Node 过滤。由于我们在原始文档层构造的是一个针对所有城市文档的向量索引, 因此为了能够在从摘要 Node 开始递归检索时对应到相应城市的原始文档, 这里使用了元数据过滤器来区分二级检索器。

实际上也可以给每个文档都构造一个独立的检索器, 然后在其对应的摘要 Node 中设置 obj 为这个检索器引用。这样无须元数据过滤器, 也能达到相同的效果。

#### 5) 构造摘要索引

最后, 构造摘要索引, 这也是最终直接使用的索引:

```python

#构造摘要索引

def create_summary_index():

    summary_collection = chroma.get_or_create_collection(

        name=f"summary_allcitys")

    vector_store = ChromaVectorStore(chroma_collection=summary_collection)

    #所有的摘要 Node

    index_nodes = []

    for doc in city_docs:

        index_node = create_doc_index_node(doc)

        index_nodes.append(index_node)

    #构造基于摘要 Node 的索引, 注意摘要 Node 用 objects 参数

    print('Creating summary index (for recursive retrieve)...\n')

    storage_context = StorageContext.from_defaults(vector_store=vector_store)

    vector_index = VectorStoreIndex(

        objects=index_nodes,

        storage_context=storage_context)

    return vector_index

summary_index = create_summary_index()

```

通过前面构造索引 Node 的方法给每个文档依次构造摘要 Node，最后构造 VectorStoreIndex 类型的索引并返回。

#### 6）测试摘要索引

我们可以简单测试这个摘要索引，并观察输出效果。我们期望在检索出对应的摘要 Node 后，能够自动使用二级检索器再次检索，最终返回原始文档 Node：

```python

print('Creating query engine...\n')

# 构造一个基于摘要索引的查询引擎

retriever = summary_index.as_retriever(similarity_top_k=1, verbose=True)

query_engine = RetrieverQueryEngine(retriever)

print('Query executing...\n')

response = query_engine.query('上海市的人口多少')

pprint_response(response, show_source=True)

# 多篇论文的分层问答方案

让同一个模型同时理解多篇文献并能区分来源，最可靠的做法是先为每篇文献配一个具备检索和综合能力的子助手，再用一个总控助手统一调度。思路是先列清单，再逐篇建索引与助手，然后把助手再封装成可调用工具，最后由主控或检索式路由完成跨文献的问答。

先把需要处理的文献标识和文件路径整理成两组列表，方便后续批量生成。
```

输出结果如图 11-12 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_397_0.png)

图 11-12

在图 11-12 所示的跟踪信息中可以看出，在检索出摘要 Node 后，自动进入了针对上海市的二级检索器（Retrieval entering 上海市：VectorIndexRetriever），并使用这个二级检索器检索了问题（Retrieving from… with query…），最后输出了两个原始文档 Node，并正确生成了答案。

## 11.3.3 多文档 Agentic RAG

### 1. 原理与架构

现在考虑这样一个场景：有很多不同来源与类型的文档（在实际应用中，并不一定是“文档”，也可以是某种形态的知识库，甚至关系数据库），需要在这些“文档”之上构建一个依赖于它们的知识密集型应用或工具。典型的应用需求如下。

- （1）查询这些文档中的一些事实性知识。比如，什么是大模型？
- （2）基于摘要与总结回答问题。比如，××文档主要讲了什么内容？
- （3）跨文档/知识库回答问题。比如，Self-RAG 与 C-RAG 的区别是什么？
- （4）结合其他工具复合应用。比如，从××文档中提取产品介绍发送给××客户。

很显然，对于这种复杂需求的场景，如果使用经典的 RAG 应用，通过知识块+向量+top_K 检索来获得上下文，让大模型给出答案，那么显然是不现实的。经典的 RAG 应用在回答文档相关的事实性问题上，在大部分时间可以工作得不错，但是知识应用并不总是这种类型的，比如无法基于向量检索简单地生成文档的摘要与总结，也无法胜任一些跨文档回答问题或者需要结合其他工具复合应用的工作。

下面采用一种基于 Agent 思想的多文档 Agentic RAG 的方案。这虽然也是一个“两级”的方案，但是并不是通过简单的两级向量来实现分层递归检索，而是通过两级的 Agent 之间的配合，结合底层的 RAG 查询引擎来完成更复杂的知识型任务。

其基本架构如图 11-13 所示。

- （1）为每一个文档或知识库都创建一个知识 Agent（这里称作 Tool Agent）。这个 Agent 的能力是可以使用一个或者多个 RAG 查询引擎来回答问题。
- （2）在多个知识 Agent 之上创建一个语义路由的 Agent（这里称作 Top Agent），这个 Agent 会借助推理功能使用后端的知识 Agent 完成查询任务。

基于 RAG 查询引擎的多级 Agent 架构（Agentic RAG）最大的优点是具备了极大的灵活性与扩展性，几乎可以完成任意基于知识的复杂任务。知识既可以是向量化的知识，也可以是外部系统的结构化或非结构化的知识。其主要优势来自以下两点。

- 对二级 Tool Agent 的扩展，可以赋予其更多的工具能力，使其不再局限于简单地回答事实性问题，可以完成更多的知识型任务。比如，整理、生成摘要、分析数据，甚至借助 API 获取外部系统的实时知识等。
- 多个 Tool Agent 可以通过协作完成联合型任务。比如，对比与汇总两个不同文档中的知识。这也是经典的问答型 RAG 应用无法完成的任务。

当然，这种方案的缺点是具有一定的实现复杂性，且对大模型的推理能力要求较高。

## 2. 实现方案

我们用一个样例来介绍如何创建基于多文档 RAG 查询引擎的分层 Agent。这种方案借助 Agent 具备的观察、规划与行动能力，既能提供 RAG 应用的基础查询能力，也能提供基于 RAG 应用的更多样与完成复杂任务的能力。

### 1）准备原始文档

首先，准备 3 个与 RAG 相关的 PDF 文档作为测试的原始文档。在实际应用中，文档数量可以扩展到非常多（后面会看到对大量文档进行优化的方法）：

```python

names = ['c-rag', 'self-rag', 'kg-rag']

files = ['../../data/c-rag.pdf', '../../data/self-rag.pdf', '../../data/kg-rag.pdf']

单篇的构建逻辑被封装成一个工厂函数。先连上本地 Chroma 服务并创建公共集合，随后读取 PDF、用句子切分器生成节点，根据本地是否已有缓存决定新建检索索引还是直接载入。基于检索索引做细节查问，同时再建一个摘要索引用于整体归纳。两个查询引擎分别转成工具，并在 ReAct 代理的系统指令里要求必须借助工具作答、不得依赖先验记忆也不得编造。
```

### 2）准备创建 Tool Agent 的函数

创建一个给单个 PDF 文档生成 Tool Agent 的函数。在这个函数中，对这个文档进行加载与分割，然后构造两个索引与对应的查询引擎。

- 针对普通的事实性问题的向量索引与查询引擎。
- 针对需要高层语义理解的总结类问题的摘要索引与查询引擎。

最后，我们把这两个查询引擎作为 Agent 的两个工具，创建一个 Tool Agent。

```python

#此处省略 import 部分与模型准备部分

#采用 Chroma 向量库

chroma = chromadb.HttpClient(host="localhost", port=8000)

collection = chroma.get_or_create_collection(name="agentic_rag")

vector_store = ChromaVectorStore(chroma_collection=collection)

#创建针对某个文档的 Tool Agent

def create_tool_agent(file, name):

    #分割文档，生成 Node 对象

    print(f'Starting to create tool agent for 【{name}】...\n')

    docs = SimpleDirectoryReader(input_files = [file]).load_data()

    splitter = SentenceSplitter(chunk_size=500, chunk_overlap=50)

    nodes = splitter.get_nodes_from_documents(docs)

    #构造向量索引，并持久化存储

    if not os.path.exists(f"./storage/{name}"):

        print('Creating vector index...\n')

        storage_context = StorageContext.from_defaults(

            vector_store=vector_store)

        vector_index = VectorStoreIndex(nodes,

            storage_context=storage_context)

        vector_index.storage_context.persist(persist_dir=f"./storage/{name}")

    else:

        print('Loading vector index...\n')

        storage_context = StorageContext.from_defaults(

            persist_dir=f"./storage/{name}",

            vector_store=vector_store)

        vector_index = load_index_from_storage(

            storage_context=storage_context)

    #构造基于向量索引的查询引擎

    query_engine = vector_index.as_query_engine(similarity_top_k=5)

    # Create a summary index

    summary_index = SummaryIndex(nodes)

    summary_engine = summary_index.as_query_engine(

        response_mode="tree_summarize")

    #转换为工具

    query_tool = QueryEngineTool.from_defaults(

        query_engine=query_engine,

        name=f'query_tool',

        description=f'Use if you want to query details about {name}')

    summary_tool = QueryEngineTool.from_defaults(

        query_engine=summary_engine,

        name=f'summary_tool',

        description=f'Use ONLY IF you want to get a holistic summary of the documents. DO NOT USE if you want to query some details about {name}.')

    #创建一个 Tool Agent

    tool_agent = ReActAgent.from_tools([query_tool, summary_tool],

                                       verbose=True,

                                       system_prompt=f"""

You are a specialized agent designed to answer queries about {name}.You

must ALWAYS use at least one of the tools provided when answering a

question; DO NOT rely on prior knowledge. DO NOT fabricate answer.

"""

                                       )

    return tool_agent

用该工厂函数遍历三份材料，把每份文献的名称对应到专属助手，方便后续统一调用。
```

这里也可以使用路由查询引擎来代替 Agent 实现接近的功能。但是要注意，路由查询引擎与 Agent 是有区别的，路由查询引擎在大部分时候仅起到选择工具与转发问题的作用，并不会多次迭代，而 Agent 则会观察工具返回的结果，有可能使用多个工具通过多次迭代来完成任务。

### 3) 批量创建二级 Tool Agent

有了上面的函数后，就可以批量创建这些文档的 Tool Agent。我们把每一个文档名字和对应的 Agent 都保存在一个 dict 型变量中：

```python

#创建不同文档的 Tool Agent

print('========================================\n')

print('Creating tool agents for different documents...\n')

tool_agents_dict = {}

for name, file in zip(names, files):

    tool_agent = create_tool_agent(file, name)

    tool_agents_dict[name] = tool_agent

代理本身也是一种查询引擎，可以再包一层变成上层可用的工具。为每篇文献生成以 tool_ 开头的工具名并配上使用说明，全部收进列表。
```

### 4) 创建一级 Top Agent

我们需要创建一个顶层的 Agent，这个 Agent 的作用是接收客户的请求问题，然后制订这个问题的查询计划，并调用工具来完成。这里的工具就是上面创建的多个 Agent。

```python

#将 Tool Agent 进行“工具化”

print('========================================\n')

print('Creating tools from tool agents...\n')

all_tools = []

for name in names:

    agent_tool = QueryEngineTool.from_defaults(

        #注意，Agent 本身也是一种查询引擎，所以可以直接转换为工具

        query_engine=tool_agents_dict[name],

        #这个工具的名称

        name=f"tool_{name.replace("-", " ")}",

        #描述这个工具的作用和使用方法

        description=f"Use this tool if you want to answer any questions about {name}."

    )

    all_tools.append(agent_tool)

工具集准备好后，初始化顶层代理。它持有全部工具，并在系统指令中规定所有回答都必须借助工具完成，禁止使用既有记忆或虚构信息，从而实现跨文献的一致问答。
```

```python

#创建 Top Agent

print('Creating top agent...\n')

top_agent = OpenAIAgent.from_tools(tools=all_tools,

                                   verbose=True,

                                   system_prompt="""You are an agent designed to answer queries over a

set of given papers. Please always use the tools provided to answer

a question. Do not rely on prior knowledge. DO NOT fabricate answer"""

                                   )

直接进入交互模式即可提问。
```

### 5）测试

下面测试这个 Top Agent：

```python

top_agent.chat_repl()

当文献数量继续增长时，一次性加载全部工具效率会下降。可以为工具本身建立检索索引，用 ObjectIndex 把工具描述向量化，按相似度挑出最相关的两项再路由。
```

输入一个问题：Please introduce Retrieval Evaluator in C-RAG pattern?（由于原文档是英文文档，因此这里使用英文测试问题），输出结果如图 11-14 所示。

![图 11-14](img/7eddb68cd925518fdc310e52e94ba9a9_403_0.png)

图 11-14

仔细观察输出的部分调试信息，可以看出 Agent 的“思考”过程。

1. 在 Top Agent 层，由于我们使用了 OpenAIAgent 类型，其是通过 OpenAI 的函数调用来实现的，因此这里显示大模型要求进行函数调用，需要调用 tool_crag 这个工具，输入参数为“Retrieval Evaluator in C-RAG pattern”。这里的函数名就是后端 Tool Agent 包装的工具名称。
2. 在 Tool Agent 层，Tool Agent 收到请求后，通过 ReAct 推理范式，决定需要调用 query_tool 工具，也就是基于向量索引的查询引擎。在调用这个查询引擎后，获得了返回内容（图 11-14 中“observation”后面的内容）。收到返回内容后，Tool Agent 通过观察与推理，认为可以回答这个问题，因此 Tool Agent 运行结束，并返回结果给 Top Agent。
3. Top Agent 收到函数调用的结果后，认为无须再次调用其他函数，因此直接输出了结果，处理过程结束。

### 6）进一步优化

前面用了 3 个文档，创建了针对它们的 Tool Agent。如果文档数量是几十个或者几百个，在 Top Agent 进行推理时，Tool Agent 过多，那么发生错误的概率就会增加。在之前介绍 Agent 时，曾经介绍过如何创建带有工具检索功能的 Agent，这里就是一种很适合使用它的场景。简单地修改上面的代码，给 Top Agent 在推理时增加工具检索功能，能够缩小选择工具的范围。

只需要在创建 Top Agent 之前给工具构造一个 Object Index 类型的工具检索器，用于根据输入问题检索必要的工具：

```python

#构造工具检索器

print('========================================\n')

print('Creating tool retrieve index...\n')

obj_index = ObjectIndex.from_objects(

    all_tools,

    index_cls=VectorStoreIndex,

)

tool_retriever = obj_index.as_retriever(similarity_top_k=2, verbose=True)

把主控代理的工具来源换成上述检索器，就能实现按需调用，指令依然要求借助工具作答、不依赖先验知识。
```

然后，简单地修改创建 Top Agent 的代码，不再传入 all_tools，而是传入工具检索器：

```python

......

top_agent = OpenAIAgent.from_tools(tool_retriever=tool_retriever,

                                   verbose=True,

                                   system_prompt="""You are an agent designed to answer queries over a

set of given papers. Please always use the tools provided to answer

a question. Do not rely on prior knowledge."""

                                   )

......

```

如果继续测试这个 Agent，就会发现仍然可以达到相同的效果，如图 11-15 所示。

![图 11-15](img/7eddb68cd925518fdc310e52e94ba9a9_405_0.png)

图 11-15

如果需要验证检索出的工具的正确性，那么可以直接对工具检索器调用检索方法来观察（输入相同的自然语言问题），比如：

```python

tools_needed = tool_retriever.retrieve("What is the Adaptive retrieval in the c-RAG?")

print('Tools needed to answer the question:')

for tool in tools_needed:

    print(tool.metadata.name)

```

可以看到如图 11-16 所示的输出结果，由于我们设置工具检索器的 similarity_top_k=2，因此检索出排名前两位的相关工具，排名首位的 tool_crag 很显然正是需要用于回答问题的 Agent，从而证明了工具检索的有效性。

![图 11-16](img/7eddb68cd925518fdc310e52e94ba9a9_405_1.png)

图 11-16

## 11.4 使用高级检索方法

检索是 RAG 应用最重要的阶段之一，检索的召回率与精确性决定了后面响应生成阶段的质量。与检索相关的因素非常多，包括原始知识的形式与质量、输入问题、索引类型、嵌入模型/大模型、检索算法、排序算法等，其中有的影响因素需要在数据加载与分割、数据嵌入与索引阶段进行优化，比如原始数据质量、嵌入质量、索引类型等，也有的影响因素则需要在检索阶段结合其他阶段综合考虑，比如利用的索引、检索的算法、重排序算法等。

在经典的 RAG 应用中，检索通常是基于向量存储索引的语义检索。本章将对基础索引检索以外的复杂检索技巧进行介绍与演示，以便在实际应用中根据需要选择更高效的检索方法。

### 11.4.1 融合检索

融合检索（Fusion Retrieval）是一种多维度检索的方法，简单地说就是通过多个不同的检索方法进行检索，并对检索的结果使用 RRF 算法（或其他算法）重排序后输出。融合检索可以组合多个不同的输入问题或者不同类型索引的检索结果，以弥补单个索引在检索精确性上的不足。融合检索的原理如图 11-17 所示。

![图 11-17](img/7eddb68cd925518fdc310e52e94ba9a9_406_0.png)

图 11-17

倒数排名融合（RRF）是一种将多个搜索结果的排名组合起来生成单个统一排名的技术。通过组合不同搜索结果的排名，可以增加最相关的文档/知识出现在最终排名顶部的机会，从而帮助大模型提高响应生成的质量。如果对 RRF 的细节感兴趣，那么可以搜索相关的论文。

LlamaIndex 框架中内置了简单易用的融合检索器。当然，你也可以完全利用已有的知识自定义一个融合检索器。为了帮助你更好地学习融合检索，下面首先介绍如何自定义一个融合检索器，然后介绍如何使用现成的融合检索器。

#### 1. 用原生代码实现融合检索

我们基于已经介绍的索引与检索组件，自定义一个融合检索器，步骤如下。

- 查询转换：根据输入问题生成多个问题用于检索。
- 构造两个检索器：采用一个向量检索器和一个关键词检索器。
- 重排序：给检索出的多个 Node 重排序。
- 自定义融合检索器：以前 3 步为基础，自定义一个融合检索器。
- 主程序实现与测试：实现与测试融合检索器。

我们仍然基于之前的城市信息来实现，准备以下城市信息的文档：

```python

citys_dict = {

    '北京市': 'beijing',

    '南京市': 'nanjing',

    '广州市': 'guangzhou',

    '上海市': 'shanghai',

    '深圳市': 'shenzhen'

}

```

准备好大模型与嵌入模型：

```python

llm_openai = OpenAI(model='gpt-3.5-turbo')

embedded_model_openai = OpenAIEmbedding(model_name="text-embedding-3-small",

                                         embed_batch_size=50)

Settings.llm = llm_openai

Settings.embed_model = embedded_model_openai

```

##### 1) 查询转换

我们采用自定义的简单查询转换：

```python

def rewrite_query(query: str, num: int = 3):

    """将 query 转换为 num 个查询问题"""

    prompt_rewrite_temp = """\n您是一个查询生成器，根据我的输入问题生成多个查询问题。

请生成与以下输入问题相关的{num_queries}个查询问题 \n

"""

```注意每个查询问题都占一行 \n
我的输入问题: {query}
生成查询列表:
"""
prompt_rewrite = PromptTemplate(prompt_rewrite_temp)
response = llm_openai.predict(
    prompt_rewrite, num_queries=num, query=query
)

# 假设大模型将每个查询问题都放在一行上
queries = response.split("\n")
return queries
```
查询改写并不是融合检索必须走的流程，完全可以不做改写，直接拿原始问题去多类索引上做融合检索与生成。

## 2）搭建双检索器
进入实操阶段，首先要准备两套检索器。它们的底层可以分别是向量索引和关键词索引，也可以是同一种索引配上不同的检索策略，做法由你来定。我们分别封装两个工厂函数，一个负责生成向量检索器，一个负责生成关键词检索器。

先以城市名为入口定位到对应的知识库文档，再基于这些文档构建向量索引。为了防止每次请求都重新建库，索引会落地持久化保存。

```python
def create_vector_index_retriever(name:str):

    #解析 Document 为 Node
    city_docs = \n    SimpleDirectoryReader(input_files=[f"../../data/citys/{name}.txt"
    ]).load_data()
    splitter = SentenceSplitter(chunk_size=500,chunk_overlap=0)
    nodes = splitter.get_nodes_from_documents(city_docs)

    #存储到向量库 Chroma 中
    collection = \n    chroma.get_or_create_collection(name=f"agent_{citys_dict[name]}")
    vector_store = ChromaVectorStore(chroma_collection=collection)

#首次运行时构造向量索引, 完成后进行持久化存储, 以后直接加载
if not os.path.exists(f"./storage/vectorindex/{citys_dict[name]}"):
    print('Creating vector index...\n')
    storage_context = StorageContext.from_defaults(vector_store=vector_store)
    vector_index = VectorStoreIndex(nodes, storage_context=storage_context)
    vector_index.storage_context.persist(persist_dir=f"./storage/vectorindex/{citys_dict[name]}")
else:
    print('Loading vector index...\n')
    storage_context = StorageContext.from_defaults(
        persist_dir=f"./storage/vectorindex/{citys_dict[name]}",
        vector_store=vector_store)
    vector_index = load_index_from_storage(storage_context=storage_context)

#返回向量检索器
vector_retriever = vector_index.as_retriever(similarity_top_k=3)
return vector_retriever
```
关键词表索引的创建思路与上面类似，照样建表并为它配好对应的检索器：

```python
def create_kw_index_retriever(name:str):

    city_docs =\n    SimpleDirectoryReader(input_files=[f"../../data/citys/{name}.txt"]).load_data()
    splitter = SentenceSplitter(chunk_size=500,chunk_overlap=0)
    nodes = splitter.get_nodes_from_documents(city_docs)

    if not os.path.exists(f"./storage/keywordindex/{citys_dict[name]}"):
        print('Creating keyword index...\n')

        #构造关键词表索引
        kw_index = KeywordTableIndex(nodes)
        kw_index.storage_context.persist(
            persist_dir=f"./storage/keywordindex/{citys_dict[name]}"")
    else:
        print('Loading keyword index...\n')
        storage_context = StorageContext.from_defaults(
            persist_dir=f"./storage/keywordindex/{citys_dict[name]}"")
        kw_index = load_index_from_storage(storage_context=storage_context)

#返回关键词检索器
kw_retriever = kw_index.as_retriever(num_chunks_per_query=5)
return kw_retriever
```
为了让多检索器多轮查询更高效，我们再封装一个辅助方法，利用异步并发的方式把几路检索同时跑起来：

```python
async def run_queries(queries, retrievers):

    tasks = []
    #对于每个问题，每个检索器都进行检索
    for query in queries:
        for i, retriever in enumerate(retrievers):
            tasks.append(retriever.aretrieve(query))

    task_results = await tqdm.gather(*tasks)

    #保存每次检索的结果
    results_dict = {}
    for i, (query, query_result) in enumerate(zip(queries, task_results)):
        results_dict[(query, i)] = query_result

    return results_dict
```
## 3）重排序
多路召回的结果需要收敛，这一步采用 RRF 融合排序对来自不同检索器的 Node 重新打分排位，最后截取 Top K 返回。下面给出一个可复用的通用实现：

```python
def rerank_results(results_dict, similarity_top_k: int = 3):
    k = 60.0
    fused_scores = {}
    text_to_node = {}

    #计算不同 Node 的文本内容评分
    for nodes_with_scores in results_dict.values():
        for rank, node_with_score in enumerate(
            sorted(
                nodes_with_scores, key=lambda x: x.score or 0.0,
                reverse=True
            )
        ):
            text = node_with_score.node.get_content()
            text_to_node[text] = node_with_score
            if text not in fused_scores:
                fused_scores[text] = 0.0
            fused_scores[text] += 1.0 / (rank + k)

    #重排序
    reranked_results = dict(
        sorted(fused_scores.items(), key=lambda x: x[1], reverse=True)
    )

    # 构造重排序的 Node 并返回前 K 个 Node
    reranked_nodes: List[NodeWithScore] = []
    for text, score in reranked_results.items():
        reranked_nodes.append(text_to_node[text])
        reranked_nodes[-1].score = score

    return reranked_nodes[:similarity_top_k]
```
## 4）自定义融合检索器
前面的组件就位后，就可以动手写一个专属的融合检索器了。实现方式是继承 BaseRetriever 基类，并在内部完成 _retrieve 方法的逻辑：

```python
class FusionRetriever(BaseRetriever):

    #基于多个检索器构造融合检索器
    #参数：检索器列表与 top_k
    def __init__(self, retrievers: List[BaseRetriever], similarity_top_k: int = 3) -> None:
        self._retrievers = retrievers
        self._similarity_top_k = similarity_top_k
        super().__init__()

    #实现检索方法
    def _retrieve(self, query_bundle: QueryBundle) -> List[NodeWithScore]:

        #查询转换
        querys = rewrite_query(query_bundle.query_str,num=3)

        #调用辅助方法得到全部检索结果
        results_dict = asyncio.run(run_queries(querys, self._retrievers))

        #使用 RRF 算法重排序
        final_results = rerank_results(results_dict, similarity_top_k=self._similarity_top_k)

        return final_results
```
## 5）主程序实现与测试
有了融合检索器就能组装查询引擎。引擎所需的响应生成器可以直接用框架自带的默认版本，也可以在此之前自行定制后传入：

```python
def run_main():
    query = "南京市有多少人口，是怎么分布的？"

    #构造两个检索器
    vector_retriever = create_vector_index_retriever('南京市')
    kw_retriever = create_kw_index_retriever('南京市')

    #构造融合检索器
    fusion_retriever = FusionRetriever(
        [vector_retriever, kw_retriever],
        similarity_top_k=3)

    #构造查询引擎
    query_engine = RetrieverQueryEngine(fusion_retriever)

    # 查询
    response=query_engine.query(query)
    pprint_response(response,show_source=True)

if __name__ == "__main__":
    run_main()
```
执行这段脚本后，得到的输出效果与图 11-18 一致。

![](img/7eddb68cd925518fdc310e52e94ba9a9_413_0.png)
以上就是一套从零搭建自定义融合检索器的完整路径，过程中没有依赖现成的融合组件，只是借助检索器与查询转换就能跑通。当然，后续完全可以根据实际需求对这套流程做二次改造。

## 2. 直接用现成的融合检索器

# 融合检索一键封装

LlamaIndex 最新版本把融合检索的核心能力收进了一个现成的组件 QueryFusionRetriever， 用它之后就不用再自己拼查询转换和重排逻辑了。 这个检索器内部已经把自定义融合检索里常见的查询改写以及基于 RRF 的重排全包办了， 调用时只要把多个检索器和必要的参数塞进去就能拿到融合后的结果。

按自定义融合检索的示例改写主流程，可以这么调整：

```python
......
def run_main():

    from llama_index.core.retrievers import QueryFusionRetriever

    query = "南京市有多少人口，是怎么分布的？"

    #构造两个检索器
    vector_retriever = create_vector_index_retriever('南京市')
    kw_retriever = create_kw_index_retriever('南京市')

    #使用现成的 QueryFusionRetriever 类型的融合检索器
    fusion_retriever = QueryFusionRetriever(
        [vector_retriever, kw_retriever],
        similarity_top_k=3,
        num_queries=1, # set this to 1 to disable query generation
        mode="reciprocal_rerank",
        use_async=True,
        verbose=True
    )

    #构造查询引擎
    query_engine = RetrieverQueryEngine(fusion_retriever)

    #查询
    response=query_engine.query(query)
    pprint_response(response)
```

换成 QueryFusionRetriever 后效果一致，而且它自带多种重排策略，通过 mode 参数切换即可。 当前可用的模式有：

- (1) reciprocal_rerank：RRF 算法。
- (2) relative_score：相关性评分融合算法。
- (3) dist_based_score：基于距离的评分融合算法。
- (4) simple：默认模式，按检索出的 Node 原始评分直接重排。

## 11.4.2 递归检索

融合检索用多源召回加重排来弥补单一索引的精度偏差。 实际工程里另一种常用做法是分层检索，因为实现上是逐层递进的，也常被叫做递归检索。 11.3 节提到的摘要检索加内容检索就是典型的二级分层案例。

### 1. 递归检索的原理

找一段文字，最笨的办法是通篇翻书，更高效的做法是分层缩小范围：

- (1) 先做粗筛，比如按出版社或类别归类。
- (2) 范围缩小后看图书简介，锁定几本候选。
- (3) 在候选中结合目录和实翻定位到确切位置。

这就是递归检索的思路：在不同粒度上为知识构建 Node 与索引，比如摘要层和原文层，再通过 Node 间的链接在检索时自动逐层下探，直至满足结束条件。

图 11-19 展示了层级关系与执行流程。

![](img/7eddb68cd925518fdc310e52e94ba9a9_415_0.png)

一种常见实现是在需要下探的知识块上挂一个指向下一层调用体的引用。 当该块被命中时，就沿着引用继续检索，直到拿到的块里不再包含可引用的组件。 最后深度检索到的块会替代最开始的命中块，作为大模型的上下文输入。

实际落地时，Node 里保存的引用形式比较灵活，主要有四类：

(1) 指向另一个 Node 的引用。 递归时直接取出被引用的 Node 内容返回，没有真正的检索过程，适合层次清晰的映射，例如：
    - ① 小块保存对应大块的引用。
    - ② 摘要块保存对应原文块的引用。
    - ③ 假设问题块保存对应原文块的引用。
(2) 能直接给出答案的 RAG 查询引擎。 递归调用该引擎拿到答案后封装成 Node 返回。
(3) 具备规划与工具调用能力的 Data Agent。 递归调用 Agent 得到答案后封装成 Node 返回。
(4) 复杂 RAG 范式的查询管道。 递归调用该管道得到答案后封装成 Node 返回。

在 LlamaIndex 里，能携带外部对象引用的 Node 类型是 IndexNode。

### 2. 从 Node 到 Node 的递归检索

基于 Node 引用的递归检索本质是关联查找：通过已命中的 Node 找到它所关联的其他 Node。 因为 Node 的语义精确度和信息丰富度通常此消彼长，RAG 设计里往往会为两种需求分别建模。 整体原理见图 11-20。

#### 1) 从子 Node 到父 Node 的递归检索

此前用 AutoMergingRetriever 把子块合并为父块，现在可以用内置的 RecursiveRetriever 实现子块向父块的递归。 该检索器的入口由 root_id 指定，从根节点开始检索。 遇到 IndexNode 时会去取它指向的对象，该对象可以是 Node、检索器或查询引擎，具体查找依赖三个字典参数 retriever_dict、query_engine_dict、node_dict。

实现分三步走：

- ① 对文档切片，生成父级 Node。
- ② 在更细的粒度上再切出子级 Node，并让子 Node 指向父 Node。
- ③ 为子 Node 建索引和检索器，再在此基础上套一层递归检索器。

##### (1) 构造父 Node

建父 Node 的方式与普通向量索引类似。 为便于观察，这里手动指定 id：

```python
docs = SimpleDirectoryReader(input_files=["../../data/c-rag.pdf"]).load_data()

def create_base_index():
    splitter = SentenceSplitter(chunk_size=1024, chunk_overlap=0)
    nodes = splitter.get_nodes_from_documents(docs)

    # 设置每个 Node 的 id 为固定值
    for idx, node in enumerate(nodes):
        node.id_ = f"node_{idx}"

    collection = chroma.get_or_create_collection(name=f"crag")
    vector_store = ChromaVectorStore(chroma_collection=collection)
    if not os.path.exists(f"./storage/vectorindex/crag"):
        print('Creating vector index...\n')
        storage_context = StorageContext.from_defaults(vector_store=vector_store)
        vector_index = VectorStoreIndex(nodes, storage_context=storage_context)
        vector_index.storage_context.persist(persist_dir=f"./storage/vectorindex/crag")
    else:
        print('Loading vector index...\n')
        storage_context = StorageContext.from_defaults(
            persist_dir=f"./storage/vectorindex/crag",
            vector_store=vector_store)
        vector_index = load_index_from_storage(storage_context=storage_context)
    return vector_index, nodes

# 构造父 Node
base_index, base_nodes = create_base_index()
```

##### (2) 构造子 Node

在 128 和 256 两个更细粒度上切分子 Node，并通过 index_id 把子 Node 链到父 Node，方便后续递归：

```python
def create_subnodes_index(base_nodes):
    # 构造两个不同粒度的分割器
    sub_chunk_sizes = [128, 256]
    sub_node_parsers = [SentenceSplitter(chunk_size=subsize, chunk_overlap=0) for subsize in sub_chunk_sizes]

    all_nodes = []
    # 对每一个父 Node 都进行分割
    for base_node in base_nodes:
        for n in sub_node_parsers:
            # 使用 get_nodes_from_documents 方法生成子 Node
            sub_nodes = n.get_nodes_from_documents([base_node])
            for sn in sub_nodes:
                # 子 Node 是 IndexNode 类型的，并用父 Node 的 id 作为 index_id
                indexnode_sn = IndexNode.from_text_node(sn, base_node.node_id)
                all_nodes.append(indexnode_sn)

        # 父 Node 也作为 IndexNode 对象放入 all_nodes 对象中
        all_nodes.append(IndexNode.from_text_node(base_node, base_node.node_id))

    # 构造子 Node 的向量索引
    collection = chroma.get_or_create_collection(name=f"crag-subnodes")
    vector_store = ChromaVectorStore(chroma_collection=collection)
    if not os.path.exists(f"./storage/vectorindex/crag-subnodes"):
        print('Creating subnodes vector index...\n')
        storage_context = StorageContext.from_defaults(vector_store=vector_store)
        vector_index = VectorStoreIndex(all_nodes, storage_context=storage_context)
        vector_index.storage_context.persist(persist_dir=f"./storage/vectorindex/crag-subnodes")
    else:
        print('Loading subnodes vector index...\n')
        storage_context = StorageContext.from_defaults(
            persist_dir=f"./storage/vectorindex/crag-subnodes",
            vector_store=vector_store)
        vector_index = load_index_from_storage(storage_context=storage_context)
    return vector_index, all_nodes

# 构造子 Node 与索引
sub_index, sub_nodes = create_subnodes_index(base_nodes)
```

代码里把已有的父 Node（1024 尺寸）按 128、256 再次切分。 切分后的子 Node 会被转成 IndexNode，其中第二个参数就是 index_id。 这里传入 base_node.node_id，意味着该索引 Node 指向它所属的父 Node，为后续递归提供基础。

```python
for sn in sub_nodes:
    indexnode_sn = IndexNode.from_text_node(sn, base_node.node_id)
    all_nodes.append(indexnode_sn)
```

##### (3) 构造递归检索器

有了前面的准备，就可以组装递归检索器和查询引擎做验证：

```python
# 准备子 Node 层的检索器
sub_retriever = sub_index.as_retriever(similarity_top_k=2)

# 准备一个所有 Node 的 id 与 Node 的对应关系字典
# 这个字典用于在递归检索时，根据 index_id 快速地找到对应的对象
sub_nodes_dict = {n.node_id: n for n in sub_nodes}

# 构造递归检索器
recursive_retriever = RecursiveRetriever(
    "root_retriever",
    retriever_dict={"root_retriever": sub_retriever},
    node_dict=sub_nodes_dict,
    verbose=True,
)

# 用递归检索器构造查询引擎
recursive_query_engine = RetrieverQueryEngine.from_args(recursive_retriever)

# 测试
response = recursive_query_engine.query("please explain the concept of Action Trigger in c-rag?")
pprint_response(response)
```

关键在于 RecursiveRetriever 的构造，它需要一个 root_id 作为检索入口，这个入口可以是检索器、查询引擎或具体的 Node。 之后检索器会按既定逻辑逐层下探。

基于该检索器建一个查询引擎测试，效果如图 11-21 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_421_0.png)

检索器先命中两个相关的子 Node，随后递归进入 node_9 和 node_5 这两个父 Node，并在父 Node 层面生成答案。 这在很多场景下很合理：子 Node 更利于精确召回，父 Node 则提供更完整的上下文。

#### 2) 从摘要 Node 到内容 Node 的递归检索

为了让检索层承载更丰富的语义，常常在基础 Node 上额外生成辅助上下文，例如：

- （1）摘要。 对内容较长的 Node 生成摘要用于检索。
- （2）假设性问题。 用大模型为 Node 内容生成若干假设问题或相似问题。

以摘要 Node 为例，可以把摘要 Node 映射回内容 Node，并在摘要层建立索引。 需要注意，本节的做法与 11.3 节的思路并不完全相同。

```python
# 根据基础 Node 构造摘要 Node
def create_summary_nodes(base_nodes):
    # 构造一个元数据抽取器
    extractor = SummaryExtractor(summaries=["self"], show_progress=True)
    summary_dict = {}

    # 为了避免重复抽取，进行持久化存储
    if not os.path.exists(f"./storage/metadata/summarys.json"):
        print('Extract new summary...\n')

        # 抽取元数据，建立从 Node 到元数据的词典
        summarys = extractor.extract(base_nodes)
        for node, summary in zip(base_nodes, summarys):
            summary_dict[node.node_id] = summary

        with open('./storage/metadata/summarys.json', "w") as fp:
            json.dump(summary_dict, fp)
    else:
        print('Loading summary from storage...\n')
        with open('./storage/metadata/summarys.json', "r") as fp:
            summary_dict = json.load(fp)

    # 根据摘要构造摘要 Node，注意使用 IndexNode 类型
    all_nodes = []
    for node_id, summary in summary_dict.items():
        all_nodes.append(IndexNode(text=summary["section_summary"], index_id=node_id))

    # 加入基础 Node
    all_nodes.extend(IndexNode.from_text_node(base_node, base_node.node_id) for base_node in base_nodes)

    # 构造摘要 Node 层的索引
    collection = chroma.get_or_create_collection(name=f"crag-summarynodes")
    vector_store = ChromaVectorStore(chroma_collection=collection)

    if not os.path.exists(f"./storage/vectorindex/crag-summarynodes"):
        print('Creating summary nodes vector index...\n')
        storage_context = StorageContext.from_defaults(vector_store=vector_store)
        vector_index = VectorStoreIndex(all_nodes, storage_context=storage_context)
        vector_index.storage_context.persist(persist_dir=f"./storage/vectorindex/crag-summarynodes")
    else:
        print('Loading summary nodes vector index...\n')
        storage_context = StorageContext.from_defaults(
            persist_dir=f"./storage/vectorindex/crag-summarynodes",
            vector_store=vector_store)
        vector_index = load_index_from_storage(storage_context=storage_context)

    return vector_index, all_nodes
```

在这一段实现里，系统调用了内置的元数据抽取功能，为原始 Node 产出摘要，并把这些摘要封装成 IndexNode 形态的节点，同时用 index_id 建立起与源 Node 的对应关系。在此基础上即可搭建基于摘要索引的递归检索器：

```python
......
summary_index, summary_nodes = create_summary_nodes(base_nodes)
summary_retriever = summary_index.as_retriever(similarity_top_k=2)
summary_nodes_dict = {n.node_id: n for n in summary_nodes}

# 构造一个递归检索器
recursive_retriever = RecursiveRetriever(
    "root_retriever",
    retriever_dict={"root_retriever": summary_retriever},
    node_dict=summary_nodes_dict,
    verbose=True,
)
recursive_query_engine = RetrieverQueryEngine.from_args(recursive_retriever)
response = recursive_query_engine.query("please explain the concept of Action Trigger in c-rag?")
pprint_response(response)
......
```

从图 11-22 的展示可以看出，检索路径从摘要 Node 出发，经递归进入了 node_5 与 node_21，并把这两处节点的内容一并送入了大模型的上下文窗口。

图 11-22

## 3）从假设性问题到答案 Node 的递归检索

要扩充检索上下文，常见做法是先生成假设性问题。此处不再展开演示，仅需把前例中“从摘要 Node 到内容 Node 的递归检索”的元数据抽取器换成 QuestionsAnsweredExtractor 即可。该抽取器能为单个基础 Node 产出多个假设问题，从而对应多个索引 Node，后续处理流程与之前保持一致：

```python
extractor = QuestionsAnsweredExtractor(questions=5,
show_progress=True)
```

## 3. 从 Node 到查询引擎的递归检索

递归检索的本质是沿着 Node 之间的关联关系层层下探。这种关联既可以是父子 Node，也可以是摘要 Node 与正文 Node。保存在一个 Node 内部用于递归的引用，既可以指向另一个 Node，也可以指向一个 RAG 查询引擎或 Agent。下面说明如何把检索到的 Node 进一步交由其关联的查询引擎使用，并得到最终结果。

这种模式很适合层级分明的知识查询场景：可以在二级知识文档上先搭建可独立使用的查询引擎，同时在一级知识文档上构建索引与检索器，并把一级索引中负责承接的 Node 链接到二级查询引擎。一旦一级 Node 被命中，就能顺着保存的链接继续下探，调用关联的查询引擎完成生成。具体思路见图 11-23 所示。

图 11-24 展示了一个非结构化 HTML 页面，同样适用于 PDF 文档，除了常规的文字说明外，还内嵌了结构化表格。常规情况下，只需采集解析页面即可得到面向整体内容的 RAG 查询引擎。但若要对内嵌表格执行基于 SQL 或 Pandas 的复杂分析，比如统计或数据挖掘，就必须额外搭建二级查询引擎。

借助递归检索查询此类文档中嵌入表格的操作可以拆成三步。

1. 为解析得到的表格单元单独构建查询引擎。实现上可以依托 Pandas 数据分析能力，也可以接入 SQL 数据库，以满足对结构化表格的复杂查询需求。
2. 为结构化表格生成一个摘要 Node，采用 IndexNode 类型，并把该摘要 Node 关联到表格专属的独立查询引擎。
3. 将表格的摘要 Node 与文档拆分出的其他 Node 一并建立一级向量索引，并组装成查询引擎供终端用户使用。

## 模型效果

为全面评估 Qwen1.5 的表现，我们对 Base 与 Chat 版本在多项基础与扩展能力上的表现进行了系统测试，覆盖语言理解、代码能力、推理能力等基础维度，多语言能力，人类偏好对齐、智能体能力以及检索增强生成 RAG 等方向。

## 基础能力

在模型基础能力的评测中，我们选用了 MMLU 5-shot、C-Eval、HumanEval、GSM8K、BBH 等基准，对 Qwen1.5 进行了对比测试。

| Model | MMLU | C-Eval | GSM8K | MATH | HumanEval | MBPP | BBH | CMMLU |
|---|---|---|---|---|---|---|---|---|
| GPT-4 | 86.4 | 69.9 | 92.0 | 45.8 | 67.0 | 61.8 | 86.7 | 71.0 |
| Llama2-7B | 46.8 | 32.5 | 16.7 | 3.3 | 12.8 | 20.8 | 38.2 | 31.8 |
| Llama2-13B | 55.0 | 41.4 | 29.6 | 5.0 | 18.9 | 30.3 | 45.6 | 38.4 |
| Llama2-34B | 62.6 | - | 42.2 | 6.2 | 22.6 | 33.0 | 44.1 | - |
| Llama2-70B | 69.8 | 50.1 | 54.4 | 10.6 | 23.7 | 37.7 | 58.4 | 53.6 |
| Mistral-7B | 64.1 | 47.4 | 47.5 | 11.3 | 27.4 | 38.6 | 56.7 | 44.7 |
| Mixtral-8x7B | 70.6 | - | 74.4 | 28.4 | 40.2 | 60.7 | - | - |
| Qwen1.5-7B | 61.0 | 74.1 | 62.5 | 20.3 | 36.0 | 37.4 | 40.2 | 73.1 |
| Qwen1.5-14B | 67.6 | 78.7 | 70.1 | 29.2 | 37.8 | 44.0 | 53.7 | 77.6 |

图 11-24

下面按顺序演示完整案例的实现过程。

### 1) 数据加载与解析

首先，使用 Web 加载器 SimpleWebPageReader 读取网页内容，再配合非结构化元素解析器把页面切分成多个 Node。

```python
url = ['https://qw**lm.github.io/zh/blog/qwen1.5/']

#此处更改默认的摘要 Prompt 为中文
DEFAULT_SUMMARY_QUERY_STR = """\n尽可能结合上下文, 用中文详细介绍表格内容。 \n这个表格是关于什么的? 给出一个摘要说明 (想象你正在为这个表格添加一个新的标题和摘要), \n"""
```

```python
#加载网页到 docs 变量
web_loader = SimpleWebPageReader()
docs = web_loader.load_data(url)

#分割成 Node，并持久化存储
node_parser = UnstructuredElementNodeParser(
    summary_query_str=DEFAULT_SUMMARY_QUERY_STR)

if nodes_save_path is None or not os.path.exists(nodes_save_path):
    raw_nodes = node_parser.get_nodes_from_documents(docs)
    pickle.dump(raw_nodes, open(nodes_save_path, "wb"))
else:
    raw_nodes = pickle.load(open(nodes_save_path, "rb"))
```

这段代码里，SimpleWebPageReader 负责抓取网页，UnstructuredElementNodeParser 负责解析文档并产出基础 Node raw_nodes。需要留意的是，该解析器会自动分辨页面中的普通文本与表格，并对表格做特殊化处理，后续编码的准确性取决于是否理解这些处理逻辑。

1. 表格会被转成 Markdown 文本作为 Node 的内容。
2. 利用大模型为表格生成摘要，并构建一个 IndexNode 类型的摘要 Node。
3. 该摘要 Node 的 index_id 指向对应的具体表格 Node。

上述实现还包含两点实用技巧。

1. 通过设置 summary_query_str 参数调整生成表格摘要时的 Prompt，主要是为了让摘要输出中文。
2. 使用 Python 的 pickle 库对解析后的 Node 做持久化存储，避免重复解析浪费时间。

### 2）构造二级 Node 查询引擎

此时，文档已被解析为基础 Node，且表格的摘要 Node 也已准备好，接下来需要处理二级 Node。

1. 定位二级 Node，即被索引 Node 指向的表格 Node。
2. 为二级 Node 单独构建查询引擎。
3. 把二级 Node 从一级向量索引的构建范围中剔除。

```python
#解析其中的 IndexNode 类型的索引 Node，找到其指向的 Node，然后给该 Node
#中的表格生成对应的查询引擎
raw_nodes_dict = {doc.id_: doc for doc in raw_nodes}
query_engine_dict = {}
nonbase_node_ids = set()

for node in raw_nodes:
    #如果是索引 Node
    if isinstance(node, IndexNode):
        #找到索引 Node 指向的表格 Node
        child_node = raw_nodes_dict[node.index_id]
        
        #把表格 Node 转换成 pandas.DataFrame 类型的
        df = node_to_df(child_node)
        
        #构造一个基于此 DataFrame 对象的查询引擎
        df_query_engine = PandasQueryEngine(df)
        
        #将索引 Node 与查询引擎链接起来
        query_engine_dict[node.index_id] = df_query_engine
        
        #记录已经被索引 Node 引用的 Node，后面将其去除
        nonbase_node_ids.add(node.index_id)

#去除已经被索引 Node 引用的 Node，剩下的 Node 用于构造一级向量索引
base_nodes = []
for node in raw_nodes:
    if node.node_id not in nonbase_node_ids:
        base_nodes.append(node)
```

简而言之，先根据 IndexNode 类型的索引 Node 找到内嵌的表格 Node，为其创建查询引擎并把引擎链接回索引 Node，同时将表格 Node 与一级向量索引解耦，不直接参与一级检索。

这里会用到 node_to_df 函数，用来把 Node 内容转为 DataFrame 对象，以便接入 PandasQueryEngine。下面的实现借助大模型完成该函数：

```python
#把 Node 中的内容转换为一个 DataFrame 对象
node_table_save_path = './storage/nodes/qwen1.5de_id}.pkl'
def node_to_df(node):
    prompt_rewrite_temp = """\n    你是一个数据清洗工具。请去除内容中前面的说明部分，仅保留表格输出。不要多余
解释和多余空格。不要修改和编造表格。\n
    内容: {content}
    表格:
    """
    prompt_rewrite = PromptTemplate(prompt_rewrite_temp)
    llm = OpenAI(model="gpt-3.5-turbo")

    node_table_save_file = node_table_save_path.format(node_id=node.id_)
    if not os.path.exists(node_table_save_file):
        response = llm.predict(
            prompt_rewrite, content=node.get_content(metadata_mode='llm')
        )
        pickle.dump(response, open(node_table_save_file, "wb"))
    else:
        response = pickle.load(open(node_table_save_file, "rb"))

    #把输出的 Markdown 表格文本转换为 Pandas 数据分析组件的 DataFrame 对象
    df = pd.read_csv(io.StringIO(response), sep="|", engine="python")
    return df
```

非结构化抽取器在产出表格类 Node 时，会把一段自然语言摘要先放在真正的 Markdown 表格前面，导致 Node 原生内容无法直接拿来用。借助大模型做内容抽取正是为了解决这个顺序问题。打印出来的一份原始表格 Node 长这样，如图 11-25 所示。

Table node content: 该表用于横向对比不同模型在多项评测上的表现。每行对应一个模型，每列对应一项指标，整体呈现出各模型在 MMLU、C-Eval、GSM8K 等维度上的分数差异，便于直观评估和横向比较。

| Model | MMLU | C-Eval | GSM8K | MATH | HumanEval | MBPP | BBH | CMMLU |

|---|---|---|---|---|---|---|---|---|

| GPT-4 | 86.4 | 69.9 | 92.0 | 45.8 | 67.0 | 61.8 | 86.7 | 71.0 |

| Llama2-7B | 46.8 | 32.5 | 16.7 | 3.3 | 12.8 | 20.8 | 38.2 | 31.8 |

| Llama2-13B | 55.0 | 41.4 | 29.6 | 5.0 | 18.9 | 30.3 | 45.6 | 38.4 |

| Llama2-34B | 62.6 | 42.2 | 6.2 | 22.6 | 33.0 | 44.1 | - | - |

| Llama2-70B | 69.8 | 50.1 | 54.4 | 10.6 | 23.7 | 37.7 | 58.4 | 53.6 |

| Mistral-7B | 64.1 | 47.4 | 47.5 | 11.3 | 27.4 | 38.6 | 56.7 | 44.7 |

| Mixtral-8x7B | 70.6 | 74.4 | 28.4 | 40.2 | 60.7 | - | - | - |

| Qwen1.5-7B | 61.0 | 74.1 | 62.5 | 20.3 | 36.0 | 37.4 | 40.2 | 73.1 |

| Qwen1.5-14B | 67.6 | 78.7 | 70.1 | 29.2 | 37.8 | 44.0 | 53.7 | 77.6 |

| Qwen1.5-32B | 73.4 | 83.5 | 77.4 | 36.1 | 37.2 | 49.4 | 66.8 | 82.3 |

| Qwen1.5-72B | 77.5 | 84.1 | 79.5 | 34.1 | 41.5 | 53.4 | 65.5 | 83.5 |

图 11-25

### 3）一级索引与递归检索器搭建

二级 Node 查询引擎准备好，并且用于构建一级向量索引的基础 Node，即 base_nodes 整理完毕后，就可以着手搭建一级向量索引与递归检索器了：

```python
#给基础 Node 构造一级向量索引
collection = chroma.get_or_create_collection(name=f"qwen1.5")
vector_store = ChromaVectorStore(chroma_collection=collection)
if not os.path.exists(f"./storage/vectorindex/qwen1.5"):
    print('Creating vector index...\n')
    storage_context = StorageContext.from_defaults(vector_store=vector_store)
    vector_index = VectorStoreIndex(base_nodes, storage_context=storage_context)
    vector_index.storage_context.persist(persist_dir=f"./storage/vectorindex/qwen1.5")
else:
    print('Loading vector index...\n')
    storage_context = StorageContext.from_defaults(persist_dir=f"./storage/vectorindex/qwen1.5", vector_store=vector_store)
    vector_index = load_index_from_storage(storage_context=storage_context)

#构造一级检索器
vector_retriever = vector_index.as_retriever(similarity_top_k=2)
```

```python
#构造递归检索器，实现递归检索
recursive_retriever = RecursiveRetriever(
    "vector",
    retriever_dict={"vector": vector_retriever},
    #node_dict=node_mappings,
    query_engine_dict=query_engine_dict,
    verbose=True,
)
```

这段实现先用基础 Node 产出向量索引和检索器，随后初始化一个递归检索器，检索起点设在一级索引的检索器上。注意这里没有传入 node_dict，因为向下探索时需要命中的是二级查询引擎而非原始 Node，所以把前面准备好的查询引擎字典传进去，让框架能够依据索引 Node 的 index_id 找到对应的查询引擎。

在递归检索器之上再封装查询引擎并做一次验证：

```python
query_engine = RetrieverQueryEngine.from_args(recursive_retriever)
response = query_engine.query('HumanEval 基准测试中，哪些模型参与了测试？平均分是多少？最高分是多少？')
pprint_response(response)
```

图 11-26 的运行输出能清晰看到递归检索的完整路径：先在一级索引里检索到一个索引 Node，随后 entering 进入对应的二级查询引擎，并由二级引擎完成最终的答案生成。

图 11-26

## 4. Node 链到 Agent 的递归检索

Node 链向 Agent 的递归检索，指的是在拿到基础 Node 之后，依据 Node 中保存的 Agent 引用继续向下探索，最终由 Agent 产出答案。思路上与 Node 到查询引擎的递归检索基本一致，只是后端换成了 Agent。整体原理见图 11-27 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_432_0.png)

图 11-27

本例以多篇文档为知识底座，为每篇文档都单独创建了一个 Agent。每个 Agent 内部可调用两类工具，一类是用于事实性问答的 RAG 查询引擎，另一类是用于内容归纳与摘要的 RAG 查询引擎，细节可参考 11.3.3 节。随后为每篇文档生成一个简短的摘要 Node，采用 IndexNode 类型，并把它与对应的后端 Agent 建立链接。该摘要 Node 负责构建一级向量索引并承担检索入口。

## 1）二级文档 Agent 的创建

这里选用若干城市介绍文档作为测试素材，为其创建可独立运行的查询 Agent。整体创建流程与 11.3.3 节类似，为节省篇幅省略了向量索引对象的显式构造：

```python
......
#创建针对某个文档的 Agent
def create_file_agent(file,name):

    print(f'Starting to create tool agent for 【{name}】...\n')

    #·····省略构造文档对应的向量索引对象的过程·····
```

```python
# vector_index = ...

#构造查询引擎
query_engine = vector_index.as_query_engine(similarity_top_k=3)

# 构造摘要索引
summary_index = SummaryIndex(nodes)
summary_engine = summary_index.as_query_engine(
    response_mode="tree_summarize")

# 将查询引擎 “工具化”
query_tool = QueryEngineTool.from_defaults(
    query_engine=query_engine,
    name=f'query_tool',
    description=f'Use if you want to query details
about {name}')
summary_tool = QueryEngineTool.from_defaults(
    query_engine=summary_engine,
    name=f'summary_tool',
    description=f'Use ONLY IF you want to get a holistic
summary of the documents. DO NOT USE if you want to query some details
about {name}.')

# 创建文档 Agent
file_agent = ReActAgent.from_tools([query_tool,summary_tool],
    verbose=True,
    system_prompt=f"""You are a specialized agent
designed to answer queries about {name}.You must ALWAYS use at least
one of the tools provided when answering a question; do NOT rely on
prior knowledge.DO NOT fabricate answer."""
)
    return file_agent
```

代码跑通后可以直接实例化一个 Agent 来检查可用性，测试用例如下：

```python
agent = create_file_agent('../../data/citys/南京市.txt','Nanjing')
agent.chat_repl()
```

对话效果见图 11-28。采用多轮交互的方式可以发现，Agent 能根据当前问题和上下文智能判定要调用 query_tool 还是 summary_tool，并据此决定下一步动作，最终完成问答闭环。

![](img/7eddb68cd925518fdc310e52e94ba9a9_434_0.png)

使用下面代码把各文档对应的 Agent 集中保存到一个字典中：

```python
names = ['Nanjing','Beijing','Shanghai']
files = ['../../data/citys/南京市.txt','../../data/citys/北京市.txt','../../data/citys/上海市.txt']

#创建不同的文档 Agent
print('========================================\n')
print('Creating file agents for different documents...\n')
file_agents_dict = {}
for name, file in zip(names, files):
    file_agent = create_file_agent(file, name)
    file_agents_dict[name] = file_agent
```

## 2）一级索引与递归检索器的构建

二级文档 Agent 创建完毕后即可进入一级索引与递归检索器的搭建。为了简化流程，这里不使用大模型动态生成摘要，而是直接用固定文本为一级索引创建所需的 Node：

```python
print('========================================\n')
print('Creating top level nodes from tool agents...\n')
index_nodes = []
query_engine_dict = {}

#给每个文档都构造一个索引 Node，用于搜索
for name in names:
    doc_summary = f"这部分内容包含关于城市{name}的维基百科文章。如果您需要
查找城市{name}的具体事实，请使用此索引。\n如果您想分析多个城市，请不要使用此
索引。"
    node = IndexNode(
        index_id = name,
        text=doc_summary,
    )
    index_nodes.append(node)

    #把 index_id与真正的 Agent 对应起来，用于在递归检索时查找
    #注意 Agent 也是一种查询引擎
    query_engine_dict[name] = file_agents_dict[name]

#构造一级索引与检索器
top_index = VectorStoreIndex(index_nodes)
top_retriever = top_index.as_retriever(similarity_top_k=1)

#构造递归检索器，从上面的 top_retriever 对象开始
#传入 query_engine_dict 变量，用于在递归检索时找到二级文档 Agent
recursive_retriever = RecursiveRetriever(
    "vector",
    retriever_dict={"vector": top_retriever},
    query_engine_dict=query_engine_dict,
    verbose=True,
)
```

为每篇文档各构造一个 IndexNode 类型的索引 Node，内部填充的是预先准备好的文本内容。实际项目中可以根据需求自行设计或生成索引 Node，核心要求是能够通过 index_id 定位到对应的文档 Agent。传入 query_engine_dict 后，递归检索器在检索时就能找到正确的文档 Agent，并由 Agent 产出最终答案。

在递归检索器之上再构建查询引擎并进行测试：

```python
query_engine = RetrieverQueryEngine.from_args(recursive_retriever)
response = query_engine.query('南京市有哪些著名的旅游景点呢？')
print(response)
```

测试期间的日志输出如图 11-29 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_436_0.png)

图 11-29

可以看到，先通过一级索引检索定位到目标，随后 entering:Nanjing 进入名为 Nanjing 的二级文档 Agent。Agent 内部遵循 ReAct 推理范式调用 RAG 查询引擎，问题最终得到正确回答。

## 第 12 章 构建端到端的企业级 RAG 应用

## 12.1 对生产型RAG应用的主要考量

RAG 从实验室原型走向企业生产，最大的落差不是模型好不好，而是工程化能不能扛住真实场景。到目前为止，我们已经厘清了基于大模型的 RAG 原理、典型架构和借助 LlamaIndex 等主流框架的搭建思路，足够把零散组件拼成一个可演示的原型。但要交付能在复杂企业环境里稳定运行的端到端方案，还必须补上几块关键拼图：

1. 索引构建与使用的解耦。生产里不会每次查询都重建索引，文档的采集、维护、清洗以及索引的创建、优化往往由专人或后台完成，前端只负责检索调用，二者需要独立但联动的机制来同步协作。
2. 前后端职责分离。企业级 Web 应用通常把 Agent、查询引擎、对话引擎等能力封装成服务 API，供前端消费，而不是把交互逻辑硬编码在管理后台。
3. 多用户与多租户隔离。原型阶段只考虑自己一个人用，正式共享后要面对多用户并行。需要对话引擎、记忆窗口、索引乃至知识库在不同用户间独立；在 SaaS 场景下，还要做到租户间资源、缓存、本地存储的严格隔离。
4. 非功能性要求。企业应用对灵活性、响应速度、扩展性、成本、体验、安全都更高。设计时要兼顾：
    1. 流式输出提升前端体感。
    2. 缓存与本地存储加速索引构建与维护。
    3. 按阶段选型合适的模型与向量库。
    4. 通过 Prompt 优化、流程调优、模型选择压降成本。
    5. 选用合适的 API 框架与流量控制保障并发体验。
    6. 提供充足的调试与追踪信息以便快速定位问题。

本章先给出企业级端到端 RAG 的典型架构，再结合 LlamaIndex 的技术栈，演示如何落地全栈 RAG 应用并解决上述痛点。

## 12.2 企业级端到端 RAG 架构概览

设想一个企业内部共享的智能知识问答助手：员工可通过自然语言检索私有知识库，如销售查报价方案、客服找投诉案例、管理层问业绩指标；外部客户也可经门户、呼叫中心、公众号/企业微信、App 等渠道进行售前售后咨询。同时管理后台要让管理员维护知识数据、管理检索索引、发布 API 并追踪调用日志。

完整架构如图 12-1 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_439_0.png)

图 12-1

### 12.2.1 数据存储层

负责 RAG 应用所有数据的持久化。典型的数据类型包括：

1. 原始知识与数据。涵盖内部文档、图片、视频、关系数据库的结构化数据、企业网站抓取的网页、内部 API 数据、用户上传文件等多种形态。
2. 向量索引。构建语义检索时先用嵌入模型生成向量并持久化，企业级场景建议使用 Milvus、Chroma 等专业向量库以支持存储、检索、备份恢复。
3. 运行期依赖数据。包括鉴权信息、账号资料、查询与对话记录、系统日志、导入转换的中间数据等。

常用存储形式有：
1. 关系数据库，存元数据与管理信息；
2. 向量库，存知识块向量；
3. 文档存储，存原始知识文件；
4. 其他存储，如图数据库存知识图谱。

该层通过专用管理接口向上层提供访问。

### 12.2.2 AI 模型层

RAG 依赖的模型主要分为：

1. 大模型，作为核心引擎，完成查询改写、答案生成、语义路由等。
2. 嵌入模型，将文档映射为高维向量用于检索。
3. 其他模型，如用于召回结果重排序的 Rerank 模型。

模型层以 API 形式对外暴露。在 LangChain、LlamaIndex 等框架中，LLM 组件已对底层差异做了封装，使用更灵活。

### 12.2.3 RAG 工作流与 API 模块

这是 RAG 的核心能力层，在 Web 形态下通常以服务 API 呈现，主要包含：

1. 检索前处理。从接收问题到正式检索前的改写、语义路由等，旨在提升召回精度与生成质量。可参考 8.1 节。
2. 检索。基于索引阶段构建的索引，采用合适的算法与范式召回相关知识块。可参考第 7 章、11.4 节。
3. 检索后处理。对召回结果做融合、去重、重排，最终组装进 Prompt 供生成器使用。可参考 8.2 节、11.4 节。
4. 生成。结合检索上下文与原问题生成最终回答。可参考第 7 章、第 9 章。
5. API 发布。用 FastAPI、Flask 等框架把检索、生成能力发布为 HTTP API，并提供安全与流量控制。常见接口包括对话、历史查询、文档上传、知识库管理等。

### 12.2.4 前端应用模块

通常基于 Vue、React 等框架实现，底层为 HTML/JavaScript/CSS。典型智能问答 UI 如图 12-2 所示，来自 LlamaIndex 官方演示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_442_0.png)

图 12-2

核心功能包括：

1. 账号管理。注册、绑定、登录、改密、登出等。
2. 对话与会话管理。通过 API 与后端交互实现连续对话，管理 Session、上下文、历史记录的归档备份删除等。可按业务拆分为知识对话、数据分析对话、自定义文档对话等。
3. ChatBot 管理。对不同风格、功能的智能体进行创建、配置、删除。
4. 配置项。提供 UI 风格、对话保存时长、本地目录等设置，部分应用还支持模型切换与 Prompt 自定义。

### 12.2.5 后台管理模块

端到端问答需要完善的后台，区分不同角色、把原型中的硬编码转为可配置。可分为：

#### 1. 索引与知识库管理模块
为前端运行提供前提。管理员可完成文档读取、加载、分词索引、假设问题生成、摘要生成等。核心是知识库创建、导入、索引、备份，还提供检索测试、模型配置、重复筛查等辅助功能。

#### 2. 应用与 API 管理模块
兼顾灵活性与可扩展性，部分平台提供可视化编排以应对复杂工作流，支持拖拽链接配置甚至嵌入代码，让管理员随时调整核心逻辑。

其余功能包括：
1. 多应用构建与配置，按业务需求管理多个应用实例。
2. API 发布、鉴权与日志管理，如端点与 Key 维护、调用日志查询监控。
3. 应用测试，支持模拟、跟踪、调试。

#### 3. 模型管理模块
管理大模型、嵌入模型、Rerank 模型，包含调用入口与参数配置。模型可为在线 API 模型或本地模型，复杂场景常多模型协同。因此需支持连通性测试、单次成本配置、调用日志追踪等。

#### 4. 系统管理模块
提供账号与权限管理、系统日志、存储目录管理、数据库连接参数配置等通用运维能力。

## 12.3 全栈 RAG 应用实践案例

本节通过两个全栈案例，结合 LlamaIndex 与 RAG 工程技术，演示如何落地并解决生产关键挑战：

- （1）索引构造与使用分离；
- （2）前后端服务分离；
- （3）多用户/多租户需求；
- （4）企业级非功能需求。

### 12.3.1 轻量级全栈 RAG 查询应用

基于 LlamaIndex 官方基础示例，搭建一个分布式、前后端完整的 RAG 查询演示。用户可在 UI 上传文档构建索引，向后台发送查询并查看生成结果与来源，也可浏览已索引文档列表。

#### 1. 技术栈
- （1）本地 Ollama 大模型 Qwen2。
- （2）OpenAI 嵌入模型 text-embedding-3-small。
- （3）Chroma 向量库。
- （4）LlamaIndex 框架。
- （5）FastAPI 搭建 API Server。
- （6）React + TypeScript 搭建前端页面。

本案例侧重前后端交互演示，未涉及多用户账号、数据隔离、关系数据库与并发控制。

#### 2. 后端模块之 Index Server

直接基于向量存储索引实现基础查询，不涉及高级检索、Agent 或复杂 RAG 范式。Index Server 是后端核心，提供文档上传与索引使用能力。

##### 1）整体模块设计
模块需对外提供可被前端调用的功能：
- （1）加载或初始化索引（initialize_index）。生产中不会每次查询重建索引，采用增量插入并持久化，启动时直接加载已有索引。
- （2）插入索引（insert_into_index）。接收用户上传文档，完成校验、读取、分块并写入现有索引。
- （3）查询索引（query_index）。基于已加载索引创建查询引擎，输入问题返回结果，采用默认检索器与生成器。
- （4）查询文档列表（get_documents_list）。维护已上传并索引成功的文档清单，减少重复上传。

##### 2）索引的共享访问设计
因索引可写，需考虑并发。两种思路：
一种是用户隔离，为每个用户分配独立索引空间与存储路径；另一种是共享索引，所有用户共用同一份索引并共同维护，后者即本文采用的方式，需通过锁机制保证顺序访问。

##### 3）独立的 Index Server 模块设计
可直接通过 HTTP API 暴露管理接口，但采用独立的 Index Server 模块，并在其中控制索引访问的串行化，通过远程调用供 API Server 使用。好处是解耦前后模块，便于单独调试调优，并支持分布式部署。

独立可远程调用的 Index Server 可借助 Python 的 multiprocessing.managers 模块实现。

##### 4）模块实现
先准备所需模型：

```python
import os
import pickle
import chromadb
from multiprocessing import Lock
from multiprocessing.managers import BaseManager
from llama_index.core import Settings, SimpleDirectoryReader, VectorStoreIndex, StorageContext, load_index_from_storage
from llama_index.embeddings.ollama import OllamaEmbedding
from llama_index.embeddings.openai import OpenAIEmbedding
from llama_index.vector_stores.chroma import ChromaVectorStore
from llama_index.llms.ollama import Ollama
from llama_index.llms.openai import OpenAI

#准备模型
llm_ollama = Ollama(model='qwen2')
embedded_model_openai = OpenAIEmbedding(model_name="text-embedding-3-small", embed_batch_size=50)
Settings.llm=llm_ollama
Settings.embed_model=embedded_model_openai
```

全局变量定义如下，index 为共享索引，stored_docs 记录上传文档信息，index_name 与 pkl_name 分别对应索引与文档清单的持久化路径，并新建 Lock 对象实现全局数据互斥访问：

```python
index = None
stored_docs = {}

# 确保线程安全
lock = Lock()

# 索引持久化存储的位置
index_name = "./saved_index"
pkl_name = "stored_documents.pkl"

# 索引服务端口
SERVER_PORT = 5602
```

下面就开始实现索引的加载与初始化逻辑，这部分接口并不向前端开放调用：

```python
# 初始化索引
def initialize_index():
    global index, stored_docs

    # 构造向量存储
    chroma = chromadb.HttpClient(host="localhost", port=8000)
    collection = \
        chroma.get_or_create_collection(name="chat_docs_collection")
    vector_store = ChromaVectorStore(chroma_collection=collection)

    # 注意使用 with lock 进行互斥的索引访问
    with lock:
        # 如果已经存在持久化存储的数据, 那么加载
        if os.path.exists(index_name):
            storage_context = StorageContext.from_defaults(
                persist_dir=index_name,
                vector_store=vector_store)
            index = load_index_from_storage(storage_context=
                storage_context)
        else:
            storage_context = StorageContext.from_defaults(
                vector_store=vector_store)

        # 首次构造空的索引, 后面再插入
        index = VectorStoreIndex([], storage_context=
            storage_context)
    index.storage_context.persist(persist_dir=index_name)

#将已经上传的文档信息从存储的文档中读取到内存
if os.path.exists(pkl_name):
    with open(pkl_name, "rb") as f:
        stored_docs = pickle.load(f)
```

这段代码的思路在查询引擎和 Agent 的示例里经常能见到，核心就是把索引持久化到本地，后续直接从本地恢复使用。第一次构建时先得到一个空索引，之后再逐步写入数据。

紧接着基于已有的索引创建查询引擎，并对外提供查询能力：

```python
#定义查询索引的方法
def query_index(query_text):
    """Query the global index."""
    global index
    response = index.as_query_engine().query(query_text)
    return response
```

现在要做的是索引的写入功能。它的作用是在用户上传新知识文档后，自动读取、切分文档，生成向量并追加进现有索引中。调用时需要传入文档路径，同时支持手动指定文档 ID，也支持让系统自动生成。

```python
#在已有的索引中插入新的文档对象
def insert_into_index(doc_file_path, doc_id=None):
    """在已有的索引中插入新的文档对象."""
    global index, stored_docs
    document = SimpleDirectoryReader(input_files=[doc_file_path]).load_data()[0]
    if doc_id is not None:
        document.doc_id = doc_id

#使用 with lock 实现共享对象的互斥（顺序）访问
with lock:
    index.insert(document)
    index.storage_context.persist(persist_dir=index_name)

#这里简化使用，只读取前 200 个字符
stored_docs[document.doc_id] = document.text[0:200]  # only take the first 200 chars

with open(pkl_name, "wb") as f:
    pickle.dump(stored_docs, f)

return
```

这里要特别注意 with lock 的用法：全局的索引对象和 stored_docs 在被访问时，需要通过锁来保证互斥，这样多用户并发操作时也能按顺序执行，避免冲突和异常。因为 Index Server 会被多用户远程调用，会产生多个并发工作线程，若没有加锁保护共享索引，数据安全就无法保证。

另外还有两点需要留意。

- （1）出于简化考虑，上传文档时只取了加载后返回的第一个 Document 对象。对纯文本 TXT 来说通常够用，但对 PDF 这类可能被解析成多个 Document 的格式就不够了。这种情况可以改成循环处理，不过要确保 doc_id 的唯一性不被破坏。
- （2）该方法的主要参数 doc_file_path 指的就是上传的知识文档。理论上 Index Server 可以和 API Server 分离部署，如果部署在不同的物理机上，就必须保证 Index Server 能够访问到该路径下的文件，比如放在共享网络存储里，并通过自定义的读取器来加载。

最后再补一个查询已构建索引文档清单的方法：

```python
def get_documents_list():
    """Get the list of currently stored documents."""
    global stored_docs
    documents_list = []
    for doc_id, doc_text in stored_docs.items():
        documents_list.append({"id": doc_id, "text": doc_text})

return documents_list
```

准备工作完成后，就可以运行 Index Server 模块的主程序：

```python
if __name__ == "__main__":

    #初始化索引
    print("initializing index...")
    initialize_index()

    #构造 manager
    print(f'Create server on port {SERVER_PORT}...')
    manager = BaseManager(('', SERVER_PORT), b'password')

    #注册函数
    print("registering functions...")
    manager.register('query_index', query_index)
    manager.register('insert_into_index', insert_into_index)
    manager.register('get_documents_list', get_documents_list)
    server = manager.get_server()

    #启动 server
    print("server started...")
    server.serve_forever()
```

这部分主要借助 Python 的 multiprocessing 库来实现远程调用。

- （1）先创建一个 BaseManager 类型的管理器，设定监听端口和连接密码。
- （2）通过 register 方法把需要暴露的函数注册上去，这些函数就能按名字被远程调用。
- （3）生成 server 实例，用 serve_forever 方法持续监听并响应请求。

serve_forever 在收到调用请求时会新建一个处理线程来执行，执行完毕后线程自动结束。

##### 5）模块启动

到这里，一个提供索引相关服务的分布式 server 就搭建完成了，可以直接启动，如图 12-3 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_451_0.png)

![](img/7eddb68cd925518fdc310e52e94ba9a9_452_0.png)

若想先做简单验证，可以用下面的代码进行单元测试：

```python
from multiprocessing.managers import BaseManager

def test_query_index():
    manager = BaseManager(('', 5602), b'password')
    manager.register('query_index')
    manager.connect()
    response = manager.query_index('你好！')._getvalue()
    print(response)

if __name__ == "__main__":
    test_query_index()
```

通常情况下，Index Server 的函数都是为 API 准备的，所以在开发 API 的时候一起联调测试即可。

#### 3. 后端模块之 API Server

接下来用 FastAPI 来搭建后端的 API 层。FastAPI 是一个现代化的高性能 Web 框架，能够快速构建 Python 版的 API Server。它自带自动化文档生成，通过 Pydantic 对请求数据进行格式和类型校验，支持异步请求和高并发处理，能让 API 的测试与部署更高效。

##### 1）API 设计

本示例后端会实现以下几个简易接口，见表 12-1。

| API | 类型 | 端点 | 参数说明 | 请求体 |
| :--- | :--- | :--- | :--- | :--- |
| 测试 | GET | / | 无 | 无 |
| 问题查询 | GET | /query | text: 输入问题 | 无 |
| 文档上传 | POST | /uploadFile | filename_as_doc_id: 文档名是否作为 doc_id | file: 需要上传的文档 |
| 文档查询 | GET | /getDocuments | 无 | 无 |

## 2）API 实现

先准备一些基础代码。测试环境下开启 CORS 跨域，同时与前面搭建好的 Index Server 建立连接：

```python
import os
from multiprocessing.managers import BaseManager
from werkzeug.utils import secure_filename
from fastapi import FastAPI, Request, UploadFile, File
from fastapi.responses import JSONResponse
from fastapi.exceptions import RequestValidationError
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
from typing import List
import os
from multiprocessing.managers import BaseManager
import uvicorn

app = FastAPI()

# 启用 CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

# 连接 Index Server 模块
manager = BaseManager(('', 5602), b'password')
manager.register('query_index')
manager.register('insert_into_index')
manager.register('get_documents_list')
manager.connect()

@app.get("/")
def home():
    return "Hello, World!"

@app.exception_handler(RequestValidationError)
async def validation_exception_handler(request, exc):
    return JSONResponse(content="Invalid request parameters",
                        status_code=400)
```

注意：连接 Index Server 时使用的端口和密码必须与服务端设置一致，否则会报错。register 注册过的函数可以通过 manager 远程调用，调用方式和本地函数一致，例如 `manager.query_index(...)`。

随后实现一个简单的 hello 接口，用于验证 API Server 是否连通。再写一个异常处理函数，在参数错误时返回状态码 400 的响应。

下面依次实现各个接口。

（1）问题查询接口。问题查询接口的实现如下：

```python
@app.get("/query/")
def query_index(request: Request, query_text: str):
    global manager
    if query_text is None:
        return JSONResponse(content="No text found, please include a ?text=blah parameter in the URL", status_code=400)

    response = manager.query_index(query_text).getvalue()
    response_json = {
        "text": str(response),
        "sources": [{"text": str(x.text),
                     "similarity": round(x.score, 2),
                     "doc_id": str(x.id_)
                     } for x in response.source_nodes]
    }
    return JSONResponse(content=response_json, status_code=200)
```

该接口采用 HTTP GET 方式，只需带上一个参数 `query_text` 作为查询问题。FastAPI 会自动从 URL 中解析出 GET 参数。

拿到参数后，通过 `manager` 远程调用 `query_index` 方法，并用 `getvalue` 取回结果，最后把内容封装成 JSON 返回。`JSONResponse` 是 FastAPI 的响应类型，可把数据以 JSON 格式返回给客户端。

（2）文档上传接口。文档上传接口的实现如下：

```python
@app.post("/uploadFile")
async def upload_file(request: Request, file: UploadFile = File(...),
filename_as_doc_id: bool = False):
    global manager
    try:
        contents = await file.read()
        filepath = os.path.join('documents', file.filename)
        with open(filepath, "wb") as f:
            f.write(contents)

        if filename_as_doc_id:
            manager.insert_into_index(filepath,
            doc_id=file.filename)
        else:
            manager.insert_into_index(filepath)
    except Exception as e:
        if os.path.exists(filepath):
            os.remove(filepath)
        return JSONResponse(content="Error: {}".format(str(e)),
        status_code=500)

        if os.path.exists(filepath):
            os.remove(filepath)

    return JSONResponse(content="File inserted!", status_code=200)
```
文档上传功能通过 HTTP POST 方式暴露，接收的两个核心参数是：

- file：类型为 `UploadFile`，承载客户端上传的原始文件。`UploadFile` 是 FastAPI 提供的上传类型，自带便捷的读取方法。
- filename_as_doc_id：布尔类型，决定是否把文件名直接用作 Index Server 中 Document 对象的 `doc_id`，若选择否则由框架自动生成。

拿到文档对象后，利用 `UploadFile` 的 `read` 方法把内容读出并写入本地 `documents` 目录下的临时文件。随后远程调用 `insert_into_index` 方法完成文档解析与向量入库，让应用具备对该文档的 RAG 查询能力。无论执行成功与否，流程结束时都会清理掉临时文件。

（3）文档查询接口。最后提供一个无需参数的查询入口，直接远调 `get_documents_list` 方法即可获取已索引文档清单：

```python
@app.get("/getDocuments")
def get_documents(request: Request):
    document_list = manager.get_documents_list().getvalue()
    return JSONResponse(content=document_list, status_code=200)
```

## 3) API 启动与校验
先编写启动脚本，注意端口空闲且防火墙放行：

```python
if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=5601)
```

随后启动服务：

```bash
> python fast_api.py
```

当控制台出现如 图 12-4 的提示，就表示服务已正常运行并在等待请求。

![](img/7eddb68cd925518fdc310e52e94ba9a9_456_0.png)

图 12-4

接下来可以对服务进行验证。

FastAPI 内置自动文档，通过 `/docs` 路径即可打开交互式 API 文档，里面列出每个端点说明、请求结构、响应结构及示例。按当前配置，可访问本地地址查看：

```
http://localhost:5601/docs
```

此时应能看到如 图 12-5 的文档页面。

![](img/7eddb68cd925518fdc310e52e94ba9a9_457_0.png)

图 12-5

点开右侧的 “∨” 查看接口详情，再点 “Try it out” 即可在页面中填入参数进行试跑，如 图 12-6。

![](img/7eddb68cd925518fdc310e52e94ba9a9_457_1.png)

图 12-6

这种方式很适合在交付给前端前做模拟验证，确保输入输出正确，也可及时调整。验证后也可以借助 Postman 等 HTTP 工具进行检查。全部通过后即可把 API 交给前端使用。

## 4. 前端 Web UI 模块
还需要一个前端 Web UI 来完成用户交互，并通过后端 API Server 调用核心逻辑。常见的技术选型是 Vue.js 或 React+Next.js，配合 Ant Design、Element UI、Naive UI 等组件库，使用 JavaScript/TypeScript 开发。

本节不展开前端开发教学，假设已有基础。我们在 LlamaIndex 官方提供的简易 Web UI 上做改造，重点说明如何与后端 API 交互。该演示应用基于 React+TypeScript+JSX 构建。

![](img/7eddb68cd925518fdc310e52e94ba9a9_458_0.png)

图 12-7

React 由 Facebook 开源，用于构建复杂用户界面，核心思想是组件化，实现高效更新渲染。JSX 是 JavaScript 的语法扩展，让开发者能用类 HTML 的标记编写组件代码，专为 React 可视化开发而生。

### 1) Web UI 搭建
最推荐使用 Next.js 等全栈 React 框架，也可直接用 create-react-app 脚手架快速生成项目。执行命令并加上 `template typescript` 参数即可创建带 TypeScript 支持的完整 React 应用，项目自带启动、打包、测试等管理命令。

```bash
> npx create-react-app my-app --template typescript
```

生成的代码结构通常如 图 12-8。

![](img/7eddb68cd925518fdc310e52e94ba9a9_459_0.png)

图 12-8

在此基础上可自行开发 UI 组件，并实现与后端 API 的联动。下面聚焦交互部分。

### 2) 后端 API 调用
前端建议把所有后端调用逻辑集中管理，例如本演示将 API 请求放在 `src/apis` 目录下，如 图 12-9 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_460_0.png)

以 `queryIndex.tsx` 为例，实现了问题查询的调用函数：

```typescript
export type ResponseSources = {
  text: string;
  doc_id: string;
  similarity: number;
};

export type QueryResponse = {
  text: string;
  sources: ResponseSources[];
};

const queryIndex = async (query: string): Promise<QueryResponse> => {
  const queryURL = new URL('http://localhost:5601/query?');
  queryURL.searchParams.append('query_text', query);

  const response = await fetch(queryURL, { mode: 'cors' });
  if (!response.ok) {
    return { text: 'Error in query', sources: [] };
  }

  const queryResponse = (await response.json()) as QueryResponse;

  return queryResponse;
};
```

```typescript
export default queryIndex;
```

代码先声明响应类型，与 API 返回的 JSON 结构一致，包含文本内容 `text` 字段和参考来源 `sources` 列表。随后实现异步函数 `queryIndex`，入参为查询问题 `query`，出参为 `QueryResponse` 类型。因为是异步调用，实际返回的是 Promise，以便调用方用 `await` 等待结果。函数内部组装请求 URL、发送异步请求、等待响应并解析为 JSON 后返回。

实现该函数后，就可以在 UI 中调用以获取结果并展示。下面以 IndexQuery 组件为例：

```typescript
import queryIndex, { ResponseSources } from '../apis/queryIndex';
const IndexQuery = () => {
  //...
  const handleQuery = (e: React.KeyboardEvent<HTMLInputElement>) => {
    if (e.key == 'Enter') {
      setLoading(true);

      //调用 queryIndex 函数
      queryIndex(e.currentTarget.value).then((response) => {
        setLoading(false);

        //设置响应结果, 同步前端 UI 状态
        setResponseText(response.text);
        setResponseSources(response.sources);
      });
    }
  };
  //...
  return (
    <div className='query'>
      <div className='query__input'>
        <label htmlFor='query-text'>输入你的问题</label>
        <input
          type='text'
          name='query-text'
```

```javascript
placeholder='你的问题'
onKeyDown={handleQuery}
></input>
</div>
```

用户在页面输入问题后触发 `handleQuery` 方法，通过封装好的 API 函数与后端交互并把结果渲染到页面。

## 3) Web UI 启动与测试
开发环境下通过 `npm start` 启动 React 应用：

```
> npm start
```

启动后访问 http://localhost:3000/ 进入演示页面。应用初始化时会调用 `getDocuments` API 拉取并展示已索引的文档列表。输入问题后回车即可触发查询接口。若首次测试，至少需先上传一份知识文档用于 RAG。

图 12-10 所示为测试页面，这里已上传两份文档构建向量索引。

![](img/7eddb68cd925518fdc310e52e94ba9a9_462_0.png)

## 5. 总结
以上完成了一个端到端的全栈 RAG 原型。可以看到它与单一模块的区别：

- 前端 UI、后端 API、知识库与索引服务分离。
- 分布式设计，采用独立的索引服务器。
- 持久化存储与动态增量建索引，而非一次性生成。
- 兼顾多用户并发，需对索引操作做顺序控制。

目前仅为演示原型，可在此基础上扩展多用户独立知识库、多轮对话、文档上传到网络共享存储、独立的知识库后台管理等能力。

## 12.3.2 多文档 Agent 端到端对话
承接此前多文档 Agentic RAG，本节将其升级为端到端对话应用。通过 API 把多文档 Agent 能力暴露给前端，支持从事实性到总结性、单文档到跨文档的提问，并实现连续对话。

重点包括：

- 多文档下复杂知识型 Agent 架构。
- 基于 Agent 构建支持连续对话的 ChatBot 前端。
- API 实现的流式输出。
- 多用户/多租户下的查询与对话引擎。

## 1. 架构概览
在原有架构上扩展为完整端到端形态，如 图 12-11。

![](img/7eddb68cd925518fdc310e52e94ba9a9_464_0.png)

主要包含三个模块：

- 后端 Agent 模块：为已有多文档构建索引与查询引擎，并以查询引擎为工具组装上层 Agent，能力通过 API 导入使用。
- 后端 API 模块：面向前端的接口，前端可通过它与 Agent 对话，向底层多文档提问。
- 前端 UI 应用：简易连续对话 ChatBot，支持与后端 API 交互并实现流式响应。

后端目录结构如 图 12-12。

![](img/7eddb68cd925518fdc310e52e94ba9a9_465_0.png)

- `api` 目录存放面向前端的 API 服务代码。
- `app` 目录存放后端查询引擎与 Agent 代码。
- `data` 目录存放原始知识文档，用于增强生成。
- `main.py` 为后端主入口，用于启动 API 服务。

技术栈沿用 12.3.1 节，测试数据采用维基百科中国部分城市介绍。

## 2. 后端 Agent 核心
准备工作与此前多文档 Agentic RAG 大体一致，下文省略部分实现。

### 1) 构建单文档 Tool Agent
如 图 12-11 所示，Tool Agent 针对单个文档创建，基于不同索引对应的查询引擎构建，仍使用向量存储索引处理事实性问题与摘要索引处理内容总结。创建单文档 Agent 的代码如下，省略 `vector_index` 构造过程，可参考 11.3.3 节：

```python
......
......省略模型与向量存储的准备代码......

# 目录
HOME_DIR='/Users/pingcy/src/multiagents/backend'
DATA_DIR = f'{HOME_DIR}/data'
STOR_DIR = f'{HOME_DIR}/app/storage'

#本应用的知识文档
city_docs = {
    "Beijing":f'{DATA_DIR}/北京市.txt',
    "Guangzhou":f'{DATA_DIR}/广州市.txt',
    "Nanjing":f'{DATA_DIR}/南京市.txt',
    "Shanghai":f'{DATA_DIR}/上海市.txt'
}

#创建针对单个文档的 Tool Agent
def _create_doc_agent(name:str,callback_manager: CallbackManager):
    file = city_docs[name]

    ······此处省略构造 vector_index 对象的过程·····
    #vector_index = ...

    query_engine = vector_index.as_query_engine(similarity_top_k=5)

    # 构造摘要索引与查询引擎
    summary_index = SummaryIndex(nodes)
    summary_engine = summary_index.as_query_engine(
        response_mode="tree_summarize")

    # 把两个查询引擎工具化
    query_tool = QueryEngineTool.from_defaults(query_engine=query_engine,
        name=f'query_tool',
        description=f'用于回答关于城市{name}的具体问题，包括经济、旅游、文化、历史等方面')
    summary_tool = QueryEngineTool.from_defaults(query_engine=summary_engine,
        name=f'summary_tool',
        description=f'任何需要对城市{name}的各个方面进行全面总结的请求请使用本工具。如果您想查询有关 {name}的某些详细信息，请使用 query_tool')

    city_tools = [query_tool,summary_tool]

    # 使用两个工具创建单独的文档 Agent
    doc_agent = ReActAgent.from_tools(city_tools,
        verbose=True,
        system_prompt=f'你是一个专门设计用于回答有关城市{name}信息查询的助手。在回答问题时，你必须始终使用至少一个提供的工具；不要依赖先验知识。不要编造答案。',
        callback_manager=callback_manager)
    return doc_agent
```

单 Tool Agent 的搭建思路与 11.3.3 节里多文档 Agent 的做法保持一致。当前选用的是 ReActAgent，这类 Agent 通用性强，无需依赖模型的函数调用能力。若想切换，也可以选用 OpenAIAgent，前提是底层大模型要支持函数调用。

下面给出批量为每个文档实例化 Tool Agent 的循环写法：

```python
#循环创建针对所有文档的 Tool Agent，并将其保存到 doc_agents_dict 字典中
def _create_doc_agents(callback_manager: CallbackManager):

    print('Creating document agents for all citys...\n')
    doc_agents_dict = {}
    for city in city_docs.keys():
        doc_agents_dict[city] = _create_doc_agent(city,callback_manager)

    return doc_agents_dict
```

CallbackManager 这个参数主要是用来追踪运行时的细节，比如大模型的调用轨迹与返回内容。此处先留出接口，后续就能基于它实现 Agent 中间过程的监控与流式推送。

### 2）创建 Top Agent

在此基础上组装 Top Agent，参考了 11.3.3 节的实现，形成了一个具备工具检索能力的顶层 Agent：

```python
def _create_top_agent(doc_agents: Dict,callback_manager: CallbackManager):
    all_tools = []
    for city in doc_agents.keys():
        city_summary = (
            f" 这部分包含了有关{city}的城市信息。 "
            f" 如果需要回答有关{city}的任务问题，请使用这个工具.\n"
        )

    #把创建好的每个 Tool Agent 都工具化
    doc_tool = QueryEngineTool(
        query_engine=doc_agents[city],
        metadata=ToolMetadata(
            name=f"tool_{city}",
            description=city_summary,
        ),
    )
    all_tools.append(doc_tool)

    #实现一个对象索引，用于检索工具
    tool_mapping = SimpleToolNodeMapping.from_objects(all_tools)
    if not os.path.exists(f"{STOR_DIR}/top"):
        storage_context = StorageContext.from_defaults()
        obj_index = ObjectIndex.from_objects(
            all_tools,
            tool_mapping,
            VectorStoreIndex,
            storage_context=storage_context
        )
        storage_context.persist(persist_dir=f"{STOR_DIR}/top")
    else:
        storage_context = StorageContext.from_defaults(
            persist_dir=f"{STOR_DIR}/top"
        )
        index = load_index_from_storage(storage_context)
        obj_index = ObjectIndex(index, tool_mapping)

    print('Creating top agent...\n')

    #创建 Top Agent
    top_agent = ReActAgent.from_tools(
        tool_retriever=obj_index.as_retriever(similarity_top_k=3),
        verbose=True,
        system_prompt="你是一个被设计来回答关于一组给定城市查询的助手。请始终使用提供的工具来回答一个问题。不要依赖先验知识。不要编造答案",
        callback_manager=callback_manager)
    return top_agent
```

先把底层的 Tool Agent 包装成工具，再用这些工具去创建 Top Agent。并非把全部工具一次性塞给 Top Agent，而是只提供一个 tool_retriever。面对海量工具时，先用检索器做一次筛选过滤，能显著提升推理的准确率。

### 3）实现 get_agent 方法并测试

对 Agent 的获取做一个轻量封装，这个方法后续会暴露给 API 层调用：

```python
def get_agent():

    #创建 Agent，此处暂时忽略 callback_manager
    callback_manager = CallbackManager()
    doc_agents = _create_doc_agents(callback_manager)
    top_agent = _create_top_agent(doc_agents,callback_manager)

    return top_agent
```

获取方法可以先用下面这段代码做简单验证：

```python
if __name__ == '__main__':

    top_agent = get_agent()
    print('Starting to stream chat...\n')
    streaming_response = top_agent.streaming_chat_repl()
```

交互测试时使用 streaming_chat_repl 来进行对话，并演示流式输出的效果，如图 12-13 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_469_0.png)

测试表明 Agent 的推理与查询行为符合预期，可以正式移交给 API Server 模块使用。

## 3. 后端 API 模块

### 1) API 设计

后端仍基于 FastAPI 搭建，核心对外接口是负责对话的 chat 接口。客户端发送对话信息，服务端调用 Agent 并返回结果。接口概览见表 12-2。

表 12-2

| 接口 | 类型 | 端点 | 参数说明 | 请求体 |

| :--- | :--- | :--- | :--- | :--- |

| 对话 | Post | /api/chat | 无 | data: ChatData |

先说明该接口的入参结构：

```python
#单个消息: 角色与内容
class _Message(BaseModel):
    role: MessageRole
    content: str

#接口数据: 消息的顺序列表
class _ChatData(BaseModel):
    messages: List[_Message]
```

入参设计沿用了主流大模型对话 API 的惯例。熟悉大模型开发的人对 _Message 应该不陌生，它描述了一条会话消息，role 表示消息来源，user 为用户，assistant 为 AI，system 为系统指令，content 为具体内容。

_ChatData 本质上是 _Message 的列表，也就是一次完整的对话历史。携带完整历史的方式也是大模型多轮对话的常用做法，能让模型基于上下文持续交流。

这种入参形式也与后面前端 UI 采用的组件保持一致。

### 2）API 实现之非流式版本

先落地一个不支持流式的版本。推理或生成复杂度较高时，客户端需要等待更久。实现如下：

```python
......
chat_router = r = APIRouter()

@r.post("/nostream")
async def chat_nostream(
    data: _ChatData
):
    #获得 Agent
    agent = get_agent()

    if len(data.messages) == 0:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="No messages provided",
        )

    #最后一个产生消息的角色必须是 user
    lastMessage = data.messages.pop()
    if lastMessage.role != MessageRole.USER:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Last message must be from user",
        )

    # 创建消息历史
    messages = [
        ChatMessage(
            role=m.role,
            content=m.content,
        )
        for m in data.messages
    ]

    #调用 Agent 获得响应结果并返回
    chat_result = agent.chat(lastMessage.content, messages)
```

return JSONResponse(content={"text": str(chat_result)}, status_code=200)

对比之前简易的全栈 RAG 查询应用，这里有两个明显变化。

（1）路由的注册方式不同。没有直接在 FastAPI 的 App 实例上挂载路由，而是统一放到 APIRouter 上。这样在大型项目里可以按模块拆分路由，代码更清晰、扩展也更方便。拆分后需要通过 include_router 导入到主程序。本项目用 main.py 来启动服务，其内容为：

```python
import logging
import os
import uvicorn
from backend.api.chat import chat_router
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from dotenv import load_dotenv

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"])

# 将 chat_router 路由包含进来，有利于路由的模块化组织管理
app.include_router(chat_router, prefix="/api/chat")

if __name__ == "__main__":
    uvicorn.run(app="main:app", host="0.0.0.0", port=8090, reload=True)
```

（2）因为 get_agent 每次返回的 Agent 都是无状态的，所以 chat 调用必须由客户端带上完整的历史。做法与调用大模型 API 相同：收到消息列表后，转换成 Agent 需要的 ChatMessage 列表再传入 chat，同时会校验最后一条消息的角色必须是 user，否则直接抛异常。

借助 FastAPI 自带的文档页面对该非流式接口进行验证，输入示例如图 12-14 所示。

![](bbox=[260, 247, 739, 420])

图 12-14

等待一段时间后，返回结果如图 12-15 所示。

```json
{
    "text": "南京的人口大约有800万人。"
}
```

图 12-15

随后把这轮对话追加到历史记录，再补充一条新用户提问来模拟多轮交互，如图 12-16 所示。

```json
{
    "messages": [
        {
            "role": "user",
            "content": "南京有多少人口？"
        },
        {
            "role": "assistant",
            "content": "南京的人口大约有800万人。"
        },
        {
            "role": "user",
            "content": "那广州呢？比南京相比如何？"
        }
    ]
}
```

图 12-16

此时 Agent 会结合上下文还原本轮的真实意图，推断工具使用的顺序，最终给出正确回答，如图 12-17 所示。

```json
{
  "text": "广州的人口在2021年底约为1881万人,人口比南京多很多。南京大约有800万人口,而广州有1881万人口。"
}
```

![](img/7eddb68cd925518fdc310e52e94ba9a9_474_0.png)
后端 API Server 的日志输出同样佐证了 Agent 的推理轨迹，细节可参看图 12-18。

![](img/7eddb68cd925518fdc310e52e94ba9a9_474_1.png)
## 3）基础流式接口的实现
前面演示的是非流式接口。在实际产品里，Agent 的响应往往较长、步骤较多，客户端体验会更依赖逐字推送。为了做到这一点，可以借助两类工具。

- （1）借助 FastAPI 提供的 StreamingResponse 类型完成流式返回。
- （2）借助 Server-Sent Events 技术实现服务端向客户端的异步推送，客户端需要配合。

Server-Sent Events 是一种基于 HTTP 的单向实时推送机制，服务端可主动持续推送数据给客户端，无需客户端反复请求。常见于需要多次推送的场景，例如流式生成、大文件分片推送等。

本小节先给出一个最简的流式接口，把此前调用 Agent 的 chat 方法换成 stream_chat，并对返回流做适配处理：

```python
# 流式响应 chat 接口
@r.post("")
async def chat(
    data: _ChatData
```

```python
):
    # 创建消息历史
    messages = [
        ChatMessage(
            role=m.role,
            content=decode_sse_messages(m.content),
        )
        for m in data.messages
    ]

    # 调用 stream_chat 方法获取 Agent 的响应流
    chat_result = agent.stream_chat(lastMessage.content, messages)

    # 构造一个生成器，用于迭代处理输出的 token
    def event_generator():
        for token in chat_result.response_gen:
            yield convert_sse(token)

    # 客户端流式响应
    return StreamingResponse(event_generator(),
                             media_type="text/event-stream")
```
下面对关键环节做展开说明。

(1) LlamaIndex 框架的流式结果，比如 Agent 的 stream_chat 返回值，可以先经过 response_gen 函数封装成生成器。随后自定义一个 event_generator 生成器函数，利用 yield 逐步产出数据，每次 yield 后暂停等待下一次调用，逐 token 消费流式结果并 yield 出去。

(2) 采用 SSE 向客户端推送，响应媒体类型为 text/event-stream。SSE 规范要求每条消息以 “data:” 开头，因此需要用 convert_sse 函数把业务消息转成符合 SSE 格式的文本：

```python
def convert_sse(obj: str | dict):
    return "data: {}\n\n".format(json.dumps(obj, ensure_ascii=False))
```

(3) FastAPI 自带的 StreamingResponse 只需传入上述生成器即可完成流式返回。该对象会持续从生成器取 SSE 格式的 token，并主动推送给前端。

## 4) 增强版流式接口的实现
Agent 通常要经历多轮推理和工具调用，最后才进入文本生成阶段。常规流式方案只能在最后生成阶段输出，中间的思考与调用过程对用户不可见。若想在最终输出前就把 Agent 的执行态实时告知客户端，就需要借助 LlamaIndex 內建的事件追踪与分发机制，通过事件处理器捕获内部事件。

以跟踪工具使用为例，比如 Function Call 事件的输入输出，并在 UI 上实时呈现，官方推荐的做法如图 12-19。

![](img/7eddb68cd925518fdc310e52e94ba9a9_476_0.png)
思路概括如下：利用框架自带的事件追踪与分发，通过自定义事件处理器在关注的中间事件触发时，把事件信息写入异步队列，再由独立处理线程从队列取数并实时推送到客户端。这种方式复用现有机制，借助队列解耦，对主流程侵入较小。

大致步骤：

- ① 编写自定义事件处理器，把关注事件写入异步队列。
- ② 在创建 Agent 时传入该事件管理器使其生效。
- ③ API 调用时读取队列中的事件，实时生成响应。

### (1) 自定义事件处理器的定义
事件回调的基础抽象是 BaseCallbackHandler。需要在此基础上派生一个流式事件处理器，实现把事件写入异步队列的功能：

```python
# 自定义一个事件类型, 包含类型与事件信息
class EventObject(BaseModel):
    type: str
    payload: dict

# 自定义流跟踪的事件处理器
class StreamingCallbackHandler(BaseCallbackHandler):
    def __init__(self, queue: Queue) -> None:
        super().__init__([], [])
        self._queue = queue

    # 自定义 on_event_start 接口
    def on_event_start(
        self,
        event_type: CBEventType,
        payload: Optional[Dict[str, Any]] = None,
        event_id: str = "",
        parent_id: str = "",
        **kwargs: Any,
    ) -> str:
        # 跟踪 Agent 的工具使用
        if event_type == CBEventType.FUNCTION_CALL:
            self._queue.put(
                EventObject(
                    type="function_call",
                    payload={
                        "arguments_str": str(payload["function_call"]),
                        "tool_str": str(payload["tool"].name),
                    },
                )
            )

    # 自定义 on_event_end 接口
    def on_event_end(
        self,
        event_type: CBEventType,
        payload: Optional[Dict[str, Any]] = None,
        event_id: str = "",
        **kwargs: Any,
    ) -> None:
        # 跟踪 Agent 的工具调用
        if event_type == CBEventType.FUNCTION_CALL:
            self._queue.put(
                EventObject(
                    type="function_call_response",
                    payload={"response": payload["function_call_response"]},
                )
            )

        # 跟踪 Agent 每个步骤的大模型响应
        elif event_type == CBEventType.AGENT_STEP:
            self._queue.put(payload["response"])

    @property
    def queue(self) -> Queue:
        """Get the queue of events."""
        return self._queue

    def start_trace(self, trace_id: Optional[str] = None) -> None:
        """Run when an overall trace is launched."""
        pass

    def end_trace(
        self,
        trace_id: Optional[str] = None,
        trace_map: Optional[Dict[str, List[str]]] = None,
    ) -> None:
        """Run when an overall trace is exited."""
        pass
```
该处理器主要关注两类事件。

- ① FUNCTION_CALL：Agent 调用工具时触发，ReActAgent 会在工具使用时派发，OpenAIAgent 在 Function Call 时触发。可据此实时拿到工具调用的输入与输出。
- ② AGENT_STEP：Agent 完成一次执行步骤后派发，通常在拿到工具结果并经大模型整理后触发。跟踪它即可拿到最终输出。

实现里，每次 FUNCTION_CALL 开始与结束都会把调用参数和返回结果打包成 EventObject 放进队列；每次 AGENT_STEP 结束会把 payload["response"] 放入队列。这样调用 Agent 时就能通过队列实时感知内部进展。

### (2) 启用自定义的事件管理器
后端 Agent 模块的 get_agent 方法中之前留空的 callback_manager 参数，现在用来注入自定义事件管理器，其余逻辑无需改动：

```python
def get_agent():
    # 构造并加入自定义的事件处理器
    queue = Queue()
    handler = StreamingCallbackHandler(queue)
    callback_manager = CallbackManager([handler])

    # 使用自定义的事件处理器
    doc_agents = _create_doc_agents(callback_manager)
    top_agent = _create_top_agent(doc_agents, callback_manager)
    return top_agent
```

### (3) 接口实现
在原有流式代码基础上，改为从队列取数并通过 StreamingResponse 输出：

```python
......
@r.post("")
async def chat(
    data: _ChatData
):
    agent = get_cached_agent(user_id)
    ......省略部分代码......

    # 转换为 ChatMessage 列表格式
    messages = [
        ChatMessage(
            role=m.role,
            content=decode_sse_messages(m.content),
        )
        for m in data.messages
    ]

    thread = Thread(target=agent.stream_chat,
        args=(lastMessage.content, messages))
    thread.start()

    # 生成器，用于构造 StreamingResponse 对象
    def event_generator():
        # 从队列中读取对象
        queue = agent.callback_manager.handlers[0].queue

        while True:
            next_item = queue.get(True, 60.0)

            # 判断 next_item
            # 如果是 EventObject 类型的，则是 FUNCTION_CALL 事件
            if isinstance(next_item, EventObject):
                yield convert_sse(dict(next_item))

            # 如果是 StreamingAgentChatResponse 类型的，则是 AGENT_STEP 事件
            elif isinstance(next_item, StreamingAgentChatResponse):
                response = cast(StreamingAgentChatResponse,
```

```python
next_item)

#通过 response_gen 方法迭代处理流式响应
for token in response.response_gen:
    yield convert_sse(token)
    break

return StreamingResponse(event_generator(), media_type=
"text/event-stream")
```
相比基础版，这里更复杂，因为不再是顺序执行 stream_chat 再生成事件，而是要异步读取 Agent 执行过程中产生的队列事件。做法是：

1. 把 Agent 的执行放到独立线程，主线程则从共享队列读取该线程产生的 FUNCTION_CALL 相关信息或 AGENT_STEP 输出的 StreamingAgentChatResponse。
2. 设计一个生成器从队列取对象并处理：
    a. 若对象为 EventObject 类型，内容即为 FUNCTION_CALL 触发时的输入参数与调用结果，可直接 yield 并推送。
    b. 若对象为 StreamingAgentChatResponse 类型，则为 AGENT_STEP 结束后放入的流式响应，可经 response_gen 迭代取出 token，再经 StreamingResponse 持续推送。

至此，流式 API 已具备中间过程可视能力。接下来构建前端 UI 来验证效果。

## 4. 前端对话界面搭建
需要一个支持连续对话与流式渲染的 ChatBot 界面。技术栈可用 React + TypeScript，实际开发中可利用开源模板或 SDK 加速。考虑到本书侧重后端，这里选用 Vercel AI SDK，该库专为流式大模型对话 UI 设计，能快速搭建。

此前 API Server 的输入格式与 SSE 推送也都是按 Vercel AI SDK 的约定设计的。

### 1) 构建简易 ChatBot UI
借助 Vercel AI SDK 可以快速搭出基于 React 的对话界面。先用命令创建一个 Next.js 应用，该框架是 React 的上层方案：

ChatBot UI 实战与进阶 RAG 范式
```
> pnpm create next-app@latest my-ai-app
```

先装好运行所需的依赖包：

```
> npm install ai @ai-sdk/openai @ai-sdk/react zod
```

依赖就绪后，就可以用 Vercel AI SDK 来搭建并完善这个 ChatBot UI。Vercel AI SDK 是一个与框架无关的工具集，内置了不少实用组件，尤其是 useChat 这个钩子，能极大降低前端页面的开发难度，简化流式返回、API 调用的实现细节，具体用法可参考官方文档。

本次示例实现了一个轻量的 ChatBot UI，效果见图 12-20。

![](img/7eddb68cd925518fdc310e52e94ba9a9_482_0.png)

图 12-20

### 2）ChatBot UI 在线测试
在这个界面里输入问题即可发起端到端的对话验证。之前实现的增强流式 API 让 Agent 的中间执行过程能够实时在前端呈现，对调试和用户体验提升都很有帮助。比如图 12-21 就展示了一个中间状态提示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_483_0.png)

图 12-21

这说明 Agent 正在调用查询工具，也就是查询引擎去取数，参数为“tourist attractions in Nanjing”。工具调用结束后，前端会呈现如图 12-22 的画面。

![](img/7eddb68cd925518fdc310e52e94ba9a9_483_1.png)

图 12-22

此时表示查询工具已返回结果，这些信息会作为上下文被保留供后续推理使用。Agent 会继续思考下一步动作，若下一步是让大模型生成答案，就能看到流式输出的最终结果，如图 12-23。

![](img/7eddb68cd925518fdc310e52e94ba9a9_483_2.png)

图 12-23

## 5. 可进一步优化的方向
我们在此前多文档 Agentic RAG 的基础上，扩展出了支持多轮对话和流式返回的端到端应用，但离成熟产品还有距离。一个关键的改进点，是在服务端借助用户管理和 Agent 记忆能力来持久化对话历史与明细，从而简化上下文处理。

当前版本通过让客户端每次都带上历史对话来实现上下文连续，对应的接口输入类型如下：

```python
class _ChatData(BaseModel):
    messages: List[_Message]
```

这种做法的好处是服务端压力小，连续对话靠客户端就能完成。缺点也很明显：历史记录没有在服务端持久化，必须依赖浏览器本地存储等客户端方案，而且对话越长，单次传输的历史数据越多，接口性能会受影响。

实际工程里更常见的做法是：

1. 引入用户管理功能，在服务端用关系型数据库如 Postgres 或 MySQL 保存每位用户的对话历史与细节。
2. 在服务端 API 增加身份识别与安全控制，例如通过安全 token 识别客户端用户，并从数据库或缓存中取出对应历史。可选用 Redis 做缓存，也可借助 Agent 自带的记忆能力做缓存。
3. 客户端每次只需发送当前的问题，无需自行维护历史记录，交互会更简洁。

以本示例为例，由于每次请求会通过 get_agent 函数获取 Agent，我们可以为不同用户缓存对应的 Agent 实例，进而利用 Agent 自身的记忆，而不是每次都新建。这样既能提升性能，也能让服务端更好地理解对话上下文，只需少量资源。

可先用最简单的缓存方式做个验证：

```python
@lru_cache(maxsize=50)
def get_cached_agent(user_id: str) -> OpenAIAgent:
    return get_agent()
```

具体实现与测试细节请自行探索。

## 第 13 章 新型 RAG 范式：原理与落地实践
随着 RAG 应用场景的扩散，从经典 RAG 向模块化 RAG 演进已成共识。本章会介绍几种新型 RAG 范式的最新思路，并基于已学知识给出实现方式。

需要说明的是，这些范式多处于实验探索阶段，并非一成不变的成熟产品，但理解其诞生动机与原理对优化 RAG 应用很有帮助。

## 13.1 自纠错 RAG：C-RAG
Corrective-RAG，简称 C-RAG。由中国科学技术大学与 Google 研究院等团队在论文《Corrective Retrieval Augmented Generation》中提出的新范式。

### 13.1.1 C-RAG 产生的动因
C-RAG 的核心诉求可以概括为：尽量提升检索上下文的相关性。我们一直强调，RAG 中召回知识块的精准度和相关性是优化的重点，无论是索引选择、检索算法还是查询改写，目的都是从海量知识里筛出对回答当前问题有用的上下文。

即便在经典 RAG 中前期设计再充分，运行时也很难保证检索结果完全相关。C-RAG 中的“C”，正是提供了一种事后纠错与调优的机制。

总体来看，C-RAG 在检索出候选知识后，会自行评估这些知识的相关性，并据此进行自我修正。修正手段包括剔除不相关内容、改写查询后重新检索、借助搜索引擎补充外部信息等。

### 13.1.2 C-RAG 的工作机制
原理并不复杂，示意图如图 13-1 所示，已对论文思想做了少量简化以便理解。

![](img/7eddb68cd925518fdc310e52e94ba9a9_487_0.png)

图 13-1

相对于经典 RAG，C-RAG 额外加入了评估器、查询转换器和搜索模块。文档检索后，会用一个轻量级评估器，通常是小规模大模型，来判断召回知识的质量，分成相关、存疑、不相关三类，并按评估结果优化。

1. 相关知识：如果至少有一个 Node 被判定为相关，就交给大模型用于生成答案。
2. 存疑知识/不相关知识：则借助网络搜索或其他方式补充相关信息，并在搜索前先重写输入问题，以期获得更好的搜索结果。

因此，C-RAG 通过对检索结果做相关性评估、剔除无效信息并尝试外补，从而提高进入模型的上下文质量，让答案更准确。

### 13.1.3 C-RAG 的实现示例
下面实现一个简易 C-RAG 应用，感受它与传统 RAG 的差异。

#### 1. Prompt 准备
首先准备三类 Prompt，对应答案生成、相关性评估、问题重写。以下 Prompt 基于 LlamaIndex 内置模板改写，实际使用时请根据模型和效果调整。

```python
#生成答案
DEFAULT_TEXT_QA_PROMPT_TMPL = (
    "以下是上下文\n"
    "-------------------\n"
    "{context_str}\n"
    "-------------------\n"
    "请仅根据上面的上下文，回答以下问题，不要编造其他内容。\n"
    "如果上下文中不存在相关信息，请拒绝回答。\n"
    "问题：{query_str}\n"
    "答案： "
)
text_qa_prompt = PromptTemplate(DEFAULT_TEXT_QA_PROMPT_TMPL)

#评估相关性
EVALUATE_PROMPT_TEMPLATE="""您是一个评分人员，评估检索出的文档与用户问题的相关性。
以下是检索出的文档:
-------------------
{context}
-------------------
以下是用户问题:
-------------------
{query_str}
-------------------"""
```

如果文档中包含与用户问题相关的关键词或语义，且有助于解答用户问题，请将其评为相关。

请给出 yes 或 no 来表明文档是否与问题相关。

注意只需要输出 yes 或 no，不要有多余解释。

```python
evaluate_prompt = PromptTemplate(EVALUATE_PROMPT_TEMPLATE)
```

#### 问题改写
```python
REWRITE_PROMPT_TEMPLATE = """你需要生成对检索进行优化的问题。请根据输入内容，尝试推理其中的语义意图/含义。
这是初始问题:
-----------------
{query_str}
-----------------
请提出一个改进的问题: """
```

```python
rewrite_prompt = PromptTemplate(REWRITE_PROMPT_TEMPLATE)
```

#### 2. 函数：检索器构建
先快速搭一个向量存储索引实现语义检索，并返回检索器和查询引擎：

##### 构造检索器
```python
def create_retriever(file):
    docs = SimpleDirectoryReader(input_files=[file]).load_data()
    index = VectorStoreIndex.from_documents(docs)
    return index.as_retriever(similarity_top_k=3), index.as_query_engine()
```

#### 3. 函数：相关性评估器
该函数会借助大模型评估语义检索出的 Node 与输入问题的相关性，并返回相关 Node 列表：

##### 评估检索结果
```python
def evaluate_nodes(query_str:str, retrieved_nodes: List[Document]):
    # 构造一个用于评估的简单查询管道，直接使用大模型也一样
    evaluate_pipeline = QueryPipeline(chain=[evaluate_prompt,
        llm_openai])

    filtered_nodes = []
    need_search = False
    for node in retrieved_nodes:
        #对 Node 中的内容与输入问题评估相关性
        relevancy = evaluate_pipeline.run(
            context=node.text, query_str=query_str
        )

        #如果相关，则返回；否则，需要搜索
        if(relevancy.message.content.lower()=='yes'):
            filtered_nodes.append(node)
        else:
            need_search = True

    return filtered_nodes,need_search
```

#### 4. 函数：问题重写
若需通过搜索补充额外上下文，先对输入问题做一次重写。这里使用简单的 Prompt 加大模型实现，更复杂需求可接入查询转换组件：

```python
#重写输入问题
def rewrite(query_str: str):
    new_query_str = llm_openai.predict(
        rewrite_prompt, query_str = query_str
    )
    return new_query_str
```

#### 5. 函数：搜索工具
可借助 LlamaIndex 框架内置的 Agent 工具快速接入网络搜索。这里选用 Tavily 搜索工具，需先在官网申请 API Key：

```python
#搜索
def web_search(query_str:str):
    tavily_tool = TavilyToolSpec(api_key="tvly-***")
    search_results = tavily_tool.search(query_str,max_results=5)
    return "\n".join([result.text for result in search_results])
```

#### 6. 函数：答案生成
由于需要自行组装送入大模型的上下文，融合语义检索与网络搜索结果，无法直接使用查询引擎，改用大模型直接生成：

```python
#使用大模型直接生成答案
def query(query_str,context_str):
    response = llm_openai.predict(
        text_qa_prompt, context_str=context_str,
        query_str=query_str
    )
    return response
```

#### 7. 主流程
准备工作完成后，可编写简单的主程序进行测试：

```python
......
file_name = "../../data/citys/南京市.txt"

#构造检索器与查询引擎
retriever,query_engine = create_retriever(file_name)
query_str = '南京市的人口数量是多少与分布情况如何？参加2024年中考的学生数量是多少？'

#先测试直接生成答案
response = query_engine.query(query_str)
print(f'----------------Response from query engine----------------')
pprint_response(response,show_source=True)

#测试C-RAG流程
```

```python
#C-RAG: 检索
print(f'-----------------Response from CRAG-----------------')
retrieved_nodes = retriever.retrieve(query_str)
print(f'{len(retrieved_nodes)} nodes retrieved.\n')

#C-RAG: 评估检索结果，仅保留相关的上下文
filtered_nodes,need_search = evaluate_nodes(query_str,retrieved_nodes)
print(f'{len(filtered_nodes)} nodes relevant.\n')
filtered_texts = [node.text for node in filtered_nodes]
filtered_text = "\n".join(filtered_texts)

#C-RAG: 如果存在不相关知识，那么重写输入问题并借助网络搜索
if need_search:
    new_query_str = rewrite(query_str)
    search_text = web_search(new_query_str)

#组合成新的上下文，并进行生成
context_str = filtered_text + "\n" + search_text
response = query(query_str,context_str)
print(f'Final Response from crag: \n{response}')
```

测试问题中特意加入了一个知识库中找不到的实时信息问题：“参加 2024 年中考的学生数量是多少？”，用于验证相关性判断与网络搜索效果。直接查询的结果如图 13-2 所示。可以看出，部分信息因缺乏参考知识而无法给出完整回答。

![](img/7eddb68cd925518fdc310e52e94ba9a9_492_0.png)

图 13-3 展示了 C-RAG 下的输出。首先对检索出的 3 个 Node 进行过滤，去掉了 2 个不相关节点；随后通过网络搜索补充实时信息并用于生成。最终输出显示，在加入搜索结果后，大模型能够给出更完整的回答。

南京常住人口约为949.11万人，城镇人口占87.01%，人口密度较高。2024年中考预计约有6.6万人参加。

![](img/7eddb68cd925518fdc310e52e94ba9a9_493_0.png)

C-RAG 是一种相对简单的 RAG 扩展方案，也存在局限。比如基于大模型的相关性评估会受模型不确定性影响，可能出现误判。另外，网络搜索在部分企业级严格场景中可能带来合规风险。

## 13.2 自省式 RAG：Self-RAG

Self-RAG：为检索增强生成装上自我反思的开关

Self-RAG，即自省式 RAG，源自华盛顿大学与 IBM 研究院团队在论文《Self-RAG: Learning to Retrieve, Generate, and Critique through Self-Reflection》中的探索。开源原型在多组评测任务上均显著优于传统 RAG，证明了在事实性和可信度上的提升。

### 13.2.1 Self-RAG 诞生的动机

RAG 通过引入外部知识库，帮助语言模型在知识密集型任务中降低幻觉。即便忽略上下文长度与响应时延等工程限制，传统做法仍存在两类顽疾，正是 Self-RAG 想要修正的对象。

1. 检索泛滥。经典 RAG 对任何输入都执行 top_K 检索，容易把大量无关或干扰性信息带入上下文，反而拖累最终结果。
2. 生成与来源脱节。检索到材料后，并不能保证模型严格遵循来源，相关性也无法完全确认，最终易出现偏离。

用通俗的类比理解，RAG 相当于允许一名优秀考生在考试时翻阅参考书。

- 无论题目是否熟悉，考生都会去翻书找答案，效率并不理想。更合理的做法是对熟悉内容直接作答，仅在不确定时才查阅。
- 即便查阅了大量资料，考生也可能看错、记偏或主观发挥，最终答案依然出错。

实际工程中常采用的补丁是检索前让模型判断是否需要检索、在提示中强制要求忠实来源、多次迭代评估答案。这些方法虽有改善，却会增加流程复杂度、拖慢响应并引入更多不确定性。Self-RAG 的思路是将判断与自检内建进模型本身。

### 13.2.2 Self-RAG 的原理

Self-RAG 与传统 RAG 的本质区别在于：通过模型层微调赋予语言模型“是否检索、何时检索”的判断能力，再配合应用层流程，提升生成准确性与可信度。

#### 1. 基本流程

Self-RAG 的整体运行示意如图 13-4。

![](bbox=[318, 108, 681, 520])

步骤拆解如下。

1. 检索决策。传统 RAG 直接检索，Self-RAG 先由模型判断是直接作答还是先检索。
2. 按需检索。
   - 不需检索的场景，例如创作一首颂扬母爱的诗，模型直接输出。
   - 需要检索的场景，例如介绍公司最畅销产品，则调用检索组件取前 K 个最相关的知识片段。
3. 增强生成。将每个检索片段与原问题组装成提示，分别生成 K 个候选答案。
4. 评估甄选。对 K 个候选进行打分，选出最优者作为最终输出。

#### 2. 评估指标

流程中存在两处需要模型自评：何时检索、如何选优。为此设计了 4 类评估标记，原文定义见表 13-1。

表 13-1

| 评估指标 | 输入 | 输出 | 定义 |

| --- | --- | --- |

| Retrieve | x/x,y | {yes, no, continue} | Decides when to retrieve with R |

| IsREL | x,d | {relevant, irrelevant} | d provides useful information to solve x. |

| IsSUP | x,d,y | {fully supported, partially supported, no support} | All of the verification-worthy statement in y is supported by d. |

| IsUSE | x,y | {5, 4, 3, 2, 1} | y is a useful response to x. |

通俗解释如下。

1. Retrieve：是否需要检索
   - [No Retrieval]：无需检索，直接生成
   - [Retrieval]：需要检索
   - [Continue to Use Evidence]：无需新检索，沿用已有证据

2. IsREL：知识相关性
   - [Relevant]：检索片段对问题有帮助
   - [Irrelevant]：检索片段与问题无关

3. IsSUP：答案支撑度
   - [Fully supported]：答案完全被检索材料支撑
   - [Partially supported]：仅部分被支撑
   - [No support / Contradictory]：无支撑或矛盾，例如材料只说“中国首都北京”，答案却加上“北京最受欢迎景点是长城”，后半句即属部分支撑。

4. IsUSE：答案有效性
   - 按有用程度给出 [Utility:1] 至 [Utility:5] 的评分。

#### 3. 指标生成

直接用提示让模型判断相关性虽然灵活，但交互次数多、成本高且难以量化。Self-RAG 采用微调注入自省标记的做法，让模型在推理时自行输出标记。

模型在生成过程中会自行决定是否需要查证，并输出 [Retrieval] 等标记暂停；在拿到上下文后，会在答案中插入 [Relevant][Fully supported] 等标记进行自我校对。

1. 含相关性等标记的输出示例。

![](img/7eddb68cd925518fdc310e52e94ba9a9_497_0.png)

Response: [Relevant] 字节推出的 Coze 是一个大模型应用开发平台，提供一站式工具、插件与编码环境。[Partially supported]

[Utility:5]

2. 触发检索的示例。

Response: 当然![Retrieval]<paragraph>

通过微调，模型学会了在合适位置输出这些标记以配合工作流。开源项目提供了训练数据、微调脚本及可用的模型 selfrag_llama2_7b，可通过 Hugging Face Hub 使用，后续实测均基于它。

#### 4. 输出评估

有了自省标记后，如何量化比较并选出最优答案？关键在于模型输出层的 logprobs，即对数概率。

![](img/7eddb68cd925518fdc310e52e94ba9a9_498_0.png)

##### 1）了解对数概率

模型生成本质是逐步预测下一个词元并循环，直到结束符。每次预测会给出多个候选词元及其概率列表，再择一输出。logprobs 字段记录了每一步各候选词元的对数概率。

![](img/7eddb68cd925518fdc310e52e94ba9a9_499_0.png)

##### 2）评估算法

自省标记同样对应概率。例如出现 [Fully supported]，说明模型在该位置同时计算了 [Fully supported]、[Partially supported] 等的概率并做了选择。评分基于这些概率计算：

1. 【IsREL】知识相关性。计算 [Relevant] 的输出概率占该指标两类标记概率之和的比例。
2. 【IsSUP】答案支撑度。计算 [Fully supported] 的概率占比，加上 [Partially supported] 的概率占比乘以 0.5。
3. 【IsUSE】答案有效性。对五类效用标记分别计算其概率占比乘以对应权重后求和。

以 IsSUP 为例，官方测试代码的逻辑可简化为：定位自省标记，读取其预测概率，经 exp 转换后按公式聚合。

```python
......
#IsSUP 的 3 种自省 token
_IS_SUPPORTED_TOKENS = [
    "[Fully supported]",
    "[Partially supported]",
    "[No support / Contradictory]",
]

#计算 IsSUP 得分
def _is_supported_score(
    pred_tokens: List[int], pred_log_probs_dict: List[Dict[str, float]]
) -> float:
    #最终的得分
    is_supported_score = 0

    #首先找到输出的自省 token 的位置, 然后退出, 这个类型的指标的 token 只有一个
    token_appear_id = -1
    for tok_idx, token in enumerate(pred_tokens):
        if token in _IS_SUPPORTED_TOKENS:
            token_appear_id = tok_idx
            break

    #如果找到了自省 token 的位置, 比如为 [Fully supported]
    if token_appear_id > -1:
        #在这个位置上查找所有该类型的指标的 3 种自省 token 的输出概率
        #保存到 issup_score_dict 这个字典中
        issup_score_dict = {}
        for token in _IS_SUPPORTED_TOKENS:
            prob = pred_log_probs_dict[token_appear_id][token]
            issup_score_dict[token] = np.exp(float(prob))

        #用上面的计算公式计算最终得分
        is_supported_score = (
            issup_score_dict["[Fully supported]"]
            + 0.5 * issup_score_dict["[Partially supported]"]
        ) / np.sum(list(issup_score_dict.values()))

    return is_supported_score
```

使用时注意两点：logprobs 为对数形式，需用 exp 还原为概率；推理工具如 Llama_cpp 会在输出中提供 pred_tokens 与 pred_log_probs_dict 作为算法输入。

### 13.2.3 Self-RAG 的实现

理解原理后，用开源微调模型 selfrag_llama2_7b 搭建原型，能更直观感受自省式输出。项目提供了微调方法，但未给出上层应用框架，需自行实现。

### 1. 模型测试

在本地使用 llama_cpp 推理该模型，Ollama 暂不支持此版本。

1. 安装推理库与模型下载依赖。

```bash
> pip install llama_cpp_python
> pip install huggingface-hub
```

2. 获取 gguf 版模型。

```bash
> huggingface-cli \
download m4r1/selffrag_llama2_7b-GGUF \
selffrag_llama2_7b.q4_k_m.gguf \
--local-dir ./model \
--local-dir-use-symlinks False
```

3. 用不同问题运行，观察输出差异。

```python
......
from llama_cpp import Llama

_MODEL_KWARGS = {"logits_all": True, "n_ctx": 2048,
"n_gpu_layers":200}
_GENERATE_KWARGS = {"temperature": 0.0,"top_p": 1.0,"max_tokens":
1024,"logprobs": 1000}

#大模型
llm=Llama(model_path="./model/selffrag_llama2_7b.q4_k_m.gguf",**_MODEL_KWARGS)

#格式化 Prompt，注意按照此格式输入问题和关联知识
def format_prompt(input, paragraph=None):
    prompt = "### Instruction:\n{0}\n\n### Response:\n".format(input)
    if paragraph is not None:
        prompt +=
"[Retrieval]<paragraph>{0}</paragraph>".format(paragraph)
    return prompt

#测试两个问题，一个无须检索知识，另一个需要检索知识
query_1 = "写一首歌颂母爱的小诗"
query_2 = "能否介绍一下字节跳动的AI平台Coze?"
```

## 2. 应用测试

在 selfrag_llama2_7b 这个微调模型验证通过后，就能快速搭一个上层 Demo，用来呈现 Self-RAG 式检索增强的完整运行轨迹。为了突出 Self-RAG 的精髓——多次生成后靠量化打分挑出最优答案，本文把其他环节都做了简化。落地时要依据真实场景做针对性加固和优化。

### 1）打造定制化查询引擎

这次 RAG 需要对检索和生成环节做更细的干预，所以用一个自定义查询引擎来承载 Self-RAG 的复杂查询逻辑：

```python
from dataclasses import dataclass
from typing import Any, Dict, List, Tuple
import numpy as np

from llama_index.core.query_engine import CustomQueryEngine
from llama_index.llms.llama_cpp import LlamaCPP
from llama_index.core.base.base_retriever import BaseRetriever
from llama_index.core.response import Response
from llama_index.core.bridge.pydantic import Field
from llama_index.core.utils import print_text

#定义所有的自省 token
_TOKENS = {
    "retrieval": ["[No Retrieval]", "[Retrieval]", "[Continue to Use Evidence]"],
    "relevance": ["[Irrelevant]", "[Relevant]"],
    "support": ["[Fully supported]", "[Partially supported]", "[No support / Contradictory]"],
    "utility": ["[Utility:1]", "[Utility:2]", "[Utility:3]", "[Utility:4]", "[Utility:5]"],
    "ctrl": [
        "[No Retrieval]", "[Retrieval]", "[Continue to Use Evidence]",
        "[Irrelevant]", "[Relevant]",
        "[Fully supported]", "[Partially supported]", "[No support / Contradictory]",
        "<paragraph>", "</paragraph>",
        "[Utility:1]", "[Utility:2]", "[Utility:3]", "[Utility:4]", "[Utility:5]"
    ]
}
```

基于 CustomQueryEngine 造一个新引擎：

```python
class SelfRAGQueryEngine(CustomQueryEngine):
```

先做初始化，处理上做简化。一个可用的查询引擎最少要有检索器和生成器两块。因为推理端用的是 llama.cpp，所以直接用 LlamaCPP 类型的 llm 实例作为生成侧来完成引擎的初始化：

```python
......
def __init__(
    self,
    llm: LlamaCPP,
    retriever: BaseRetriever,
) -> None:
    """初始化查询引擎"""
    super().__init__()
    self.llm = llm
    self.retriever = retriever
```

随后要补齐 CustomQueryEngine 里定义的抽象方法 custom_query：

```python
......
def query(self, query_str: str) -> str:
    """
    自定义查询函数。
    参数:
    query_str (str): 查询字符串。
    返回:
    Response: 查询的响应结果。
    """
    #调用大模型获得响应结果
    response = self.llm.complete(_format_prompt(query_str))
    answer = response.text

    if "[Retrieval]" in answer:
```

```python
print_text("需要检索知识，开始检索...\n", color="blue")
documents = self.retriever.retrieve(query_str)
print_text(f"共检索到 {len(documents)} 个相关知识\n", color="blue")

paragraphs = [
    _format_prompt(query_str, document.node.text) for document in documents
]

# 使用检索内容重新生成结果并评估
print_text("=====开始：重新生成并评估====\n", color="blue")
llm_response_per_paragraph, paragraphs_final_score = \
    self._regen_then_eval(paragraphs)
print_text("=====结束：重新生成并评估====\n", color="blue")

best_paragraph_id = max(
    paragraphs_final_score,
    key=paragraphs_final_score.get
)
answer = \
    llm_response_per_paragraph[best_paragraph_id]
print_text(f"已选择最佳答案：{answer}\n", color="blue")

else:
    print_text("无须检索知识，直接输出答案\n", color="green")

answer = _postprocess_answer(answer)
print_text(f"最终答案：{answer}\n", color="green")
return str(answer)
```

1. 调用 llm.complete 并通过 _format_prompt 做 Prompt 格式化，拿到大模型首次回复。
2. 若回复里没有出现 [Retrieval] 标记，就直接返回答案；若有，则继续。
3. 用检索器对原问题做检索，取前 K 条最相关知识块，与原文问题拼成 Prompt，汇总到 paragraphs 列表。
4. 触发 _regen_then_eval 重新生成并打分，该方法会输出： llm_response_per_paragraph 为每段知识对应的生成结果，paragraphs_final_score 为对应结果的得分。
5. 在 _regen_then_eval 的结果里挑出总分最高的生成作为最终输出。
6. 生成文本里残留自省 token，用 _postprocess_answer 做后处理清洗掉标记：

```python
def _postprocess_answer(answer: str) -> str:
    for token in _TOKENS["ctrl"]:
        answer = answer.replace(token, "")
    if "</s>" in answer:
        answer = answer.replace("</s>", "")
    if "\n" in answer:
        answer = answer.replace("\n", "")
    if "<|endoftext|>" in answer:
        answer = answer.replace("<|endoftext|>", "")
    return answer
```

_format_prompt 在 13.2.2 节已给出，下面重点实现 _regen_then_eval：

```python
......
def _regen_then_eval(self, paragraphs: List[str])
->Tuple[Dict[int,str],Dict[int,float]]:
    """
    运行评估模块，调用大模型对给定的段落进行评估。
    参数:
    paragraphs (List[str]): 包含要评估的段落的列表。
    返回:
    Tuple[Dict[int,str],Dict[int,float]]: 包含生成的结果索引和
    评估字典。
    """
    paragraphs_final_score = {}
    llm_response_text = {}

    for p_idx, paragraph in enumerate(paragraphs):
        #生成结果
        response = self.llm.complete(paragraph)
        pred = response.raw
        llm_response_text[p_idx] = response.text

        #从 raw 字段中取得 token 输出概率相关的信息
```

```python
#top_logprobs 字段保存每个位置上每个 token 的输出概率
logprobs = pred["choices"][0]["logprobs"]
pred_log_probs = logprobs["top_logprobs"]

# 计算 IsREL 得分，相关性为第一个 token，直接传入 0
isrel_score = _relevance_score(pred_log_probs[0])

# 计算 IsSUP 得分
issup_score = _is_supported_score(logprobs["tokens"],
pred_log_probs)

# 计算 IsUSE 得分
isuse_score = _is_useful_score(logprobs["tokens"],
pred_log_probs)

#最终得分
paragraphs_final_score[p_idx] = (
    isrel_score + issup_score + 0.5 * isuse_score
)

print_text(
    f"输入：{paragraph}\n响应：
{llm_response_text[p_idx]}\n评估：
{paragraphs_final_score[p_idx]}\n",
    color="blue",
)
print_text(
    f"已完成 {p_idx + 1}/{len(paragraphs)} 段落\n\n",
color="blue"
)

return llm_response_text, paragraphs_final_score
```

简要说明如下，细节可结合代码注释理解。

1. 针对传入的若干段落，即原问题加检索到的知识，逐个生成并评估。
2. 通过响应对象的 raw 字段读取 token 概率，这里需要打开 logits_all 选项。
3. 借助 logprobs 里的概率值计算三项指标，具体评估逻辑参考原理章节。
4. 合并为总分并写入 paragraphs_final_score 返回。

13.2.2 节已介绍过 _is_supported_score 的实现，这里补充 _relevance_score 与 _is_useful_score 的实现，注意对照评估算法说明：

```python
......
def _relevance_score(pred_log_probs: Dict[str, float]) -> float:
    rel_prob = np.exp(float(pred_log_probs["[Relevant]"]))
    irel_prob = np.exp(float(pred_log_probs["[Irrelevant]"]))
    return rel_prob / (rel_prob + irel_prob)

def _is_useful_score(
    pred_tokens: List[int], pred_log_probs_dict: List[Dict[str, float]]
) -> float:
    isuse_score = 0
    utility_token_appear_id = -1
    #先找到位置
    for tok_idx, tok in enumerate(pred_tokens):
        if tok in _TOKENS["utility"]:
            utility_token_appear_id = tok_idx

    #在这个位置上获取不同 token 的输出概率
    if utility_token_appear_id > -1:
        ut_score_dict = {}
        for token in _TOKENS["utility"]:
            prob = pred_log_probs_dict[utility_token_appear_id][token]
            ut_score_dict[token] = np.exp(float(prob))

        #IsUSE 的得分需要加权计算
        ut_sum = np.sum(list(ut_score_dict.values()))
        ut_weights = [-1, -0.5, 0, 0.5, 1]
        isuse_score = np.sum(
            [
                ut_weights[i] * (ut_score_dict[f"[Utility:{i + 1}]"])
                / ut_sum
                for i in range(len(ut_weights))
            ]
        )
    return isuse_score
```

至此定制查询引擎已搭建完成，整个流程回顾如下。(1) 组合大模型与检索器初始化查询引擎。(2) 先让大模型生成，若出现检索标记则调用检索器补全知识。(3) 对每条检索结果分别重生成并量化评估。(4) 择优输出得分最高的答案。

## 2) 原型主程序演示

下面用一个极简的原型程序来跑通全流程。文档加载与切分被简化，直接用 Document 对象手工构造几份简单文档作为嵌入知识：

```python
import os
from llama_index.llms.llama_cpp import LlamaCPP
from llama_index.core import Document, VectorStoreIndex
from llama_index.core.retrievers import VectorIndexRetriever
from pathlib import Path

#导入已经构造的自定义 Self-RAG 查询引擎
from selfrag_queryengine import SelfRAGQueryEngine

#注意打开 logits_all 参数选项
_MODEL_KWARGS = {"logits_all": True, "n_ctx": 2048, "n_gpu_layers": -1}
_GENERATE_KWARGS = {
    "temperature": 0.0,
    "top_p": 1.0,
    "max_tokens": 1000,
    "logprobs": 32016,
}

# 之前下载并保存 selfrag_llama2_7b 模型的目录。
download_dir = "../../model"

# 构造简单的测试文档，此处直接构造 Document 对象，方便观察检索结果
documents = [
    Document(
        text="Xiaomi 14 is the latest smartphone released by Xiaomi. It adopts a new design concept, the body is lighter and thinner, equipped with the latest processor, and the performance is more powerful."),
    Document(
        text="Xiaomi 14 phone uses a 6.7-inch ultra-clear large screen,
with a resolution of up to 2400x1080, whether watching videos or playing
games, it can bring the ultimate visual experience."),
    Document(
        text="Xiaomi 14 phone is equipped with the latest Snapdragon
888 processor, equipped with 8GB of running memory and 128GB of storage
space, whether it is running large games or multitasking, it can easily
cope."),
    Document(
        text="Xiaomi 14 phone is equipped with a 5000mAh large-capacity
battery, supports fast charging, even if you are traveling or using
it for a long time, you don't have to worry about power issues."),
    Document(
        text="Xiaomi 14 phone has a rear camera of 64 million pixels
and a front camera of 20 million pixels. Whether it is taking pictures
or recording videos, it can capture every wonderful moment in life."),
    Document(
        text="Xiaomi 14 phone runs the latest MIUI 12 operating system.
This operating system has a beautiful interface, smooth operation,
and provides a wealth of functions and applications."),
    Document(
        text="Xiaomi 14 phone supports 5G network, fast download speed,
low latency, whether watching high-definition videos or playing online
games, you can enjoy the ultimate network experience."),
    Document(
        text="Xiaomi 14 phone supports facial recognition and
fingerprint unlocking, protects user privacy, and provides a more
convenient unlocking method."),
    Document(
        text="Xiaomi 14 phone supports wireless charging and reverse
charging functions. Wireless charging can free you from the shackles
of data cables, and reverse charging can charge your other devices."),
    Document(
        text="Xiaomi 14 phone is equipped with a 90Hz high refresh rate
screen, whether scrolling pages or playing games, it can bring a smooth
visual experience."),
]

# 嵌入与索引
index = VectorStoreIndex.from_documents(documents)

# 构造一个检索器
retriever = VectorIndexRetriever(index=index, similarity_top_k=5)

# 构造一个大模型：使用 Llama_cpp 作为推理工具
model_path = Path(download_dir) / "selfrag_llama2_7b.q4_k_m.gguf"
llm = LlamaCPP(model_path=str(model_path),
model_kwargs=_MODEL_KWARGS, generate_kwargs=_GENERATE_KWARGS)

# 构造自定义的查询引擎
query_engine = SelfRAGQueryEngine(llm, retriever)

# 查询一：无须检索的创作问题
print("\nQuery 1: write a poem about beautiful sunset")
response = query_engine.query("write a poem about beautiful sunset")

# 查询二：需要检索的事实性问题
print("\nQuery 2: Tell me some truth about xiaomi 14 phone, especially
about its battery and camera?")
response = query_engine.query("Tell me some truth about xiaomi 14 phone,
especially about its battery and camera?")
```
最后我们挑了两道对比题目来验证：一道是不需要查资料就能完成的开放式创作，一道是必须依赖文档才能给出正确结果的事实类提问，通过观察两种情形下的内部决策差异来检验效果。

## 3）实机验证
下面直接跑通流程并看输出，可以明显看出两种提问的处理路径不同。

因为第一问属于创作类任务，模型自行判断不需要检索，直接生成结果，如图 13-7 所示。

Query 1: write a poem about beautiful sunset

无须检索知识，直接输出答案

最终答案: The sky is on fire, A canvas painted with hues of orange and red. The sun sets in the night takes over. But in my heart, I'll hold this moment dear, As I watch the sunset's beauty a

![](img/7eddb68cd925518fdc310e52e94ba9a9_514_0.png)
第二问则属于事实依赖型问题，模型判定需要先检索。在拿到检索结果后，模型会进入循环重生成并打分评估，这里只展示第一次生成的片段。例如第一段生成知识的得分为 1.7676576042966103，如图 13-8 所示。把所有被检索到的知识都轮流重生成并评估后，最终胜出的是得分最高的那个答案，如图 13-9 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_514_1.png)

![](img/7eddb68cd925518fdc310e52e94ba9a9_514_2.png)
整体来看，这次演示的输出与预期基本一致。

## 13.2.4 Self-RAG 的优化方向
Self-RAG 通过模型层的微调，让大模型具备了自主决定是否检索以及自行评估结果的能力，在很大程度上简化了应用层的设计，同时不削弱模型本身的生成能力，思路相当新颖。在前面的原型里，Self-RAG 的多次生成仍然是基于一次检索得到的前 K 个最相关块来进行的，实际使用中暴露出两个明显短板。

(1) 检索块本身已经按语义相似度排过序，于是生成结果的得分排序往往与相似度排序高度重合，自评的价值被削弱。

(2) 真实场景的知识结构更复杂，常常需要一次性把多份文档喂给模型，才能保证答案的完整性和参考广度，而不是逐个文档输入。

因此，更好的做法是让检索策略具备弹性：进行多次融合式的检索，并用不同轮次的检索结果分别生成和评估，而非用单次检索的多文档一起生成评估。这样既能扩充模型的上下文视野，也能通过 Self-RAG 的自省机制在多轮生成中挑出质量最高的输出。实现多次检索时可以灵活组合策略，比如：

- （1）对查询进行重写后再检索，并尝试不同的重写方法。
- （2）采用不同的检索算法来获取差异化的相关知识。
- （3）检索后使用不同的 Rerank 方法重新排序，形成不同的知识侧重。

更多可行的优化思路还需要在真实业务中持续探索和完善。

## 13.3 检索树 RAG：RAPTOR
本节介绍另一种 RAG 思路——RAPTOR，即基于树状结构的递归抽象检索。它源自斯坦福团队的论文《Raptor: Recursive Abstractive Processing for Tree-Organized Retrieval》。

### 13.3.1 RAPTOR 为什么出现
RAG 通常服务于知识密集型场景，借助向量索引召回上下文来回答问题。但 RAG 的挑战不止于简单事实问答。比如问《西游记》里孙悟空如何从顽皮猴子成长为斗战胜佛，这个问题在书中可以找到答案，但需要对整本书做阅读、理解和归纳，而非召回几段原文就能解决。

传统做法只取向量最相关的前 K 块，天然偏向局部细节，难以把握整体和宏观语义，除非把全部知识塞进超长上下文。RAPTOR 正是要解决这个问题：构建一个从上到下、从概览到细节、从宏观到微观的多层树状知识库，让模型既能回答细节事实，也能回答依赖高层理解的问题。

### 13.3.2 RAPTOR 工作原理
如何搭建这样一棵树？RAPTOR 的构建流程如图 13-10 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_516_0.png)
图 13-10

核心思路分为三步。

(1) 从基础节点 Leaf Node 开始，即把原始文档切分为多个知识块，类似 LlamaIndex 中的 Node，这里示例有 6 个，先为它们生成嵌入向量。

(2) 用聚类算法把这些基础节点分组，比如分成 3 组。把“相关”的文档聚到一起后，为每个分组生成摘要 Summary，并据此构造语义更抽象、信息更丰富的上级节点，共产生 3 个新节点。

(3) 对这 3 个新节点递归执行嵌入→聚类→生成摘要节点的操作，直到无法再聚类为止。

如此便得到一棵完整的节点树。图 13-10 中树包含 1 至 10 共 10 个节点，实际应用中会更多。Node1~Node6 为底层基础节点，Node7~Node9 为中间层，Node10 为根节点，根不一定唯一。高层节点可视为对若干低层节点的提炼和总结。所有节点的嵌入向量都会写入向量库供检索使用。

RAPTOR 在使用时主要有两种检索方式。

一种是树遍历检索：从根节点出发，结合向量相似度和父子关系逐层向下，最后汇聚所有相关节点作为上下文，如图 13-11 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_517_0.png)
图 13-11

另一种是全量展开检索：把整棵树展平成单层，直接对全部节点做向量相似度检索，速度更快且不易遗漏，如图 13-12 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_518_0.png)
从原理上看，RAPTOR 的价值在于：

- （1）在多层级构建语义表示并嵌入，提升了召回广度。
- （2）能兼顾不同层级的问题，细节点由低层节点回答，综合问题由高层节点回答。
- （3）适合需要融合多个知识块才能作答的综合性输入。

### 13.3.3 RAPTOR 实现要点
RAPTOR 的落地关键阶段如下。

#### 1. 构造索引
RAPTOR 使用常规的向量存储索引，难点不在索引算法，而在如何生成树状结构所需的节点。除最底层的基础节点外，需要通过嵌入→聚类→生成摘要节点的迭代循环不断生成父节点，最终形成完整树，并在树上所有节点上建立向量索引。

其核心逻辑是把加载后的原始文档逐步抽象成多级父节点并写入向量库。

```python
......RaptorRetriever 的部分实现......
#基于文档列表构造多级树状的索引结构
async def insert(self, documents: List[BaseNode]) -> None:

    #嵌入模型/转换器
    embed_model = self.index._embed_model
    transformations = self.index._transformations

    #对传入的文档做 Node 分割，这是底层叶子 Node 的基础
    cur_nodes = run_transformations(documents, transformations,
    in_place=False)

    #根据设置的树层次循环构造。在每一次循环后都将本轮生成的父 Node 作为当前
    Node
    #继续循环处理
    for level in range(self.tree_depth):

        #给当前 Node 生成向量并暂存到 id_to_embedding 变量中
        embeddings = await embed_model.aget_text_embedding_batch(
            [node.get_content(metadata_mode="embed") for node in
            cur_nodes]
        )

        id_to_embedding = {
            node.id: embedding
            for node, embedding in zip(cur_nodes, embeddings)
        }

        # 聚类，将语义相近的 Node 聚类到一个聚簇中
        nodes_per_cluster = get_clusters(cur_nodes,
        id_to_embedding)

        #给每个聚簇都生成摘要
        summaries_per_cluster = await \
        self.summary_module.generate_summaries(nodes_per_cluster)

        # 把生成的摘要构造成新的 Node,即当前 Node 的父 Node
```

new_nodes = [

    TextNode(

        text=summary,

        metadata={"level": level},

        excluded_embed_metadata_keys=["level"],

        excluded_llm_metadata_keys=["level"],

    )

    for summary in summaries_per_cluster

]

# 处理当前 Node，设置其 parent_id 为生成的父 Node 的 id

# 根据生成的向量信息设置 embedding 字段

# 然后，把当前 Node 插入索引中，这样就完成了本层的索引构造

# 同时生成了这一层的父 Node

nodes_with_embeddings = []

for cluster, summary_doc in zip(nodes_per_cluster, new_nodes):

    for node in cluster:

        node.metadata["parent_id"] = summary_doc.id

        node.excluded_embed_metadata_keys.append("parent_id")

        node.excluded_llm_metadata_keys.append("parent_id")

        node.embedding = id_to_embedding[node.id_]

        nodes_with_embeddings.append(node)

self.index.insert_nodes(nodes_with_embeddings)

# 以父 Node 作为新的当前 Node，进入下一次循环

# 注意：此时父 Node 还没有插入索引中

cur_nodes = new_nodes

# 在达到迭代次数后，把最后一次的父 Node 插入索引中

self.index.insert_nodes(cur_nodes)

上述流程先批量生成本层的汇总节点，再为每个原始节点挂上父级引用、补齐向量后写入索引，完成一层层的树形构建，最后把迭代结束时的顶层父节点也一并入库。

## 2. 实现聚类

在索引构建阶段，真正要落地的两个函数是 generate_summaries 和 get_clusters，前者负责产出摘要节点，后者负责按向量做分组。向量本身由嵌入模型给出，摘要的生成可以借助 LlamaIndex 提供的 tree_summarize 响应生成器快速实现：

```python
# ......生成每个聚簇的摘要 Node，可增加并行处理......
async def generate_summaries(
    self, documents_per_cluster: List[List[BaseNode]]
) -> List[str]:

    # 构造一个 tree_summarize 类型的响应生成器
    responses = []
    response_synthesizer = get_response_synthesizer(
        response_mode="tree_summarize", use_async=True, llm=llm
    )

    # 对输入的多个聚簇循环: 给每个聚簇中的 Node 都生成摘要
    jobs = []
    for documents in documents_per_cluster:
        with_scores = [NodeWithScore(node=doc, score=1.0)
                       for doc in documents]
        response = response_synthesizer.asynthesize(
            self.summary_prompt, with_scores)
        responses.append(response)

    return [str(response) for response in responses]
```

真正耗时和难度较高的部分在于聚类，通常会依赖成熟的 Python 工具链来完成，例如 scikit-learn 这类机器学习库，示意见图 13-13 所示。

![](img/7eddb68cd925518fdc310e52e94ba9a9_521_0.png)

图 13-13

## 3. 实现检索

RAPTOR 在查询时给了两种思路，一种是沿树结构向下遍历，一种是直接做全库检索。实践中更推荐后者，因为 RAPTOR 使用的底层仍然是普通向量存储，检索逻辑和常规向量检索并无差异，所以实现起来非常直接：

```python
# 实现简单的全量检索
async def collapsed_retrieval(self, query_str: str) -> Response:
    # 直接对索引构造检索器后检索即可
    return await self.index.as_retriever(
        similarity_top_k=3
    ).aretrieve(query_str)
```

本篇详细拆解了 C-RAG、Self-RAG、RAPTOR 这类新型 RAG 方案的产生背景、工作原理和落地方式。每种范式各有擅长的场景，并非适用于所有问题的通用解法。研究它们的价值在于吸收设计思路，在实际项目中结合业务灵活选用。相关技术仍在演进，使用时务必做充分的测试与评估，切忌直接照搬。LlamaHub 上的 LlamaIndex Packs 中收录了不少社区实现的参考代码，值得下载研读。

## 【高级篇小结】

RAG 的核心思想容易让人误以为落地很简单，事实上从一个能跑的原型到可稳定服务、可持续迭代的生产级系统差距很大。真实项目里必须深入理解内部机制，结合各类优化手段、工具和评估方法反复打磨，才能达到生产就绪。

本篇聚焦 RAG 能力进阶的话题，涵盖了高级 RAG 下的新模块与算法、Agentic RAG 的构建思路、RAG 评估体系、常见优化策略，以及企业级端到端 RAG 的架构实践，最后对新型 RAG 范式的设计思想做了梳理。

希望这些内容能帮助你把 RAG 应用从可用做到好用，从原型推向生产，实现真正的跃迁。