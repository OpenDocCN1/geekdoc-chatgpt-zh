[PRE0][PRE1]

## **10.7 最小波动组合实战**

最小方差组合的核心思路就是不断调配持仓比例，让整组资产的波动被压到最低。在既定标的池里，寻找风险最轻的配比方案，就是它的任务。通过精细化地分配各品种的权重，把组合层面的不确定性降到极限，这一理念在现代组合理论里始终占据关键位置。

### **10.7.1 组合优化步骤**

（1）PyPortfolioOpt 是面向组合优化与风险控制的 Python 工具包，内置多套建模与求解方法，可依目标与约束自动给出最优配比。下面是安装该库的命令。
[PRE2]
（2）代码先载入 EfficientFrontier 与 risk_models 两个核心模块，前者负责求解，后者负责风险建模。随后从历史交易数据里筛出不重复的股票代码和交易日期，再打印开头几行，快速核对数据形态。
[PRE3]
执行后会输出以下结果。
[PRE4][PRE5]
（3）这段逻辑用来估算最小方差组合在各期的市值。做法是逐日滚动，先取当日与下一交易日的数据算出收益率协方差，再喂给 EfficientFrontier 求解，权重限制在 0 到 0.1 之间，调用 min_volatility() 得到原始权重。清洗权重后，依据当期资金算出每只股票的买入金额与股数，再用下一期价格推算组合总值，全部写入结果表。
[PRE6][PRE7]
执行后会输出以下结果。
[PRE8]
（4）把 A2C 策略、最小方差组合和基准的逐日收益率累积起来，就能看到各自从起点开始的累计增值轨迹，便于横向对比不同方法的表现。
[PRE9]

### **10.7.2 三策略收益可视化对比**

本节借助 Plotly 把深度强化学习策略、最小方差组合以及道琼斯工业平均指数的累计收益画在一起，直观对比走势。

（1）先用下面命令安装 Plotly。
[PRE10]
（2）用 Plotly 生成多条散点轨迹，每条代表一种策略的累计收益。将它们叠加到同一坐标系，以日期为横轴、累计收益为纵轴，即可形成三策略的对比图。
[PRE11]
其中 trace0_portfolio 对应 A2C 组合的累计收益，trace1_portfolio 对应 DJIA 基准，trace2_portfolio 对应最小方差组合。图上能清晰看出三者在各时点的强弱变化。
（3）随后实例化 fig，把三条轨迹全部 add_trace 进去，再通过 update_layout 调整标题、轴标签、图例位置和背景等，让图表更易读。
[PRE12][PRE13]
这里 add_trace 负责把散点加入画布，update_layout 负责整体排版，show 最终渲染出图。

总的来说，这段代码生成一张三条累计收益曲线的综合对照图（见图 10-6），用视觉化方式帮助评估各策略的绩效。
[PRE14]
图 10-6 三种投资策略累积收益曲线的综合对比图

# **第十一章**

# **仿 Manus 开源智能体平台**

本章的主角是 OpenManus，一个开放源代码的 AI Agent 平台，目标是让开发者更容易搭建通用智能体。项目采用多智能体协作加容器化部署的思路，能自动处理旅行规划、数据分析等复杂流程。模块化集成浏览器、代码执行等能力，提升了扩展性与适应性。交互方式覆盖 CLI、API 和 Web UI，同时欢迎社区共同迭代。

## **11.1 成长背景**

随着大模型能力快速提升，对能一站式解决复杂问题的 AI 助手需求也在攀升。现实场景越来越复杂，技术也在不断突破，AI 正从单一任务走向多任务融合。通用 AI Agent 试图把各种技能整合起来，已成为当前研发的热点方向。

正是在这种趋势下，OpenManus 诞生。项目灵感源自 Monica 团队的 Manus AI，后者以自主完成复杂任务闻名，比如定制旅行方案、深度解构股市趋势等。OpenManus 秉承开源精神，以 Docker、Python、JavaScript 为底座，搭建模块化容器框架，为开发者提供高效灵活的多智能体实验平台。

它的野心不止于复现，更想推动通用 Agent 的普及。通过开源协作聚集全球力量，不断丰富功能。从精细旅游安排到数据深度挖掘，再到个性化内容创作，应用边界在持续扩展。未来随着更强大的语言模型接入、工具链完善和社区贡献加码，系统有望在智能化、人机协作层面发挥更大作用，为各行各业转型提供支撑，逐步形成稳定高效的通用 Agent 生态。

## **11.2 核心功能概览**

