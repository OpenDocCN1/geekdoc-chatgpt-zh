LangChain 聊天模型与 LLM 集成指南

[PRE0]
LangChain 提供了开发者友好的对话模型搭建基座，兼顾强大功能与高度可定制性。借助 SystemMessage、HumanMessage、AIMessage 等多种消息类型，聊天过程中的不同角色与内容得以清晰区分，整个对话流更易组织与管理。配合函数调用能力，模型可在对话中动态调起外部函数或工具，极大拓展了与外部系统的交互边界，能够完成更复杂的任务链。

框架还统一了 ChatModel 与 BaseChatModel 等标准接口，让自研聊天模型无缝接入 LangChain 生态并复用其丰富能力。原生支持流式输出与异步编程，使其在实时对话、低延迟、高交互场景下表现突出。内置缓存机制可加速响应、减少 API 调用、降低成本。与此同时，模型实现细节完全开放，灵活性足以应对各类特定需求。

以客服机器人为例，SystemMessage 可固化客服行为规范，HumanMessage 与 AIMessage 用来还原客户和客服的问答流，函数调用则接入客户资料库与订单系统实现业务闭环。在写作助手类应用中，流式传输让用户边生成边阅读，缓存机制则结合用户的写作历史与偏好动态调整 LLM 输出，实现个性化陪写。

整体来看，LangChain 交付了一整套构建可扩展、功能完备聊天系统的工具链。开发者可在其之上发挥创造力，打造贴合真实场景的智能对话体验，随着持续迭代，聊天应用的质量与表现也会不断提升。

#### 3.2.4 大语言模型

LLM 是 LangChain 的核心驱动。LangChain 本身并不内置语言模型，而是提供一个统一交互标准，接收字符串输入并返回字符串输出。面对 OpenAI、Cohere、Hugging Face 等众多厂商，LLM 类提供一致的调用面，方便切换与横向对比。

若选用 OpenAI，需要先安装官方 Python 包：pip install openai。调用 API 必须持有密钥，可在 OpenAI 官网注册后获取。最常见做法是写入环境变量：export OPENAI_API_KEY="你的API密钥"。不希望配置环境变量时，也可在初始化 OpenAI LLM 实例时直接传入参数 openai_api_key：

[PRE1]

否则，可以直接初始化，不需要任何参数。

[PRE2]

使用本地 Gemma 模型时，先从官方仓库下载最新权重文件，解压后放置到工作目录，再初始化对应的 Gemma LLM。

[PRE3]

这里的 model_path 用来指定 Gemma 权重所在的路径。

初始化完成后即可像其他 LLM 一样使用：llm.invoke("请写一段励志的话")。返回结果会是一段励志文字，内容由模型决定。

如需接入自研模型或 LangChain 尚未封装的包装器，可通过自定义 LLM 实现。自定义实现至少包含两个必要项和一个可选项：

- _call：必要方法，接收输入字符串、可选停止词，返回字符串
- _llm_type：必要属性，返回字符串标识，仅用于日志
- _identifying_params：可选属性，返回字典用于打印类属性

【例3-6】 自定义大语言模型（代码参见3.6.py）。

[PRE4]

以上示例展示了自定义 LLM 的典型骨架，继承 LangChain 的 LLM 基类并重写 _call 方法实现核心逻辑，通过 _llm_type 与 _identifying_params 暴露类型与配置信息，便于调试与日志追踪。该实现仅截取输入前 n 个字符，常用于测试或演示如何在框架内创建自己的 LLM。

随后可构建更复杂的 CustomGemma，用来模拟 Gemma 的调用并支持动态调整生成参数，例如 temperature 与 max_length，增强使用的可控性。

[PRE5]

输出通常是一首关于春天的诗，具体结果取决于 Gemma 的生成能力与所设置的超参数。该类演示了如何面向特定模型封装自定义 LLM：在 _call 中调用假设的 generate 接口，按 prompt 与参数生成文本，并通过 temperature 与 max_length 控制多样性与长度。类中保留 _identifying_params，可快速记录使用的配置，利于日志与排查。

需要留意的是，示例代码并非开箱即用。实际运行前要根据生成函数的真实签名调整调用方式，并确认模型权重路径正确。若追求简便，可用 Transformer 等基础模型替代上述实现。

#### 3.2.5 输出解析器

输出解析器是 LangChain 中结构化响应的关键环节，职责是将模型生成的原始文本转化为更易处理的结构化格式。框架内置了多类解析器，覆盖不同场景。

OpenAITools 与 OpenAIFunctions 专注于 OpenAI 函数调用输出的解析；JSON、XML、CSV 等标准格式解析器可把文本转为对应数据结构；OutputFixing 与 RetryWithError 能在解析失败时自动触发重试或修复；Pydantic 与 YAML 解析器支持将输出映射到用户自定义数据模型；Pandas DataFrame、Enum、Datetime、Structured 等则面向特定领域需求。

多数解析器支持流式处理，可在生成过程中边读边解析，减少等待。解析器通常提供格式说明，帮助模型按预期格式输出。尽管偶尔需要重新调用 LLM 修复错误，整体上解析器显著提升了应用的实用性与效率。

当任务需要结构化信息而非自然语言时，选择合适的解析器尤为重要。开发者可根据需求灵活组合，以最大化模型潜力。

每个解析器一般实现以下方法：

- 获取格式说明：返回指导模型如何格式化的说明字符串
- 解析：接收模型响应字符串并解析为结构化数据
- 可选的带提示解析：同时接收响应与原始提示，用于在重试或修复时利用上下文信息

【例3-7】 输出解析器类型PydanticOutputParser（参考代码3.7.py，3.7.1.py）。

[PRE6]

示例先创建使用 gemma:2b 的 OllamaLLM 实例，然后定义 Pydantic 模型 Joke，包含 setup 与 punchline 两个字段，并用校验器确保 setup 以问号结尾。接着用 PydanticOutputParser 构建解析器，将文本转为 Joke 实例。PromptTemplate 要求模型严格输出 JSON，并把解析器提供的格式说明嵌入提示。模板与模型串联后，以空查询触发生成，随后尝试 JSON 解析，成功后再交由 PydanticOutputParser 完成最终校验，失败则输出错误信息。