[PRE0]
先跑一遍前面的示例，完整代码在源文件 callback-in-chain.py 里，输出效果可对照图 5-17 查看。

[PRE1]
从运行日志能看出，StdOutCallbackHandler 会在链条的每个进入与退出节点都打印一条以 “>” 开头的提示。这种日志方式在调试时非常直观。不过当需要让回调做更多事时，就不能只用默认实现，需要自己写一个回调类挂到链上。

自定义回调要继承 langchain_core.callbacks.base 里提供的 BaseCallbackHandler。 这个基类已经预置了大量事件钩子，比如链开始 on_chain_start、链结束 on_chain_end，模型调用开始 on_llm_start、结束 on_llm_end，工具调用开始 on_tool_start、结束 on_tool_end 等，异常捕获的钩子也在里面。借助这些钩子，可以在关键节点灵活植入自己的逻辑，实现全流程的监控和处理。

下面示例里定义了一个 CustomCallbackHandler，并重写了链起止、模型调用起止这几个钩子。

[PRE2]
在程序里把回调实例指定为 CustomCallbackHandler 的一个对象，其余部分与上一示例保持一致，具体写法见下方代码。

[PRE3]
注：为了让输出更清晰可读，这里把示例的提示词做了精简，减小模型返回长度。

执行这段示例，完整代码在 customCallbackHandler.py，运行效果见图 5-18。结合代码和输出就能理解各钩子被触发的时机以及对应的处理结果。

实际项目里，可以按需求在不同事件点塞入不同的业务逻辑，完成特定的功能。

当回调逻辑变得复杂，为了职责清晰和可维护，往往需要多個回调协同工作。此时只要把多个回调实例一起传给链即可。之前示例里回调是放在 “[]” 这种列表里的，多個回调就依次列在同一个列表中即可。

下面这个示例同时引入了用于流式打印的 StreamingStdOutCallbackHandler，并额外定义了 CustomCallbackHandler1 和 CustomCallbackHandler2，实例化后全部加入回调列表，具体代码如下。

[PRE4]
执行 multicallback.py 里的程序，输出参考图 5-19。可看到 CustomCallbackHandler1 与 CustomCallbackHandler2 先后响应 on_llm_start，随后 StreamingStdOutCallbackHandler 以流式方式把模型回复逐字输出，最后 CustomCallbackHandler1 与 CustomCallbackHandler2 再依次响应 on_llm_end。

5.7 让 Agent 会联网搜索：LangChain 智能体入门

智能体是 AI 领域一个核心概念，本质上是能感知环境、自主决策并执行动作的系统。它可以是纯软件、硬件或软硬件结合，目标是模拟人类智能行为。广义上，任何能思考并与环境交互的独立实体都可以抽象为智能体。

一个合格的智能体通常具备以下特质。

- 1. 自主性
能够在缺乏外部指令时，依据环境变化自行调整行为和状态。
- 2. 反应性
能感知环境变化并及时作出对应反应。
- 3. 主动性
不只被动响应，还能主动采取行动以适应或改造环境，实现无外部刺激时的自主任务。
- 4. 适应性
通过积累经验和知识，动态调整策略以适应新环境。
- 5. 社会性
可以与其他智能体交互协作，共同完成目标或解决问题。

把智能体融入应用，能显著提升交互性、自动化和智能化水平，提高用户体验与工作效率，并让业务决策更精准。它可以支撑自动化处理、个性化推荐、智能客服等高级场景。

本节用 LangChain 演示如何搭建一个简易智能体，以具体案例说明基本思路和实现步骤。

完整智能体一般包含感知、决策、执行三部分，若需要还能加入学习、通信、情感和界面等模块。这里实现的是仅用网络搜索工具作为感知层的智能体，也就是一个以 Agent 方式实现的网络搜索助手。

本次选用 Tavily 作为搜索工具，Tavily 提供在线搜索服务，地址 https://tavily.com 。需要先申请 API Key 并写入环境变量 TAVILY_API_KEY，之后即可调用。创建 Tavily 本地代理时，先读取环境变量中的 Key，再用其构造器生成对象实例，之后就能发起搜索。

以下代码演示如何创建模型代理实例和 Tavily 实例，并把后者作为搜索工具放入工具集合 tools 中。

[PRE5]
准备好工具后，可以借助 LangChain 的提示词模板和 create_react_agent 函数组装智能体。LangChain 内置多种 Agent 形态，包括工具调用、OpenAI 工具、OpenAI 函数、XML、结构化聊天、JSON 聊天、ReAct、自问带搜索等。

本例以 ReAct 为示范，先引入 ReAct 的预设提示词。

[PRE6]
第一行导入 langchain 的 hub 模块，hub 用于管理和拉取社区预定义的 prompt 模板。第二行从 hub 拉取名为 “hwchase17/react” 的模板，该模板定义了 Agent 的初始设定和行为规范，详细信息可参考 https://smith.langchain.com/hub/hwchase17/react ，模板内容大致如下。

> Answer the following questions as best you can. You have access to the following tools:

>
> {tools}
>
> Use the following format:
>
> Question: the input question you must answer
> Thought: you should always think about what to do
> Action: the action to take, should be one of [ {tool_names} ]
> Action Input: the input to the action
> Observation: the result of the action
> ... ( this Thought/Action/Action Input/Observation can repeat N times )
> Thought: I now know the final answer
> Final Answer: the final answer to the original input question
>
> Begin!
>
> Question: {input}
> Thought: {agent_scratchpad}

当然也可以直接在代码里手写一个类似的 prompt，构造方式如下。

[PRE7]
这里的提示词全部是英文，如果希望运行时稳定输出中文，建议把整段提示词换成中文版本，避免模型中途切换语言。

工具和提示词准备妥当后，就能创建 Agent 及其执行器，相关代码如下。

[PRE8]
第一行同时导入 AgentExecutor 和 create_react_agent。AgentExecutor 负责运行 Agent，create_react_agent 用于生成 ReAct 类型的 Agent。随后用 create_react_agent 构建 Agent，再用 AgentExecutor 实例化执行器。

到此，一个基础的智能体应用就搭建完成。可以通过调用 invoke 方法传入用户问题来激活它，例如查询“新型电力系统”的含义。

[PRE9]
运行 agent-start.py 及 agent-start2.py，过程如图 5-20 所示。可以看到 Agent 先判断需要借助网络搜索获取相关资料，调用搜索工具拿到网页内容后，再对结果进行整理，最后给出针对用户问题的最终答案。