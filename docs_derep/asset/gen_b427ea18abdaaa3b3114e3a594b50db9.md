基础模型本质上是“文本输入、文本输出”的模型，也常被叫做纯文本补全模型。
聊天模型一般建立在基础模型之上，处理的是消息列表的输入并返回消息列表的输出，也被称作聊天补全模型。

> **提示** 基础模型收字符串发字符串，代表作是 GPT-3。聊天模型是为对话而优化的变体，接收的是带角色标记的消息列表，比如系统、助手和用户，返回对话消息。GPT-4 和 Claude 就属于这一类。

LangChain 同时给两种模型提供了统一的调用方式。纯文本场景常用 `predict()`，聊天场景常用 `predict-messages()`。单独使用时建议直接调用模型专属方法，需要混用多种模型时，统一接口会更省心。

#### 4.1.1 基础模型接口初探
LLM 是 LangChain 应用的发动机。LangChain 本身不造模型，但提供了统一的交互层，让 OpenAI、Cohere 等厂商的模型能用同一套写法调用。下面以 OpenAI 的基础模型为例演示。

##### 1. 准备 OpenAI 密钥
先在命令行终端安装 OpenAI 的 Python 包。

[PRE0]

要用 OpenAI 服务，得先在官网申请 API Key。拿到 Key 后，可以把它写入环境变量。

[PRE1]

环境变量配置好后，就能不传任何参数直接实例化模型。

[PRE2]

不想动环境变量，也可以在创建 OpenAI LLM 类时把 `openai_api_key` 直接传进去。

[PRE3]

##### 2. OpenAI 接口常用方法
密钥准备就绪后，基础模型就能直接使用 OpenAI 提供的方法。

###### 1）call() 方法
导入必要包、设定参数后，用 `call()` 启动模型即可。该方法只接受字符串输入并返回字符串。

[PRE4]

输出以下信息。

> '在历史上，有多个城市曾经是中国的首都，以下是一些重要城市的名称和它们在中国历史上的地位。

...//省略部分内容

###### 2）generate() 方法
`generate()` 可以一次性喂入字符串列表，得到更丰富的返回，包含多个候选结果以及厂商相关的额外信息。

调用方式如下。

[PRE5]

#### 4.1.2 聊天模型接口初探
聊天模型虽源于基础模型，但使用习惯完全不同。

它不走“文本进文本出”的老路，而是采用专门的聊天消息接口。

要上手聊天模型，需要安装 OpenAI Python 包、配置好 API Key，并导入相关模块，这里用到 ChatOpenAI。

[PRE6]

聊天模型的接口围绕消息设计，而不是裸文本。

> 提示 LangChain 目前支持的消息类型有 AIMessage、HumanMessage、SystemMessage 和 ChatMessage。其中 ChatMessage 允许自定义角色，实际使用以 AIMessage、HumanMessage、SystemMessage 三种为主。

配置完成后，聊天模型同样可以使用 OpenAI 的调用方法。

##### 1. call() 方法
通过 `call()` 可以一次性发送多条消息并拿到回复。

[PRE7]

输出以下信息。

[PRE8]

OpenAI 的聊天模型原生支持消息列表输入。下方展示同时传入系统消息和人类消息的写法。

[PRE9]

输出以下信息。

[PRE10]

##### 2. generate() 方法
`generate()` 针对消息列表返回 LLM 内部信息，并生成带附加消息参数的 LLMResult 对象。

使用示例。

[PRE11]

输出以下信息。

[PRE12]

从 LLMResult 中可以拿到 Token 消耗等数据，代码如下。

[PRE13]

输出以下信息。

[PRE14]

#### 4.1.3 异步调用
并发调用多个 LLM 时，异步能明显提升效率。LangChain 基于 asyncio 为 OpenAI、PromptLayerOpenAI、ChatOpenAI、Anthropic、Cohere 等模型提供异步能力，其他模型也在持续适配中。

在 LangChain 里可以用 `agenerate()` 异步调用 OpenAI LLM，示例代码如下。

[PRE15]

输出以下信息。

[PRE16]

#### 4.1.4 流式输出
部分模型支持流式输出，LangChain 在还没收到完整响应前就能开始处理，体验更即时。

> 提示 当需要实时展示生成内容或做流式后处理时，这个特性非常关键。

目前已支持 OpenAI、ChatOpenAI、ChatAnthropic 等的流式传输。

使用流式需要实现 `on_llm_new_token` 回调，常用 `StreamingStdOutCallbackHandler()` 实现。

[PRE17]

输出以下信息。

[PRE18]

若想拿到最终结果，可配合 `generate()` 访问完整的 LLMResult，注意流式模式下目前无法获取 Token 使用量。

[PRE19]

输出以下信息。

[PRE20]

#### 4.1.5 Token 用量追踪
OpenAI 用户普遍关心 Token 消耗，LangChain 为此提供了追踪能力。

##### 1. 单次调用追踪
下面演示如何追踪一次 LLM 调用的 Token 用量。

（1）导入依赖

[PRE21]

（2）实例化 OpenAI 模型

[PRE22]

（3）用 `get_openai_callback()` 创建上下文管理器，捕获模型返回。

[PRE23]

输出以下信息。

[PRE24]

##### 2. 上下文范围追踪
上下文管理器可以覆盖范围内所有调用。

以下示例按顺序追踪多轮调用。

[PRE25]

输出以下信息。

[PRE26]

如果使用链或代理这类多步骤组件，上下文管理器会完整记录每一步，示例如下。

[PRE27]

输出以下信息。