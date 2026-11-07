LangChain 底层还调用了不少 Python 生态的通用组件，比如 pydantic、tenacity、openapi-schema-pydantic 等。它们分别负责数据校验、请求重试、API 规范描述等工作，共同支撑起 LangChain 的完整能力。

Pip 安装 LangChain 时，上述依赖会一并自动拉取，无需单独操作，安装完成即可直接导入使用。若想进一步了解这些组件的原理，建议去看官方文档，它们在数据科学和 AI 领域都有广泛应用。

#### 2.2.3 安装问题排查
用 pip 装 LangChain 大多数情况都很顺滑，但偶尔也会碰到阻碍。下面汇总了高频问题及对应的处理思路。

- (1) 网络连通异常：若出现超时、SSL 校验失败之类的网络错误，可按以下方式排查。

  ① 先确认本地网络是否通畅，能否正常打开 PyPI、GitHub 等站点。

  ② 换成国内镜像源尝试，例如豆瓣、阿里云、清华等，pip 命令加上 -i 参数指定镜像地址。

  ③ 若在代理环境下，需确认 pip 的代理配置是否正确，可通过 --proxy 参数或设置 HTTP_PROXY、HTTPS_PROXY 环境变量来生效。

- (2) 依赖版本打架：安装时若提示某依赖版本不匹配，可尝试以下办法。

  ① 用 pip list 查看当前环境已装包及版本，确认是否存在重复或多版本共存。

  ② 先尝试 pip install --upgrade 将依赖升到最新，或明确指定与 LangChain 兼容的版本进行安装。

  ③ 若仍冲突，推荐新建一个干净的虚拟环境单独安装 LangChain，隔离开其他项目的依赖。

- (3) 编译工具缺失：部分依赖需要 C++ 编译，环境缺失会导致安装失败。此时可按系统补充工具。

  ① Windows 用户可安装 Visual Studio Build Tools 或 MinGW 等编译套件，官网下载即可。

  ② macOS 需装 Xcode 命令行工具，终端执行 xcode-select --install 即可完成。

  ③ Linux 系统建议通过 apt、yum 等包管理器安装 GCC 及相关编译依赖。

- (4) 权限问题：在某些系统上使用 pip 安装全局包需要管理员权限，遇到权限不足时可这样处理。

  ① 使用 pip install --user 命令，将包装到当前用户目录而非系统目录。

  ② 用虚拟环境安装 LangChain，避免全局安装的权限限制。

  ③ 若必须全局安装，请以管理员权限运行 pip 命令，例如 sudo。

其它问题先看报错信息和 traceback，再去 LangChain GitHub 搜索相关 Issue，社区里往往已有答案。若仍未解决，可在 GitHub 新建 Issue 或去官方论坛求助。

### 2.3 搭建高效的 LangChain 开发环境
LangChain 与依赖装好后，下一步就是把开发环境搭起来。环境搭得好，效率提升、冲突减少、代码也更好维护。下面会讲虚拟环境的创建、IDE 项目配置，以及配置文件和环境变量的管理方法。

#### 2.3.1 虚拟环境创建与激活
新建 Python 项目时，业界通用做法是给它一个独立的虚拟环境。这样不同项目的依赖和解释器互不干扰，避免版本冲突和污染全局。用虚拟环境，每个项目都能自由选定 Python 版本和依赖列表。

如图2-3所示，PyCharm 也支持建项目时顺带建虚拟环境。更通用的做法是使用内置的 venv 模块，按以下步骤操作。

- (1) 打开终端，切换到项目根目录。

[PRE0]

图2-3 PyCharm中建立项目的同时建立虚拟环境

- (2) 执行命令创建虚拟环境，把 myenv 换成你喜欢的名字。

[PRE1]

- (3) 创建完成后激活环境，Windows 下执行：

[PRE2]

- (4) 激活成功后终端会显示环境名，如 (myenv)。这时就可以在该环境里安装 LangChain 了。

[PRE3]

除 venv 外，conda、virtualenv 等工具也很常见，它们提供更多定制能力。无论选哪种，为每个项目单独建环境都是好习惯，能让开发更干净、可控。

#### 2.3.2 IDE 项目配置实操
环境准备好后，就可以在 IDE 里建项目并配置了。不同 IDE 操作细节有差异，但核心流程相近：新建项目、指定解释器、管理依赖、设置代码风格与检查等。下面以常见 IDE 为例说明。

在PyCharm中配置LangChain项目：

- (1) 启动 PyCharm，选择 File → New Project。

- (2) 指定项目路径，并勾选 New environment using Virtualenv。

- (3) 选择 Python 解释器版本，并可勾选 Inherit global site-packages。

- (4) 点击 Create，等待项目初始化完成。

