在实际部署大模型时，普遍卡在两点：外部知识无法及时刷新，以及一次能处理的输入长度有限。于是工程上必须想办法把必要的先验信息喂进去，并按不同任务单元灵活调用。LangChain 把这类需求主要收敛到 Memory 这个抽象层，正是它存在的核心意义。

换句话说，真正流畅的对话离不开回看历史，至少能直接调取之前的问答片段。面对更复杂的业务，往往还需要一个会自我更新的状态容器，来持续维护事实、实体以及它们之间的关联。这种把往期交互存下来并随时取用的能力，就是常说的记忆。

图9-23里，Memory 充当记忆数据的抽象仓库，本身并不产生推理，只是提供存放与取用的空间。它和 Chains 是配合关系，就像为不同场景定制 Prompt Template 一样，Memory 负责把用户的原始提问、模型的回复乃至链路中间产生的中间产物都存下来，必要时再供给下游使用。

同一幅图中，Model I/O 本质上就是一条标准 Chain，内部固定了 Prompt、模型与 Output Parser 三件套。它既能直接消费用户传进来的 {question}，也能读取 Memory 里吐出来的 {past_passages} 作为上下文。没有 Memory 时链路结束就是 {answer}；接入 Memory 后，结果会按预设策略写入记忆，供后续步骤复用。

[PRE0]

### 9.4.3 常用Memory

- 1. ConversationBufferMemory

ConversationBufferMemory 是 LangChain 里最常用的对话记忆实现，几乎成了构建连贯多轮对话的标配。它的基本工作就是把每次交互中人和模型说的话抓下来存好，并在下一次调用时自动拼进上下文，让对话保持连续性。

代码9-15展示的是原生写法：没有框架时开发者得自己维护一个 History 对象或数据库，手工拼接历史，繁琐且易错。引入 ConversationBufferMemory 后，存储和检索被自动化，开发者可以把精力放回业务逻辑本身。

[PRE1]

运行结果如图9-24所示。

[PRE2]

值得一提的是，这个模块能把原本不具备上下文能力的普通文本生成模型改造成可记忆的聊天体。如代码9-16所示，只需接入它，非对话模型也能实现轮次连续、上下文依赖的交互，大幅拓宽了模型的应用边界。

它把历史以结构化形式保留，并开放给后续 Chain 调用，从而提升系统的灵活度。基于此可以实现个性化推荐、上下文问答、历史情绪分析等场景。模块也可定制，比如通过 memory_key 指定变量名，或用 return_messages 控制是否把历史原样返回，进一步满足个性化需求。

[PRE3]

输出结果如图9-25所示。

Out[16]: {'history': 'Human: 你好\nAI: 你好，请问有什么事情吗'}

图9-25

代码9-17演示了把历史当作消息列表取回，在搭配 Chat 模型时特别好用。

[PRE4]

输出结果如图9-26所示。

Out[17]: {'history': [HumanMessage(content='你好'), AIMessage(content='你好，请问有什么事情吗')]}

图9-26

代码9-18则展示了在 Chain 中使用它的方式，打开 verbose 即可看到提示拼接过程。

[PRE5]

运行结果如图9-27所示。

> Entering new ConversationChain chain...

Prompt after formatting:

The following is a friendly conversation between a human and an AI. The AI is talkative and provides lots of specific details from its context. If the AI does not know the answer to a question, it truthfully says it does not know.

Current conversation:

Human: 你好

AI:

> Finished chain.

'你好！有什么我可以帮助你的吗？'

图9-27

对比直接调 API，用上 Memory 后上下文会被自动维护并送给模型。再次调用时 Prompt 会变长，历史一次回复也被带入，如代码9-19所示。

[PRE6]

结果变化如图9-28所示。

[PRE7]

图9-28

#### 2. ConversationBufferWindowMemory

前面用 ConversationChain + ConversationBufferMemory 能把全量对话串起来，虽能实现多轮对话，却有个明显短板：历史无差别累积，Token 消耗很快。

代码9-20 引入 ConversationBufferWindowMemory 来缓解。不同于全量记录，它只保留最近 k 轮对话，形成滑动窗口。这样既避免缓冲区无限膨胀，也更高效利用资源。

代码9-20

[PRE8]

取最后一次predict的输出结果，如图9-29所示，请注意，这里没有出现第一次交互的记录。

[PRE9]

#### 3. ConversationSummaryBufferMemory

代码9-21 展示的 ConversationSummaryBufferMemory 就是为此设计的。它仍保留最近的原始交互，但对更早的部分做摘要压缩，而不是直接丢弃。刷新时机不按轮次，而是按词元长度判断。

该模块会在内存中保留一段最近的原始对话，其余历史则被提炼成摘要并继续参与推理，判断是否压缩依据的是 Token 占用而非固定轮数。

[PRE10]

输出结果如图9-30所示。

[PRE11]

如代码9-22所示，继续询问，发现conversation_with_summary得到了更新。

代码9-22

[PRE12]

运行结果如图9-31所示。

[PRE13]

此时控制缓冲区大小的不是对话轮数，而是 Token 预算。官网还提供多种 Memory 变体，适配不同场景。核心思路都是在积累信息。Chat 类模型还能按角色信息做分类增强。

整体来看，Memory 为 Chain 提供了状态化能力。过去 Chain 是无状态、可组合的，现在借助 Memory 能够持久化会话状态，并通过多种方式维护、保存与加载。

## 9.5 Agents

### 9.5.1 理论

- 1. 流程图

说到 Agent，我们期待的不是简单的工具调用，而是一个能自主思考、理解需求、拆解任务并逐步推进的智能体。这种能力更接近 AGI 的愿景，像贾维斯那样的助手。

基于这一理念，诞生了多种 Agent 构建范式。大模型应用的落地程度，很大程度上取决于开发者对模型的理解。LangChain 在迭代中形成了相对完整的 Agent 架构，可视为 Agent 1.0。它不仅提供现成方案，更能激发创新思维。

从能力来看，Agent 的设计思路与之前搭建 Chain 有明显差异。Agent 的核心价值在于解决复杂应用问题。下面介绍 LangChain 中 Agent 的设计方式和使用思路。

工具可以是 API、函数、数据库等任意资源，用来扩展模型能力而不只输出文本。模型与工具结合的关键在于提示设计和响应解析，让模型能挑对工具并给出正确参数。

如图9-32所示，在 Chains 的抽象框架下，一条链路可整合无限多个工具，借助大模型判断该调用哪个 Tool，以及从用户输入中抽取执行所需的关键信息。因此，用 Chains 构建流程时可以预设工具调用顺序。思想上类似传统应用开发，只是把部分分析和处理交给了大模型。虽然模型在链路中的占比不高，但这种方式提升了稳定性，能更好控制每个环节，降低错误率，特别适合流程相对固定的业务场景。

[PRE14]

- 2. 大模型的作用

大模型在整个链路中起到的作用如下。

- 1. 解析用户输入，以确定需执行的特定流程。
- 2. 当需调用外部工具时，从用户输入中提取关键信息。
- 3. 基于分析数据，输出最终结果。

然而 Chains 有局限：它难以识别诸如“先查两次再对比”这类需要循环或多步聚合的需求。当用户要求重复执行同一链路并对结果做综合分析时，传统 Chain 就力不从心，如何实现就成了下一步要解决的问题。