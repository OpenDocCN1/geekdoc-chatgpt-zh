# RAG 文本分块方法论全解

在构建检索增强系统时，把长文档拆成合适的块是关键一步。下面梳理了目前实务中常用的六种切分思路，覆盖从最粗放到语义感知的全谱系。

- **固定长度切分**：最直观的做法是给定一个字符上限，把大文本均分。单纯按长度切容易把句子、代码片段劈开，语义断裂。为此一般会先用分隔符做粗分，再在相邻块之间保留一定重叠，让上下文能衔接，这样就能避免像 2.1.2 小节里 “太原游乐场” 与 “开门时间” 这种关联被硬切开的情况。小冰追问：既然大模型最终吃的是 token，为什么不直接按 token 切？咖哥解释：分块阶段通常还没做Embedding，文本还没转成 token，真正长度只能用字符或词数粗估，所以没法以 token 为标准定长。
- **递归式切分**：准备一组分隔符，层层递进地细化。先用双换行 `\n\n` 把段落拆开，再用句号、分号、空格等进一步细分。若某段仍超限，就换下一级分隔符继续切，保证每个块既在长度范围内又尽量完整。
- **格式感知切分**：面对 Markdown、LaTeX、HTML 或源码这类结构化文件，可以按文档元素来切。工具会识别标题、章节、脚注、列表、表格等边界，保持结构完整。例如 Markdown 按 `#` 层级拆分，代码则按 `class`、`def` 等关键字拆分。
- **版式感知切分**：PDF、PPT 这类带版式的材料，用 Unstructured 提供的布局策略更有效。它按逻辑单元——段落、表格、图片、代码块——来划分，同时支持调整块大小和合并策略以适配不同需求。
- **语义聚合切分**：先把文本拆成句子，再用 Embedding 计算句间相似度，把语义相近的句子聚成一块。块内主题一致，适合对理解深度有要求的场景。LlamaIndex 的 SemanticSplitterNodeParser 与 LangChain 的 SemanticChunker 都可实现。
- **命题式切分**：借助语言模型把长文分解为若干表达完整思想的独立命题。每个命题都是最小语义单元，便于检索和存储，适合需要精细搜索的场景。

#### 2.1.5 用 ChunkViz 直观预览分块效果
ChunkViz 可以把不同的切分策略在同一界面下可视化，方便调参。图2-7 ChunkViz主界面

[PRE0]

把文本粘到输入框后，可切换不同的 Splitter 也就是不同的分块策略，并设定块大小，实时观察参数变化带来的切分结果，如图2-8所示。图2-8 选择不同的分块器

[PRE1]

### 2.2 固定长度切分实操
下面从最简单的实现开始。固定字符数切分上手快、效率高，是常见起手式。

#### 2.2.1 LangChain 的 CharacterTextSplitter 用法
CharacterTextSplitter 支持按字符、词或 token 粗略切分。

图2-9展示了 CharacterTextSplitter 的处理流程。

图2-9 CharacterTextSplitter的分块流程

[PRE2]

其流程是先按指定分隔符——默认是 `\n\n`，也就是段落空行——把文本拆开，再把片段累加起来检查是否超限，超了就开启新块。该工具还允许设置块间重叠字符，帮助保留跨块关联。

下面演示用 split_documents 方法对文档对象做切分。

[PRE3]

[PRE4]

小雪：split_documents 接收的是 LangChain 的 Document，如果我手头只有纯文本怎么办？
咖哥：可以先用 create_documents 把文本列表转为 Document，再统一切分。

[PRE5]

[PRE6]

小雪：我只想得到纯字符串片段，不想生成 Document，该怎么操作？
咖哥：用 split_text 方法即可，它只返回切分后的字符串，不会产生 Document，自然也就不保留元数据。

[PRE7]

云冈石窟位于中国北部山西省大同市西郊17公里处的武周山南麓，石窟依山开凿，东西绵延1公里。存有主要洞窟45个，

注意，若设定块上限为 1000 字符，默认分隔符为 `\n\n`，而某段本身就有 1200 字符，切分时不会强行截断该段，而是直接输出一个 1200 字符的块并提示：“Created a chunk of size 1200, which is longer than the specified 1000”。这种场景建议改用 RecursiveCharacterTextSplitter，它能更智能地保证块不超限。

#### 2.2.2 LlamaIndex 中配置块大小
LlamaIndex 的 SimpleDirectoryReader 在加载文件时就能同步完成切分，只需提前设置相关全局参数即可。下面是配置参数并进行处理与检索的代码示例。

[PRE8]

云冈石窟位于中国北部山西省大同市西郊17公里处的武周山南麓。

### 2.3 递归分块
咖哥：在 LangChain 里，RecursiveCharacterTextSplitter 是默认的递归切分方案。核心思想是用一组分隔符逐级细化（见图2-10）。初始切分后若块仍过大，就继续用更细的分隔符切，直到满足目标大小。图2-10 递归分块的工作流程

[PRE9]

小冰：相较 CharacterTextSplitter 提升在哪？
咖哥：CharacterTextSplitter 只用一个分隔符，而 RecursiveCharacterTextSplitter 会尝试一个分隔符列表，默认是 ["\n\n", "\n", " ", ""]，优先保留段落、句子、单词的完整性。比如要求 500 字符/块，若段落约 400 字符就按段落切；若段落有几千字符，则递归改用句子切分。

下面是递归切分的代码示例。

[PRE10]

### 2.4 按语言格式智能切分
处理源码时，合理的块边界对检索质量影响很大。RecursiveCharacterTextSplitter 提供了 from_language 方法，按编程语言优化切分。

小冰：什么场景需要专门拆代码块？
咖哥：多团队并行开发、代码评审与版本控制、按模块做性能与内存分析；单元测试粒度控制、Bug 定位与修复、行为调试等，都可以通过 RAG 在海量代码库里精准抽取所需片段。

该工具内部维护了多语言分隔符列表，语言枚举在 langchain_text_splitters.Language 中，支持 "cpp"、"go"、"java"、"kotlin"、"js"、"ts"、"php"、"proto"、"python"、"rst"、"ruby"、"rust"、"scala"、"swift"、"markdown"、"latex"、"html"、"sol"、"csharp"、"cobol"、"c"、"lua"、"perl"、"haskell" 等。

调用 RecursiveCharacterTextSplitter.get_separators_for_language 即可查看某语言的分隔符。

[PRE11]

[PRE12]

下面示例演示对一段 Python 代码的切分。

[PRE13]

切分器会依据语言关键字把类、函数等拆开，保持代码块的完整与语义。LangChain 还有其他基于文件格式的切分工具，需求时可查官网。

### 2.5 基于版式与语义的进阶切分
仅靠换行或空行切分，难以捕捉论文、报告、说明书这类文档的逻辑结构。段落、标题、表格、列表项都有自身语义，需要更智能的拆分——既考虑换行，也依据排版结构如标题、正文、表格、分页等，产出语义更丰富的块。

#### 2.5.1 用 Unstructured 做结构化分块