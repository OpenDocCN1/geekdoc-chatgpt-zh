[PRE0]

通过 Retriever 来实现测试数据的自动生成。核心思路是借助精心设计的提示词驱动大模型产出所需的测试样本，并且以表格形式呈现，同时生成对应的查询 SQL，比如代码清单 8-18 所展示的那样。

### 代码清单 8-18

[PRE1]

LlamaIndex 里实现的三种大模型 SQL 生成方案，各自在不同场景下更有优势，并不存在一种万能解法，具体选用哪种得看实际问题需求来定。

## 8.6 LlamaIndex的NodeParser

在 LlamaIndex 体系里，NodeParser 承担着解析与重塑检索结果的核心职责。RAG 流程中，检索器会从海量素材里挑出与任务相关的片段，NodeParser 随后对这些片段进行解读并转成模型可直接使用的结构。它会把一批文档拆分成若干 Node，每个 Node 对应文档的一个切片，子节点会完整继承父节点的属性。

### 8.6.1 文档的NodeParser

处理普通文档时，NodeParser 底层依赖 FlatReader 和 SimpleFileNodeParser 来适配多种文本格式，使用者无需手动指定具体解析器，系统会自动匹配合适的实现，示例见代码清单 8-19。

代码清单 8-19

[PRE2]

该解析器的输出效果可参考图 8-2。

[PRE3]

### 8.6.2 HTML的NodeParser

HTML 类型的 NodeParser 基于 Beautiful Soup 进行解析。默认提供了一组可提取的标签集合，用户也可以自定义。内置标签涵盖 p、h1 至 h6、li、b、i、u、section 等，具体用法参见代码清单 8-20。

代码清单 8-20

[PRE4]

解析后的呈现效果见图 8-3。

### 8.6.3 JSON的NodeParser

JSON 文档的解析由 JSONNodeParser 负责，使用方式较为简洁，示例见代码清单 8-21。

[PRE5]

代码清单 8-21

[PRE6]

JSONNodeParser 的解析结果如图 8-4 所示。

[PRE7]

图8-4 JSONNodeParser的解析结果

### 8.6.4 Markdown的NodeParser

解析 Markdown 文件的组件是 MarkdownNodeParser。它能识别标题、段落、列表、超链接、代码块等 Markdown 语法，并转成结构化信息。处理流程先抽取原始文本内容，再解析其中的链接与引用，涉及 URL、链接描述、标题文本的提取等能力，使用示例见代码清单 8-22。

代码清单 8-22

[PRE8]

MarkdownNodeParser 的解析结果如图 8-5 所示。

[PRE9]

### 8.6.5 文档分割

面对长文本，加载后通常要先切分再检索。LlamaIndex 提供了丰富的文档处理手段，分割、合并、过滤等操作都能便捷完成。

- CodeSplitter：官方文档列出了其支持的编程语言范围，涵盖 Erlang、Lua、Elisp、Dockerfile、go-mod、Elixir、Kotlin、Perl、Markdown、YAML、Objective-C、SQL、R、Common Lisp、Bash、C、C#、C++、CSS、Embedded Template、Go、Haskell、HTML、Java、JavaScript、JSDoc、JSON、Julia、OCaml、PHP、Python、QL、Regex、Ruby、Rust、Scala、TOML、T-SQL、TypeScript、HCL、Fortran 等，具体示例见代码清单 8-23。

代码清单 8-23

[PRE10]

CodeSplitter 的结果如图 8-6 所示。

[PRE11]

- SentenceSplitter：SentenceSplitter 按句子边界划分节点，使用示例如代码清单 8-24 所示。

代码清单 8-24

[PRE12]

使用 SentenceSplitter 按照句子划分 Node 后的结果如图 8-7 所示。

[PRE13]

- 其他 Splitter：SentenceWindowNodeParser 会生成单句节点，并在元数据中保留上下文邻句，这类节点不适合直接输入 LLM 或 Embedding 模型。SemanticSplitterNodeParser 不依赖固定块大小，而是依据向量相似度动态决定断点进行切分。TokenTextSplitter 则以每个块的 token 数量为基准进行文档分割。

## 8.7 大模型云服务生成接口测试脚本实战

### 8.7.1 大模型云服务的调用

在本地部署大模型对算力有一定门槛，但这并不意味着只能自建。当前主流模型大多开放了云端调用服务，比如此前提到的讯飞星火、本章用到的智谱清言，百度文心一言、阿里通义千问等同样提供对应接口，甚至 Hugging Face 也支持便捷的云端调用。不同厂商的计费标准各异，选择时应结合实际需求权衡。

本节示例基于智谱清言开放平台的 API 进行调用。智谱清言除了推理模型外，还提供 Embedding 模型。Embedding 在 RAG 架构中至关重要，它把文本映射为向量，通过向量空间的相似度来捕捉语义。若没有 Embedding，RAG 就难以把文本转为可计算的形式。传统基于词袋或精确匹配的方法对语义细微差别不敏感，容易受同义词、近义词、多义词干扰，检索效率和准确率均受限。Embedding 通过向量化保留语义差异，为 RAG 提供了更强的语义表达能力。

若要在 LlamaIndex 中接入智谱清言的云服务，需要按 CustomLLM 的规范对其进行封装，示例见代码清单 8-25。

代码清单 8-25