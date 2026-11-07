| AWS Lambda | AWS 提供的无服务器计算能力，开发者无需关心服务器配置与运维，只需编写并部署代码，平台会自动完成扩缩容、补丁和基础设施管理，明显降低开发负担 |
| Shell (bash) | 给代理开放强大的 bash 执行权限。请注意脱离沙盒环境使用存在安全隐患。LLM 可借此运行任意 shell 命令，典型场景是操作本地文件系统。该工具不支持 Windows 系统 |
| Bearly Code Interpreter | 支持远程执行代码，尤其适配代理在 Microsoft 沙盒中的运行场景，能安全地搭建代码解释功能 |
| Bing Search | 由微软打造并运营的网络搜索引擎 |
| ChatGPT Plugins | ChatGPT 生态的插件集合 |
| Connery Action | 可把单一 Connery Action 嵌入到 LangChain 代理中协同工作 |
| Dall-E Image Generator | OpenAI 开发的文本转图像模型，图像生成表现突出 |
| DuckDuckGo Search | 在常规搜索引擎基础上汇聚各大 Web 2.0 站点内容，核心原则是保护用户隐私，承诺不监控也不留存搜索记录 |
| File System | LangChain 官方提供的本地文件系统交互开箱工具 |
| Google | 涵盖 Google Cloud Text-to-Speech、Google Drive、Google Finance、Google Jobs、Google Lens、Google Places、Google Scholar、Google Search、Google Serper、Google Trends 在内的工具集 |
| Gradio | 一键接入 Hugging Face 空间上千余个 Gradio 应用 |
| GraphQL | 既是 API 查询语言也是执行查询的运行时，为 API 数据提供完整且可理解的描述，让客户端精确取数，有助于 API 演进并支撑强大的开发工具 |
| Human as a tool | 当 AI 代理遇到困境时，可把人类作为辅助工具提供指导和支持 |
| Memorize | 利用无监督学习对 LLM 进行微调以实现信息记忆，需使用支持微调的模型 |
| Python REPL | 复杂计算时让 LLM 生成代码执行比直接给出答案更高效，LangChain 提供简洁的 Python REPL 工具来运行生成代码 |
| Requests | 网络中大量信息 LLM 无法直连访问，LangChain 封装了 Python 请求模块，可接收 URL 并抓取指定地址数据供 LLM 使用 |
| SerpAPI | 提供 Google 与 Bing 的搜索结果接口 |
| SQL Database | 数据库连接的适配器封装，方便与 SQL 数据库交互 |
| Tavily Search | 专为 AI 代理设计，能够快速返回实时、准确、可信的搜索结果 |
| Wikipedia | 由志愿者协作编写的多语言免费在线百科全书 |

#### 11.2.2 内置工具上手演示
下面以一个内置工具为案例展示调用流程。
资源 代码可在本书配套资源 “/Chapter11/Default%20Tools.ipynb” 中查看。

（1）引入所需依赖，代码如下。
[PRE0]

（2）创建内置工具实例，代码如下。
[PRE1]

（3）打印工具名称，代码如下。
[PRE2]

输出以下信息。
[PRE3]

（4）打印内置工具的说明，代码如下。
[PRE4]

输出以下信息。
[PRE5]

（5）获取默认 JSON 模式，代码如下。
[PRE6]

输出以下信息。
[PRE7]

（6）查询工具是否应直接把内容返回给用户，代码如下。
[PRE8]

输出以下信息。
[PRE9]

（7）以字典形式调用内置工具，代码如下。
[PRE10]

若工具仅需一个输入，也可直接用单个字符串调用，代码如下。
[PRE11]

当工具需要多个输入时，单一字符串调用将不可行。

#### 11.2.3 内置工具的个性化定制
定制内置工具时，可以对名称、描述与 JSON 模式进行设定。

> 提示 设置自定义内置工具的 JSON 模式时，必须保证输入与函数定义保持一致，不建议随意修改。

##### 1. 定义内置工具的 JSON 模式
（1）定义 JSON 模式，代码如下。
[PRE12]

（2）读取默认的 JSON 模式，代码如下。
[PRE13]

输出以下信息。
[PRE14]

##### 2. 定义内置工具的名称和描述
（1）指定名称与描述，代码如下。
[PRE15]

（2）读取工具的名称与描述，代码如下。
[PRE16]

输出以下信息。
[PRE17]

### 11.3 自定义工具
构建代理时需要准备一份可用的工具清单，每个工具主要包含以下要素。
- 名称 name，字符串类型，必须项，且在工具集中需唯一
- 描述 description，字符串类型，非必须但强烈建议提供，帮助代理理解用法
- 参数模式 args_schema，Pydantic BaseModel，非必须但建议提供，用于补充信息或校验参数

自定义方式有多种，下面以两个函数为例说明。
- 一个简单的搜索函数，恒定返回字符串 LangChain
- 一个乘法函数，计算两个数字的乘积

二者差异主要在输入参数个数：搜索只需一个输入，乘法需要多个输入。由于不少代理仅支持单输入函数，掌握协同使用方式很重要。实现路径大致相同，具体做法却不完全一致。可选的三类方法为使用 @tool 装饰器、继承 BaseTool 类、使用 StructuredTool 类。

#### 11.3.1 使用 @tool 装饰器
用 @tool 装饰器定义自定义工具是最便捷的途径。

> 提示 默认情况下 @tool 会以函数名作为工具名，也可把字符串作为第一个参数来自定义名称。为完善描述，@tool 会取函数的文档字符串作为工具说明，因此务必为函数编写文档字符串。

具体步骤如下。
（1）导入依赖，代码如下。
[PRE18]

（2）定义工具，代码如下。
[PRE19]

（3）输出信息，代码如下。
[PRE20]

输出以下内容。
[PRE21]

如需更灵活配置，可向 @tool 传入工具名称与 JSON 参数，代码如下。
[PRE22]

输出以下信息。
[PRE23]

#### 11.3.2 继承 BaseTool 类
相较于装饰器方式，通过继承 BaseTool 类定制工具更繁琐，但能实现对工具定义的全面掌控。

资源 源代码见本书配套资源中的 “/Chapter11/CustomTools/Subclass%20BaseTool.ipynb”。

（1）继承 BaseTool 类自定义工具的方法如下。
[PRE24]

（2）实例化 CustomSearchTool 并把实例赋给变量 search，代码如下。
[PRE25]

（3）输出 search 实例的属性，代码如下。
[PRE26]

输出以下信息。
[PRE27]

（4）实例化 CustomCalculatorTool 并把实例赋给变量 multiply，代码如下。
[PRE28]

（5）输出 multiply 实例的属性，代码如下。
[PRE29]

输出以下信息。
[PRE30]

#### 11.3.3 使用 StructuredTool 类
StructuredTool 兼具前两者的优点。
- 相比 @tool 装饰器，功能更丰富
- 相比继承 BaseTool 类，操作更便捷

资源 源代码见本书配套资源中的 “/Chapter11/CustomTools/StructuredTool%20dataclass.ipynb”。

（1）使用 StructuredTool 类自定义工具的方法如下。