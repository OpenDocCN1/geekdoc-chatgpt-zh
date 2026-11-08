> 注：如果想要进一步了解 Agently 如何为你提供模型请求控制能力增强，请访问 Agently 框架官网阅读相关教程：https://agently.tech/guides/agentic_request/guide.html。

##### 2. 特性：代码级工作流开发更清晰好上手

回看前面让模型用数据库记事的方案，你会发现带大模型参与的业务流程，和传统工作流的思路非常接近。实际上，绝大多数面向具体场景的智能化方案，都能先画出一张流程图，再把它落到代码里去编排。正是基于这一认知，Agently 框架提供了一套可直接在代码层面操作的工作流开发与管理机制，把开发过程拆成三个核心环节：先定义工作块，再把工作块串联起来，最后执行运行，同时还附带了运行时数据传递的便捷管理手段。

Agently Workflow 的写法十分直观，几乎不用额外讲解，下面这段示例就能体现它的组织方式和书写习惯：

[PRE0]

把这段示例跑起来，当输入为 "This is a workflow" 时，输出会是：

[PRE1]

如果改成输入 123，则得到的结果是：

[PRE2]

> 注：如果想要进一步了解 Agently Workflow 的相关知识，请访问 Agently 框架官网阅读相关教程：https://agently.tech/guides/workflow/。

#### 4.5.3 部署 SQLite MCP 服务器

开发工具到位后，要实现模型调用数据库记事的智能应用，还需要一个模型容易调用的数据库接入点。SQLite 基于本地文件或内存就能搭建 SQL 友好的结构化存储，体积小巧，在 Python 环境里直接用自带的 sqlite3 库即可，无需额外服务。好在 MCP 官方恰好开源了一套 SQLite MCP 服务器，既能从官方 GitHub 仓库拉取安装，也支持通过 Cline 一键安装。

下面我会先说明如何从 MCP 官方仓库下载并使用该服务器，个人更推荐直接用 Cline 安装，体验下智能时代的新型工具接入方式。

想走官方仓库的话，只需打开 MCP 官方 GitHub 主页，进入 servers/src/sqlite 目录，就能看到官方提供的 SQLite MCP 服务器示例，完整链接是 https://github.com/modelcontextprotocol/servers/tree/main/src/sqlite。

把该目录下的全部文件复制到本地工作目录即可。

该服务器建议用 uv 来管理运行，若本地还未安装，可执行 `pip install uv` 进行安装。

安装完成后，就可以用以下配置在 MCP 客户端中接入 SQLite MCP 服务器：

[PRE3]

#### 4.5.4 实战：让模型调用数据库记事的处理逻辑

前期准备都已就绪，下面正式进入让模型使用数据库记事的智能处理逻辑开发。

首先确认已按前期指引在工作环境中执行 `pip install Agently==3.5.1` 安装 Agently 框架，并且已下载好 SQLite MCP 服务器脚本。接着在工作环境中新建一个名为 `note.py` 的文件，并写入如下代码：