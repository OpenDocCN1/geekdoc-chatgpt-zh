[PRE0]
在输出答案的环节里，代码是可以灵活调整的，利用 ChatDeepSeek 就能直接调起 DeepSeek 模型来完成生成。

[PRE1]
最终结论是本作的世界观大量借用了本土文化与真实地标。比如重庆大足石刻、山西的小西天、南禅寺、铁佛寺、广胜寺以及鹳雀楼；贵州的承恩寺；云南的崇圣寺；天津的独乐寺；四川的安岳石刻、南充醴峰观、新津观音寺；河北蔚县的玉皇阁、南安寺塔、井陉的福庆寺等，都在设定里出现。

由于 DeepSeek 官方已把全部模型开源，调用 API 只是其中一种做法。借助 LangChain 或 HuggingFace 的接口，本地部署也是可行的。需要留意的是，负责推理的大模型对 GPU 的消耗远高于嵌入模型，后者参数量通常很小。所以完整上线的 671B DeepSeek-V3 或 DeepSeek-R1 成本不低，Hugging Face 社区提供的多种参数规模的蒸馏版本会是更经济的选择。

LangChain 里实现 RAG 的整体思路和 LlamaIndex 非常接近。两者最大的价值在于提供了嵌入模型、向量库和大模型的一体化接入层，让切换变得无缝。

从功能取向上看，LangChain 更偏向把各个模块拼起来、把流程串起来，并没有像 LlamaIndex 那样在内部塞进大量现成能力。举例来说，LlamaIndex 五行代码就能自动完成索引构建，而 LangChain 则要你自己指定向量数据库；LlamaIndex 的查询引擎自带提示设计、检索与生成逻辑，LangChain 则要求使用者自行准备提示模板、把结果喂给模型，再手动处理返回内容。换句话说，LlamaIndex 在文档索引和检索算法上封装得更深，也做了不少专项优化，具体内容会在第4章和第5章展开。

## LCEL重构RAG链
LangChain 既然主攻流程编排，2023 年底推出了 LCEL，也就是 LangChain Expression Language，专为组合大模型应用链而生的声明式语法，也是新版的核心。借助类似 Unix 管道“|”的操作符，可以把提示模板、模型调用、输出解析器等元件一口气串成可扩展的生产链。

LCEL 的主要优势：

- 写法更清爽，可复用性高：对比老式的 LLMChain，声明式写法减少了冗余，原型可以直接搬到生产不用改动。
- 组合更自由：支持 RunnableParallel 并行执行、RunnablePassthrough 动态传参、条件分支等。例如在 RAG 场景里可同时检索多个源，再合并生成。
- 并行提速：用 RunnableParallel 跑独立检索，用 RunnableBatch 批量处理输入，把串行等待变成并行计算，吞吐量明显提升。
- 原生异步高并发：.ainvoke() 和 .abatch() 原生可用，和 LangServe 等异步服务栈配合，单机可扛千级并发，避免阻塞。
- 流式响应快：.stream() 支持逐块输出，结合底层优化，首个 token often 毫秒级返回，长文本交互体验更好。
- 可追踪可调试：和 LangSmith、LangServe 无缝衔接，能实时查看链路、检索到的原文与模型中间回答，实现全生命周期管理。
- 模式校验：基于链结构的 Pydantic 和 JSON Schema 校验，保障输入输出类型和格式，减少运行时错误。

前面讲的 RAG 体系完全可以用 LCEL 来改造。

[PRE2]
这段实现把 vectorstore.as_retriever 封装进链，让检索器成为链的一个组件，整体更模块化、可组合。相对更底层的 vector_store.similarity_search 直接调用，LCEL 的写法更清晰：先用 RunnablePassthrough 构造出包含 context 和 question 的字典，把原文和问题一起传下去；再喂给提示模板；随后交给语言模型处理；最后用 StrOutputParser 解析结果。这种管道式表达让数据流向一目了然，也更容易调试和调整。

LCEL 能不能用因人而异。对很小的 RAG，如果之前没接触过 LCEL，硬切并非必须。但它用极简语法解决了模块化组合、生产部署和实时监控这几个核心痛点。

## LangGraph构建RAG图
到本书写作时，LangChain 官方文档的 RAG 案例已改用 LangGraph。LangGraph 把应用流程抽象成图，由节点 node 和边 edge 构成，节点是独立操作，边定义执行顺序和条件。图结构相对复杂，实现会比线性链更费劲，但换来了明显好处。

首先状态管理更清晰，用 TypedDict 显式声明状态，流转更容易追踪维护。其次流程控制更灵活，可轻松加入条件分支和循环，支持更复杂的检索-生成模式。再者可观测性更强，天然支持 LangSmith 追踪，能可视化流程图辅助调试。最后运行模式丰富，同步、异步、流式皆可，方便嵌入更大系统。

下面用 LangGraph 复现前面同等功能的 RAG。

先安装 LangGraph 包。

[PRE3]
再配置环境变量以打开 LangSmith 追踪，需要在 LangChain 官网注册并申请 API Key。

[PRE4]
索引部分沿用之前做法，后续检索与生成改用 LangGraph 实现，具体代码如下。

[PRE5]
LangGraph 还自带可视化控制流的工具。

[PRE6]
执行后会得到图 0-14 所示的检索生成流程。图 0-14 通过 LangGraph 构建的检索和生成流程

所有和大模型的交互细节都会被 LangSmith 记录，见图 0-15。这正是 LangGraph 加 LangChain 的附加价值。图 0-15 LangSmith 对 RAG 流程中与大模型交互的细节进行了清晰的追踪

小冰：咖哥，讲到这我选择困难症犯了。仅 LangChain 就有直接调用、LCEL、LangGraph 三种路线，我该怎么选？
咖哥：LangChain 给过参考原则。
- 单纯一次模型调用，不需要 LCEL，直接用 LangChain 提供的底层接口即可。
- 只做简单链路，比如提示词+模型+输出解析+基础检索，LCEL 就够了。
- 要做复杂链路，比如分支、循环、多智能体，LangGraph 更合适。注意 LangGraph 的节点内部照样可以嵌套 LCEL。

## 放弃框架 自建RAG流水线
咖哥：到这里，LlamaIndex 和 LangChain 在搭建 RAG 上的各自长处应该清楚了。
小冰：具体是啥优势？
小雪：LlamaIndex 把很多索引和检索策略内置了，LangChain/LangGraph 则更强调流程编排和状态追踪。既然框架优势这么明显，能不能完全不用框架，从零搭一个 RAG？
咖哥：当然可以。代码量会多一些，但对每个组件的理解更透彻，也能按需定制。

下面用 SentenceTransformers 做嵌入、Faiss 做向量库、Claude 或国产 DeepSeek 做生成大模型，手写一个 RAG。

先导入依赖。

[PRE7]
先把游戏相关文档用轻量开源向量模型 all-MiniLM-L6-v2 编码，准备入库。

[PRE8]
古琴与管弦撞出战斗的昂扬，笛箫配木鱼写出禅意空灵。悟空进入关键场景时，古风配乐总让人仿佛回到神话时代。

[PRE9]
文档向量维度: (9, 384)