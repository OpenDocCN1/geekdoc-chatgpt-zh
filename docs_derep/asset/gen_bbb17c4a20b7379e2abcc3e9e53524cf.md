[PRE0]

代码4-6把Human-in-the-Loop的完整做法串起来了。human_approval节点内会触发interrupt，并把待人工核验的信息以字典形式带入。执行走到这里时流程会被挂起，返回体中的__interrupt__字段正好承载暂停信息供前端呈现。人工确认完毕后，通过Command（resume="approved"）唤醒流程，resume传入的值会作为interrupt的返回值返回，节点据此刷新approved字段。紧接着execute_task会根据approved的取值判断是继续执行还是直接取消。务必注意，interrupt生效的前提是已配置checkpointer，否则暂停状态无法被持久化。

## 4.3 CrewAI与其他框架协作机制

### 【学习目标】

- 1) 吃透CrewAI的Crew-Agent-Task协作模型以及角色定义方法
- 2) 认识MetaGPT依托SOP实现软件开发自动化的思路
- 3) 理解Pydantic AI在Agent开发中提供的类型安全机制
- 4) 了解OpenAI Agents SDK的快速搭建范式

在AutoGen和LangGraph之外，CrewAI、MetaGPT、Pydantic AI以及OpenAI Agents SDK各有侧重，分别在角色扮演协作、软件开发自动化、类型安全保障和轻量编排上提供差异化能力。

### 4.3.1 CrewAI角色定义与任务委派

CrewAI是一个脱离LangChain的轻量多Agent框架，主打通过角色扮演来组织Agent协作。其设计核心是把Agent编排成类似真实团队的“Crew”，让每个Agent承担特定角色，借助清晰的任务分工来达成复杂目标。编排上提供两种互补手段：Crews适合需要自主决策的协作场景，Flows则适合对控制精度要求高、事件驱动的流程。

框架采用三层抽象：Agent是具备角色、目标、背景故事的自主单元，可配置工具与记忆；Task是分配给Agent的具体工作单元，包含任务说明、期望产出和执行限制；Crew负责把多个Agent组织成协作团队，并约定执行顺序是串行还是层级，以及协作规则。该思路让开发者像组建真实团队一样搭建多Agent系统，每个Agent专注专业领域，通过自然的任务委派完成整体目标。

图4-9展示了这套三层抽象。最外层的Crew决定团队级配置，如执行方式、记忆类型以及回调；中间层的Agent代表团队成员，包含Role、Goal、Backstory、工具集和LLM配置；底层的Task链定义具体工作流，每个Task指定负责人，并通过Context参数引用前置Task的输出。虚线箭头表示Agent与Task的绑定关系，实线箭头表示Task间的执行先后与数据流向。

[PRE1]

代码4-7展示了CrewAI角色定义与任务分配的完整实现。

#### 代码4-7 CrewAI角色定义与任务分配实现

[PRE2]

代码4-7呈现了用CrewAI搭建多Agent团队的标准做法。先用Agent类创建两个角色、目标、背景故事明确的Agent，并为其配备专属工具；再用Task类定义任务，写明任务描述、预期输出和负责人，writing_task通过context声明对research_task的依赖，从而实现任务间的数据传递；最后用Crew类组建团队，设置顺序执行模式并调用kickoff启动。框架会自动完成Agent间的配合、任务调度与上下文传递。

#### Prompt 4-1 CrewAI研究员角色System Prompt模板

[PRE3]

Prompt 4-1给出了CrewAI研究员角色的System Prompt模板结构。模板里用占位符引用Agent的角色、目标、背景故事和当前任务描述，运行时由框架自动填充。Prompt明确了工作规范：使用工具、分析数据、呈现结论、标注来源、交叉验证，并规定了输出格式如执行摘要、关键发现、详细分析等，保证Agent产出稳定可用。这种结构化Prompt是CrewAI实现可靠协作的关键。

### 4.3.2 MetaGPT软件开发自动化实现

MetaGPT是一个元编程框架，把标准操作流程SOP融入LLM多Agent协作，专注软件开发自动化。其创新点在于复现真实软件公司的组织架构与工作流，通过产品经理、架构师、项目经理、工程师等角色配合，把一句话需求转化为完整项目。

设计借鉴了软件工程的最佳实践。传统多Agent系统直接串联LLM容易出现级联幻觉，而MetaGPT用SOP约束Agent产出结构化文档，如产品需求文档、系统设计、API规范，每个阶段的产出经校验后才进入下一阶段，有效遏制错误累积。这种流水线式协作使MetaGPT在代码生成评测中表现突出。

图4-10展示了MetaGPT模拟团队的协作路径。用户输入需求后，产品经理做竞品分析与需求分析，输出PRD；架构师基于PRD进行系统设计与技术选型，输出设计文档；项目经理把设计拆解为具体任务并规划进度；工程师按设计编写源码；QA编写用例并执行测试，发现Bug后通过反馈回路交工程师修复。

[PRE4]

#### 代码4-8 MetaGPT项目初始化与执行示例

[PRE5]

代码4-8体现了MetaGPT的简洁用法。通过Team类创建虚拟软件公司团队，用hire方法加入产品经理、架构师、项目经理和工程师等角色，工程师可多实例并行。invest方法用于设定Token消耗预算，run_project方法接收需求描述启动项目。run方法异步运行协作流程，n_round限制最大轮次。运行结束后，工作目录会生成完整的项目文件，包括产品需求文档、设计文档、源码和测试代码。

### 4.3.3 Pydantic AI类型安全Agent开发

Pydantic AI由Pydantic团队推出，把Pydantic的数据校验能力带入Agent开发，侧重类型安全与生产级可靠性。其理念类似FastAPI对Web开发的革新，旨在为GenAI应用提供同等体验提升。

类型安全是核心。传统Agent开发中LLM输出不确定，格式不符、字段缺失或类型错误是常见痛点。Pydantic AI用Pydantic模型定义输出结构，运行时自动校验LLM响应，确保结果符合预设数据模式。实现“编译期通过就能稳定运行”，大幅降低运行时异常，提升系统可靠性。

框架还提供模型无关性，兼容DeepSeek、智谱、通义千问等多种LLM；类型安全的依赖注入用于上下文传递；集成Logfire实现全链路可观测；支持流式输出与结构化数据的实时校验。

Pydantic AI的类型安全机制如图4-11所示。

[PRE6]

图4-11对比了传统方式与Pydantic AI方式处理LLM输出的差异。传统方式调用LLM后直接JSON解析，易出现KeyError或TypeError等运行时异常。Pydantic AI方式在调用后增加Pydantic校验环节，保证输出符合预定义模型，自动检查字段完整性并完成类型转换与校验。图下方展示了Pydantic模型定义示例，使用BaseModel和Field约束字段类型与取值范围，在Agent配置时指定result_type即可启用自动校验。

#### 代码4-9 Pydantic AI类型安全Agent实现示例