在分布式或云端运行的远端服务里，MCP 必须维持客户端和服务端之间稳定可靠的双向通信。此时基于标准输入输出的 STDIO 方式已经不适用，MCP 因此提供了两套基于网络协议的实现：HTTP SSE 与 Streamable HTTP。

HTTP SSE 算是较早的流式推送做法，最初就是为让服务端持续向客户端派发事件设计。它靠标准的 HTTP 长连接实现单向的数据流，把事件从服务器推到客户端，形成近实时的推送效果。在 MCP 体系里，它曾用来承载模型输出的流式返回，比如逐 token 推送内容。典型的 MCP HTTP SSE 通信链路如图 4-10 所示。

[PRE0]

图 4-10 MCP HTTP SSE 通信链路

SSE 依赖长连接维持通信，消息本身是单向的，这在需要高并发的 MCP 远端服务场景里会暴露出明显短板：

（1）通信是单向的。只支持服务端到客户端的数据流，客户端发起请求仍要走额外的 HTTP 通道。

（2）扩展性受限。难以承载多路并发流或复杂的状态同步，高并发下必须同时保持大量长连接，对服务器负载压力很大。

（3）稳定性易受网络影响。长连接在弱网或代理环境下容易断掉，需要额外做重连恢复。

正因为这些局限，随着 MCP 通信模型的演进，SSE 逐渐退出主力，Streamable HTTP 成为了更高效也更通用的选择。

从实现原理看，Streamable HTTP 基于 HTTP/1.1 的分块传输编码 Chunked Transfer Encoding 来做流式输出。响应头声明 Transfer-Encoding: chunked 后，服务器无需预知完整长度，就能把数据块持续推送给客户端，实现流式响应。对比之下，SSE 更像事件订阅式的长连接，专为单一通道推送设计；而 Streamable HTTP 直接利用标准 HTTP 响应流做分段与续传，更贴合 HTTP 原生语义。

在 SSE 模式里，服务端通常要显式暴露 /sse 端点来建立持久通道，客户端则用 EventSource 维系连接。Streamable HTTP 则去掉了固定的 /sse 端点。大多数请求由客户端以标准 POST 发起，如果需要 SSE 向后兼容，可以对端点发起 GET 来协商 SSE 会话。典型的 MCP Streamable HTTP 通信链路如图 4-11 所示。

[PRE1]

图 4-11 MCP Streamable HTTP 通信链路

下面是一个简化的 Streamable MCP Server 示例，若忽略 SSE 兜底兼容，只实现 POST 请求即可。

[PRE2][PRE3][PRE4][PRE5][PRE6]

从示例可以看出，排除进程内通信这类特殊环境后，STDIO 与 Streamable HTTP 在工具、资源和提示词的注册与调用方式上几乎一致。核心区别在于服务暴露方式。前者用 StdioServerTransport 搭建标准输入输出通道，适合本地进程交互；后者通过注册 HTTP POST 接口，使用 StreamableHTTPServerTransport 与客户端建立连接，实现基于 HTTP 的流式交互。示例运行效果如图 4-12 所示。

[PRE7]

图 4-12 MCP Streamable HTTP 效果

从双向通信的角度再看，WebSocket 也是一个极具吸引力的选项。相比 SSE 或 Streamable HTTP 这类单向流，WebSocket 在客户端与服务端间建立全双工通道，双方都能主动推送，而不局限于请求响应触发。在需要复杂交互或更高实时性的场景里，比如模型上下文同步、工具调用回传、流式生成的中断控制，WebSocket 能明显降低延迟和资源消耗。

在 MCP 语境下，若未来需求从“模型输出流式返回”扩展到模型与客户端的实时协同与事件驱动交互，WebSocket 将是更理想的基础承载。比如客户端可在流式生成过程中实时发送暂停、中断或上下文更新指令，服务端也能即时反馈状态变化，形成真正的双向流式通信。

也因此，Streamable HTTP 在设计上保留了向 WebSocket 过渡的思路，其基于会话 ID 的上下文机制本身就为未来的双向扩展留了入口。换句话说，Streamable HTTP 更像是过渡形态，它兼顾了传统 HTTP 的兼容性与流式通信的灵活性；而 WebSocket 可能代表 MCP 通信体系向更实时、更交互、更分布式方向演进的终极形态。

4.4 MCP 应用

本节说明在任意支持 MCP 客户端的 Code Agent 中如何接入 MCP 服务端并使用，包括如何消费服务端能力，以及如何结合 Code Agent 自带的 Memory 与 Rules 模块做工作流级协同，让 MCP 服务端在合适时机按预设流程高效执行。