- (5) 在 PyCharm 终端激活环境，执行 pip install langchain 安装框架。

- (6) 新建 Python 文件，导入 LangChain 做一次简单测试。

无论用哪款 IDE，都建议坚持好习惯：使用虚拟环境、锁定依赖版本、遵循 PEP 8 规范。代码会更规范、可读、可维护，也更少出错。

#### 2.3.3 配置文件与环境变量管理
除了 IDE 配置，LangChain 本身也支持配置文件和环境变量来定制行为。合理使用能让应用更灵活高效。

LangChain 的核心配置文件是项目根目录下的 .env，用于存放 API Key、数据库连接串等敏感信息。把密钥放在 .env 而非代码里，可避免误提交到版本控制，提升安全性。

以下是一个示例的.env文件。

[PRE4]

代码里可用 python-dotenv 加载 .env，再通过 os.getenv 获取对应值。

[PRE5]

除 .env 外，也可直接用环境变量配置，例如设置 OPENAI_API_KEY 来指定 OpenAI 密钥，无需改动代码即可切换配置。

要设置环境变量，可以在终端中使用 export 命令（macOS/Linux）或 set 命令（Windows）。

- (1) 在macOS/Linux上：

[PRE6]

- (2) 在Windows上：

[PRE7]

配置完成后 LangChain 会自动读取，无需手动加载。

此外框架还提供全局配置对象如 langchain.llm_cache 和 langchain.embeddings_cache，用来调节 LLM 与 Embedding 的缓存策略，可设置最大缓存数、过期时间等。

[PRE8]

综合使用配置文件、环境变量和全局对象，能让项目更灵活、部署更便捷。实际开发中按需选择合适的方案即可。

搭建合格的开发环境涉及虚拟环境、IDE、依赖管理以及框架特有的配置。虽然前期步骤看似繁琐，但对效率、质量和可维护性至关重要。掌握以上要点，就能为后续项目打下坚实基础。

### 2.4 跑通你的第一个 LangChain 程序
环境就绪后，我们来写第一个 LangChain 程序。通过一个基于本地 PDF 的问答 Demo，能直观感受框架的用法与开发流程。示例会用 Ollama 模型和嵌入处理文档问答，并用 Streamlit 提供交互式网页，问题输入、答案展示以及对话历史都会实时呈现。

#### 2.4.1 实现流程拆解
整体实现可拆解为以下几个步骤。

- (1) 安装依赖：langchain 核心库、langchain_community 社区扩展、Streamlit 网页框架、PyPDF2 文档解析库。

- (2) 加载并处理数据。

  - 使用 PyPDFLoader 读取指定的本地 PDF 文档。

  - 使用 RecursiveCharacterTextSplitter 将文档内容分割成多个文本块。

- (3) 创建 Embeddings 和向量存储。

  - 使用 OllamaEmbeddings 计算每个文本块的向量表示。

  - 使用 Chroma 将向量存储起来，构建向量索引。

- (4) 创建问答链。

  - 使用 RetrievalQA.from_chain_type 创建一个基于 stuff 策略的检索式问答链。

  - 使用 OllamaLLM 加载本地的 GEMMA 模型作为 LLM。

  - 使用 Chroma 向量存储作为知识检索器。

- (5) 创建 Streamlit 应用。

  - 使用 Streamlit 创建一个 Web 应用，包含问答界面和对话历史。

  - 用户可以在文本框中输入问题。

  - 程序使用问答链生成答案，并将问答记录添加到对话历史中显示。

- (6) 运行 Streamlit 应用。

  - 使用 streamlit run 命令运行 Streamlit 应用。

  - 在浏览器中访问应用的 URL，进行交互式问答。

#### 2.4.2 运行效果与解读
具体程序代码如下。

【例2-1】 第一个LangChain程序（参见代码文件2.1.py）。

[PRE9]

执行结果如图2-4所示。该应用基于本地 PDF 实现问答，结合 LangChain 与 Ollama。首先用 PyPDFLoader 读取 linux.pdf，再用 RecursiveCharacterTextSplitter 切分为小块，随后通过 OllamaEmbeddings 生成向量并存入 Chroma 构建检索索引。最后用 RetrievalQA 结合基于 gemma:2b 的 OllamaLLM 和 Chroma 检索器生成答案。

界面由 Streamlit 构建，支持输入问题、展示答案并保留对话历史。问题提交后经问答链处理，结果实时呈现，历史记录保存在 Streamlit 会话状态中，刷新页面后仍可保留。

#### 2.4.3 关键代码要点
为更好理解，下面拆解核心代码片段。

(1) 导入所需的库。

os: 用于与操作系统交互。

langchain.chains.RetrievalQA: 用于创建检索问答链。

langchain.document_loaders.PyPDFLoader: 用于加载PDF文档。