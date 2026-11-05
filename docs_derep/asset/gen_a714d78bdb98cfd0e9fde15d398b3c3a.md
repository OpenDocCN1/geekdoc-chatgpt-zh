先在终端敲一下 conda --version 来确认是否已经可用。

想要装 Anaconda 的话，整体流程和 Miniconda 几乎一样，唯一区别是下载源要换成 Anaconda 官网提供的安装包。

[PRE0]

图4-18 安装向导指示（Miniconda3 macOS版）

macOS 下进入虚拟环境：

[PRE1]

macOS 下退出虚拟环境：

[PRE2]

## 4. Linux 安装

1. 获取安装脚本
打开 Miniconda 的下载页，挑选对应自己发行版的脚本文件，比如 Miniconda3-latest-Linux-x86_64.sh。

2. 执行脚本
先打开终端，用 cd 进入脚本所在目录，路径因人而异请自行调整。
举例切换到下载目录：输入 cd /home/parallels/Downloads 后执行 bash Miniconda3-latest-Linux-x86_64.sh。

3. 按向导完成配置
安装期间阅读许可协议点同意，然后指定安装位置。

4. 检查是否成功
重新打开一个终端窗口，再次运行 conda --version，看到版本号即表示安装正常。

Anaconda 在 Linux 上的做法大同小异，只需从 Anaconda 下载页拿到适合 Linux 的脚本，用同样的 bash 方法运行即可，安装画面如图4-19。

[PRE3]

图4-19 安装过程（Miniconda3 Linux版）

5. Conda 常用操作
查看已创建的环境列表，可用：

[PRE4]

或者使用：

[PRE5]

新建一个名为 aiagent 的环境，Python 版本固定为 3.11.5：

[PRE6]

激活 aiagent 环境：

[PRE7]

在当前激活的环境里装 numpy：

[PRE8]

列出当前环境里的包：

[PRE9]

更新软件包：

[PRE10]

删除环境：

[PRE11]

Linux 下激活 venv：

[PRE12]

Linux 下退出 venv：

[PRE13]

### 4.4 LangChain 配置

LangChain 是一个模块化框架，主攻智能体类应用的搭建。

核心模块有：
- 模型 Models：支持多种大语言模型，可从嵌入模型中挑选合适的模型。
- 提示 Prompts：负责管理大模型输入。
- 链 Chains：把大模型和其他组件串联起来。
- 索引 Indexes：对接外部数据源。
- 存储 Memory：保存历史对话。
- 智能体 Agents：实现自动化调用外部工具。

本节在 Windows、macOS、Linux 三大主流系统上均可运行，具体测试环境为 Windows 11、MacOS Monterey 12.7.2、Linux Ubuntu 20.04.2。

用 pip 安装 langchain：

[PRE14]

用 Conda 安装：

[PRE15]

langchain-core：提供底层抽象与 LangChain 表达式语言。

[PRE16]

langchain-community：负责第三方生态集成。

[PRE17]

LangChain experimental：收录实验性质代码，多用于研究探索。

[PRE18]

LangChain 模板：可按业务需求定制 Prompt 流程。

LangServe：把可运行的 LangChain 对象或链以 REST API 形式对外提供，基于 FastAPI 构建并用 pydantic 做数据校验，同时自带客户端。JavaScript 侧由 LangChainJS 提供客户端。

完整安装：

[PRE19]

仅装服务器与客户端：

[PRE20]

LangChain CLI：在使用模板和 LangServe 项目时非常顺手。

[PRE21]

LangSmith：SDK 会随 LangChain 自动安装，帮助追踪与评估语言模型和智能体，把原型推向生产。

安装命令：

[PRE22]

LangGraph：把智能体建模为图状结构，用于构建有状态、多角色的 LLM 应用，基于 LangChain 构建并与其协同。它拓展了表达式语言，支持循环式的多步计算和多链协同，设计灵感来自 Pregel 与 Apache Beam，公开接口借鉴 NetworkX，主要作用是为 LLM 请求增加循环。需要特别注意，这并非 DAG 工具，若要做 DAG 请继续用 LangChain。