系统以 Docker、Python、JavaScript 为技术栈，提供模块化容器框架，方便构建、部署和试验多智能体。整体功能结构见图 11-1。
[PRE15]
图 11-1 本项目的主要功能模块
[PRE16]
图 11-1 本项目的主要功能模块（续）

## **11.3 配置体系**

config 目录负责核心配置，通过读取环境变量和多类大模型参数，为各类 Agent 提供灵活的资源调度。同时还包含 Chrome 实例配置，保证任务执行时可动态调整运行环境。

### **11.3.1 环境配置**

使用 dotenv 读取 .env，配置推理型、基础型、视觉型 LLM 以及 Azure OpenAI，同时设定 Chrome 路径、无头模式和 Agent 服务等参数，方便在项目中统一调用。
[PRE17][PRE18]
### **11.3.2 Agent 与 LLM 的映射**

agents.py 负责 Agent 与 LLM 的对应关系。借助 LLMType 枚举，定义了 basic、reasoning、vision 三种模型类型，再用 AGENT_LLM_MAP 为每个 Agent 设定默认模型。比如 coordinator、supervisor 用基础模型，planner 用推理模型，browser 用视觉模型。这样的映射让多智能体协作更灵活，各自按需选用合适模型。
[PRE19][PRE20]

## **11.4 工具模块**

tools 目录收纳了一批扩展能力工具，覆盖 Bash 执行、Python 运行、网页爬取和浏览器操作等场景。接口清晰、逻辑明确，让 Agent 能高效完成复杂任务，提升系统灵活性。

### **11.4.1 Bash 命令工具**

bash_tool.py 提供了 bash_tool 函数，内部用 subprocess.run 执行命令，捕获标准输出与错误，并通过日志记录执行状态。
[PRE21][PRE22]
### **11.4.2 Python 代码执行工具**

python_repl.py 里的 python_repl_tool 基于 PythonREPL 运行代码，收集结果或异常并记录日志。执行后可用 print 查看输出，适合数据分析和计算场景。
[PRE23][PRE24]
### **11.4.3 爬虫工具**

crawl.py 的 crawl_tool 利用 Crawler 实例抓取指定 URL，并把内容转成 Markdown 文本。过程均有日志记录，成功返回内容字典，失败返回错误信息。
[PRE25]
### **11.4.4 浏览器工具**

browser.py 定义了 BrowserTool 类，继承 BaseTool，提供同步 _run 与异步 _arun 方法，当前均返回占位符字符串，并实例化 browser_tool 供外部使用。
[PRE26][PRE27]

## **11.5 语言模型模块**

llms 目录下的 llm.py 实现了 get_llm_by_type，根据模型类型返回对应实例。函数读取环境变量中的模型名、基础 URL、API Key 和 Azure 部署名等，最后返回 PlaceholderLLM 作为调用占位符，实现灵活的模型管理。
[PRE28][PRE29]

## **11.6 Agent 模块**

agents 目录是核心，存放各种 Agent 实现及节点逻辑。根目录文件定义不同功能 Agent，均调用对应 LLM 处理任务；nodes 子目录封装具体执行节点，保证工作流按序推进。

### **11.6.1 Agent 节点**

nodes 目录包含多个执行节点，每个节点对应特定任务逻辑，调用相应 Agent 与工具处理输入状态并生成下一步指令。整体构成工作流框架，支持任务分发与协作。

（1）browser_node.py 中的 browser_node 是浏览器 Agent 的执行节点，调用 browser_agent.invoke 完成浏览任务，对返回的最后一条消息做 JSON 修复，然后打包成 Command，指定下一步跳转如 supervisor，全程记录日志便于调试。
[PRE30]
（2）coder_node.py 的 coder_node 负责运行 Python 代码，调用 coder_agent.invoke 后修复 JSON，再封装为 Command 并指向下一步如 supervisor。
[PRE31]
（3）coordinator_node.py 的 coordinator_node 是协调 Agent 的交互节点，利用 OpenManusPromptTemplate 生成提示，结合 get_llm_by_type 获取模型处理，并依据回复决定跳转到 planner 或结束。
[PRE32][PRE33]
（4）planner_node.py 的 planner_node 负责生成完整计划，先基于当前状态生成提示，再按配置选用 LLM。开启深度思考时会切到推理模型，需搜索则调用 Bing 并把结果喂入提示。流式输出拼接后修复 JSON，再决定下一步跳转。
[PRE34][PRE35][PRE36]
（5）reporter_node.py 的 reporter_node 是报告 Agent 节点，调用 reporter_agent.invoke 生成报告，修复返回内容的 JSON 后封装为 Command 回跳到 supervisor 决定后续。
[PRE37][PRE38]