4.4.1 接入本地与远程服务端

不同 Agent 的 MCP 客户端实现并不完全相同，接入细节会随参数获取和配置方式而异。例如 IDE 场景通常通过图形化界面或 JSON 配置完成接入，而命令行工具如 Claude Code 则多以终端命令完成安装与连接。

无论哪种 Agent，接入都离不开通信层。当前 MCP 主要支持 STDIO 与 Streamable HTTP 两种方式。

以 IDE 场景下的 STDIO 接入为例，只需在配置文件中声明服务端的启动命令即可快速建立通信。下面示例展示如何接入官方提供的 GitHub STDIO Server：

[PRE8]

核心逻辑是执行终端命令：

    npx -y @modelcontextprotocol/server-github

该命令会启动一个可被连接的进程通信通道 Stdio Transport，实现 Agent 与 MCP 服务端间的双向数据交互。若使用自建服务端，同样可用此方式，只需把 command 与 args 换成对应的可执行命令即可，例如 node your_server.js 或 python3 your_server.js。

这样无论官方或自研服务端，Agent 都能通过统一协议建立稳定连接。结合 Code Agent 内置的 Memory 与 Rules，还能让 MCP 服务端在特定时机自动触发固定工作流，实现任务的高效编排与智能调度。

4.4.2 借助 Memory 与 Rules 编排工作流

实际使用中，MCP 服务端常会同时提供工具与提示词。设计初衷是让 Agent 清楚何时以及如何调用这些能力，保持上下文一致性。

但真实业务工作流往往需要特定的流程组合，比如多个 MCP 服务端按自定义顺序协作，或在调用工具前先执行上下游逻辑。这时仅靠默认能力已不够，需要借助 Agent 的 Rules 扩展机制做工作流级编排。

目前主流的 Code Agent，如 Claude Code、Cursor、Qoder 等，都提供了 rules/workflow 相关配置接口。用户可基于此为指定工作空间或项目添加规则，让 Agent 按预期逻辑流自动完成 MCP 调用。优势在于：

· 扩展性强：可组合多个 MCP 服务端的调用。
· 成本低：规则描述采用自然语言，无需改动底层代码即可调试迭代。

由于厂商实现各不相同，本小节以思路为主，阐述其在实际工作流中的应用逻辑。

假设项目使用 Figma MCP Server 实现图生码功能，即从设计稿生成前端代码。实际业务中，生成代码前需先从自研 MCP 服务端获取组件定义。此时可补充一条自定义规则串联工作流。示例规则如下：

[PRE9]

该规则定义了一个典型的复合工作流：

（1）先调用自研组件库的 MCP Server，执行 getComponents 工具获取组件定义。
（2）将结果保存为上下文变量 custom_components。
（3）调用 Figma MCP Server 的 getAst 工具，并把上一步的组件数据作为输入参数传递。
（4）Agent 执行任务时会遵循该规则，保证调用顺序正确、依赖关系明确。

通过这种方式，用户可自由组合多个 MCP Server 的调用逻辑，实现模块化、自动化的工作流协同。规则机制遵循统一通信协议，同时具备很高的灵活性和可扩展性，让 MCP 更自然地融入企业级开发与交付体系。

规则定义方式取决于所用 Agent 的实现与调试效果。通常用 DSL 结构化配置工作流，能为 AI 提供更友好明确的语义约束与参数传递；若 Agent 采用 embeddings 引入规则，也可直接用自然语言描述。无论形式如何，核心都是以更低成本完成工作流编排，具体落地仍应以官方文档与推荐实践为准。

4.5 本章要点回顾

本章系统介绍了 MCP 的设计理念与整体架构，阐明其如何通过扩展模型上下文让复合型 Agent 无缝接入外部工具与能力。通过与 Function Calling 对比，分析了 MCP 在能力表达、扩展性与运行时可控性上的优势与权衡，帮助建立对协议定位与适用场景的整体认知。

实践层面，详细讲解了 MCP 基础开发流程，从客户端与服务端职责划分到调试工具 MCP Inspector 的使用，展示如何快速搭建可验证的开发链路，完成能力注册、联调与调试闭环。随后深入剖析 STDIO 与 Streamable HTTP 两种主要通信模式，比较性能、实时性、部署复杂度与跨平台适配差异，并结合场景给出工程选型建议。最后通过完整示例演示在支持 MCP 客户端环境中接入并运行服务端的过程，覆盖能力注册、调用到日志追踪的完整闭环。整章思维导图如图 4-13 所示。

[PRE10]

图 4-13 MCP：模型上下文协议思维导图