循环对智能体行为至关重要，可在循环体中调用 LLM 询问下一步动作：

[PRE23]

引用示例：

[PRE24]

#### 4.4.1 开发环境配置

使用 LangChain 时可接入多种模型来构建智能体。开发前在 VS Code 里先引入所需的 Python 库和模型，再编写具体程序，如下所示：

[PRE25]

#### 4.4.2 组件配置

LangChain 各模块职责清晰，按场景引入即可。若选用 GPT 或 ChatGLM 作为驱动，建议用 ENV 格式文件保存模型平台发放的 API Key：

[PRE26]

LangChain 模板是快速搭建可生产化 LLM 应用的最简路径，提供一系列参考架构，覆盖常见用例。模板格式统一，便于借助 LangServe 部署。

安装 langchain-cli：

[PRE27]

新建项目根目录命名为 LangAgent：

[PRE28]

在该根目录下创建两个子文件夹：app 存放 LangServe 相关代码；packages 存放 LLM 或智能体代码，完成后进入目录。

[PRE29]

引入官方模板项目，这里以 openai-functions-agent 为例，该项目基于 OpenAI 函数式智能体调用。

执行 langchain app add openai-functions-agent 会把模板拉到 packages/openai-functions-agent，随后提示是否安装，实质等同 pip install -e packages/openai-functions-agent。确认后会生成路由代码，需加入应用才能使用。生成的代码如下：

[PRE30]

可通过编辑模板进一步改动 packages/openai-functions-agent 中的代码，换模型、换提示、改逻辑均可。上文片段始终要求最终链可按 from openai_functions_agen.chain import chain 导入，所以包结构最好保持相似，或同步更新导入代码。

完成定制后，让 LangServe 能识别该项目，需要修改 app/server.py 并把上述片段添加进去：

[PRE31]

同时也可以接入 LangSmith，实现对 LangChain 应用的追踪、监控与调试。

引用 langsmith：

[PRE32]

借助 LangServe 的 Python 或 JS SDK 与 API 交互，使用方式与普通 Runnable 一致：

[PRE33]

模板成功上线 LangServe 后，可通过运行对应命令启动生产服务：

[PRE34]

提示：若在 VS Code 的 Python 文件中无法导入 LangChain，通常是包冲突导致，建议先卸载再重装以保证环境一致。具体做法：

先列出已装包并检查 LangChain 相关包：

[PRE35]

删除冲突包，以卸载 langchain-openai、langchain-core 为例：

[PRE36]

重新安装 LangChain 相关包：

[PRE37]

#### 4.4.3 测试

retrieval-agent RAG 智能体测试：

[PRE38]

在 server.py 中追加内容：

[PRE39]

配置 LangSmith、OpenAI key 与项目：

[PRE40]

客户端调用服务：

[PRE41]

启动服务：

[PRE42]

### 4.5 Open Interpreter 配置

Open Interpreter 本质上是一个本地 AI 程序员，可以完成人类程序员能做的绝大多数任务，比如生成与编辑图片视频、操控浏览器、做数据分析等。官方主推 GPT-4 作为驱动。当前它本身没有记忆能力，需要借助其他技术实现数据存储。在此基础上演化出了 Claude Code、Gemini CLI 等本地编程智能体。

#### 4.5.1 Python 环境安装

使用前先安装 Python SDK 包并引入解释器：

[PRE43]

#### 4.5.2 开发环境配置

在 VS Code 中创建项目后，可调用 GPT 驱动的开源解释器处理任务，所需 Python 库如下：

[PRE44]

#### 4.5.3 基础配置

配置门槛很低，核心是模型设置。若使用 GPT，只要把 API Key 写进 ENV 格式文件即可：

[PRE45]

功能验证通过后，可以部署到云端或本地平台，例如 E2B 平台或本地 Docker 容器。

#### 4.5.4 测试

下面是一组测试用例，用于检验解释器的各项能力。

数学问题解答。添加代码后在箭头处输入待解问题：

[PRE46]

信息搜索。运行后输入搜索需求：

[PRE47]

文件和文件夹生成：