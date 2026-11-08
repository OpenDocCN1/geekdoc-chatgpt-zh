[PRE0]

这个演示里一共串联了 5 个任务模块。模块之间通过连线明确了数据流向和上下游关系，模块本身对应图的节点 Node，连线则对应边的 Edge，正是这种图化的表达让整个应用的模块构成和执行顺序一眼就能看清。

能够这么灵活地定义和编排工作流，根源在于模块化 RAG 的演进方向。从第一章就提到过，和传统 RAG 的线性流水线不同，面向复杂业务和性能优化的新范式不断涌现，于是出现了查询改写、路由分发、检索与索引、重排、多种大模型生成等更多类型的 RAG 单元。为此需要一种能自由挑选、组合和编排这些单元的机制，这就是查询管道 Query Pipeline 以及 LangGraph 类组件出现的由来。

## 8.6.2 查询管道的两种调用模式

LlamaIndex 里的查询管道提供两种使用方式，均可基于图结构搭建工作流。

### 1. 线性串行链

把若干模块按顺序排成一条链，后一个模块直接消费前一个模块的输出，例如检索器到生成器、大模型到输出解析器等。下面是一个最小示例：

[PRE1]

这里用 `[prompt_tmpl, llm]` 把提示模板和大模型串起来形成一条简单链，运行管道即可得到结果，输出示意如图 8-53。

[PRE2]

从内部执行日志可以看到两个单元被依次触发：prompt_tmpl 先生成好 Prompt，随后 llm 拿着上游的提示继续生成最终结果。

### 2. DAG 模式

当 RAG 应用的流程变得复杂，就可以用 DAG 形态的查询管道来编排。以基于向量检索的 RAG 为例，演示如何用管道搭建查询引擎并跑通。

先按惯例准备好向量索引：

[PRE3]

再用查询管道把输入 input、提示模板 prompt_tmpl、大模型 llm、检索器 retriever、生成器 summarizer 这些单元组织起来：

[PRE4]

代码可读性很高：先实例化一个管道，再通过 add_modules 把模块放进去，最后用 add_link 声明连线关系。连线部分清晰展示了各模块如何配合。运行过程与最终结果如图 8-54 所示。

[PRE5]

图 8-54

图里完整呈现了管道的执行轨迹：按照编排的拓扑，模块按序被激活并交换数据，最终输出符合预期的答案。

## 8.6.3 查询管道内部如何运作

查询管道是构建在底层组件之上的声明式接口，它本身不做具体计算，更像一个指挥者，负责按预设的连接关系调度各个组件的输入输出，完成一次查询任务。相关组件的依赖关系见图 8-55。

[PRE6]

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

[PRE7]

定义好后即可把实例接入管道。改造前面的 DAG 示例：

[PRE8]

新增自定义单元后，别忘了补齐连线让它参与流程。运行后查看调试信息，如图 8-56。

[PRE9]

可以看到自定义的 output_parser 被正常调用：它接收 summarizer 的输出，经过处理后最终返回了 Phone 类型的结构化结果。

## 2. 用 FnComponent 快速构造

除了继承 CustomQueryComponent，还可以用更轻量的方式：用 FnComponent 把一个普通函数包装成查询组件。函数体即为运行逻辑，参数名和类型由开发者自行定义。