综合来看，MCP 并非提升智能的银弹，而是一种通信协议，为第三方应用或个人开发者提供标准化接口，让具备调度能力的复合型 Agent 获得外部工具与扩展能力。

换句话说，MCP 是一种 Context Engineering 的实践形式，让模型在既定上下文中识别工具、理解参数并完成任务。相较于通信方式本身，更关键的是如何设计 MCP 工具：参数如何结构化暴露、输入输出如何组织、怎样让 Agent 精准识别意图并补全上下文，这些才决定复合型 Agent 的能力边界与表现。

学完本章，读者应具备以下 4 项核心能力：

（1）理解并评估 MCP 在复合型 Agent 体系中的定位与优势，能在架构层面比较 MCP 与 Function Calling 的差异并做出合理选型。
（2）掌握 MCP 客户端与服务端的基础开发与调试能力，完成能力注册、调用与错误处理。
（3）在 STDIO 与 Streamable HTTP 之间具备工程判断力，根据场景选择最优实现方案。
（4）理解 MCP 能力在产品链路中的落地方式，能将其与推理、日志和监控系统整合，使复合型 Agent 能力实现可观测、可调试、可维护的闭环运行。

第五章 LangChain：复合型 Agent 开发框架

[PRE11]

本章介绍 LangChain，它是一个面向复合型 Agent 的开发框架，正逐步发展成完整的 AI 应用开发生态系统。重点介绍两个核心模块：用于快速构建 AI 应用和工具链集成的 langchain 模块，以及支持复合型 Agent 状态图工作流的 langgraph 模块。通过示例展示如何高效利用 LangChain 加速复合型 Agent 开发实践。

5.1 LangChain 生态全景

langchain 和 langgraph 在工程师社区已传播很久，广泛应用于各类 Agent 开发，并成为不少 AI 应用岗位的核心要求。对新手而言，常会产生疑问：

· LangChain 到底是什么？
· langchain 和 langgraph 的关系是什么？
· 为什么 LangChain 框架会包含 langchain 模块？

LangChain 由 Harrison Chase 于 2022 年 10 月推出，最初是面向 AI 应用的开源 Python 库和简易工具链包。当时 Agent 概念尚未普及，模型能力也远不如今日。随着 AI 时代到来，LangChain 已发展为完整的复合型 Agent 开发框架，提供从开发到部署的企业级生态系统，如图 5-1 所示。

LangChain 生态包含两个面向 Agent 开发的重要模块：langchain 用于 AI 应用的基础抽象与工具链支持，langgraph 用于复合型 Agent 的状态图工作流构建。

[PRE12]

图 5-1 LangChain 框架生态系统

至此，前述三个问题已清晰：社区常说的 LangChain 实际指代 LangChain 开发框架，其核心包含 langchain 模块和 langgraph 模块。说“LangChain 包含 langchain”容易产生歧义，更准确的说法是 LangChain 生态系统包含一个名为 langchain 的核心模块。为避免混淆，后文统一用“LangChain 框架”指整体，“langchain 模块”与“langgraph 模块”指具体模块。

除了 langchain 与 langgraph，生态还包括 LangServe 用于快速转为 REST API、LangSmith 用于 AI 应用监控与调优、低代码可视化编排平台 LangGraph Studio，以及众多第三方工具链集成。这些组件加速了原型验证与工具集成，但也带来困扰：

· 这些生态都需要吗？
· 应该从何处开始？

坦白说，LangChain 虽是影响力较大的 AI 应用开发框架，但生态并未避免软件工程常见问题——功能冗余与重复造轮子。它在快速原型、链式思路验证、集成丰富第三方工具时效率很高，但从工程实战看，部分生态模块并非构建 AI 应用的必需。

例如，工作流与状态管理逻辑可用轻量 DAG、事件驱动或状态机框架实现，未必绑定 langgraph 封装；工具集成部分直接调用 OpenAI、Anthropic、Vector Store 或数据库原生 SDK，往往获得更好可控性和可观测性；在应用部署层，选择 FastAPI、Serverless 或直接使用 LLM API 调度可能更灵活；即便是用于观测调试的 LangSmith，也可通过结构化日志、链路追踪如 OpenTelemetry 和简单指标收集替代，避免额外依赖和成本。

因此使用 LangChain 时，需结合业务场景、团队技术栈和维护成本综合考量。需要快速构建原型、验证链式思路或集成生态工具时，LangChain 能显著提效；若追求系统高可控性、性能可观测性和灵活调度，或需在复杂场景下分层管理 Agent 能力，直接使用原生 SDK、自建链路或混合竞品方案往往是更优选择。