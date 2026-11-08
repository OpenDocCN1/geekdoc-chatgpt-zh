基础环境和本地大模型部署完毕之后，就可以借助 pip 快速装上微软开源的 GraphRAG 框架，具体的安装命令见下。

[PRE0]

##### 2. 新建检索项目目录
为了让每个检索任务的数据与配置文件互不干扰，建议采用独立目录管理。以本例为例，用 ml-ollama 作为项目名创建文件夹，并在内部准备好 input 目录用于存放待索引素材，相关创建命令如下。

[PRE1]

目录创建后的实际呈现效果可参考图5.14。

[PRE2]

##### 3. 导入待索引数据
把需要构建知识索引的原始文件，比如 .txt、.md、.pdf 等，全部放到 input 目录下。这里先上传一份 txt 文本文档作为示例，上传前的文件示意见图5.15，上传完成后的目录状态见图5.16。

[PRE3]

[PRE4]

[PRE5]

##### 4. 一键初始化项目结构
GraphRAG 自带 CLI，可迅速生成标准的项目骨架。执行初始化命令即可。

[PRE6]

执行后，项目根目录下会自动出现 .env、settings.yaml 以及 project 等必要文件和子目录。

##### 5. 调整项目配置参数
（1）编辑 .env 文件。把 GRAPHRAG_API_KEY 的取值改为 ollama，用来声明本项目通过 Ollama 调用本地大模型服务，修改示例如下。

[PRE7]

（2）编辑 settings.yaml 文件。该文件是整套项目的核心配置，需要分别指定 LLM 与 Embedding 的模型名称及接口地址，Ollama 默认监听在 11434 端口。

大语言模型的相关配置代码如下。

[PRE8]

配置完成后的界面效果见图5.17。

[PRE9]

图5.17 大语言模型配置效果

Embedding 模型的配置代码如下。

[PRE10]

设置完毕后的呈现效果见图5.18。

[PRE11]

图5.18 Embedding模型配置效果

#### 5.1.5 为 Ollama 适配 GraphRAG 源码
当前 GraphRAG 默认对接 OpenAI 接口，若要使用本地 Ollama 作为 Embedding 与 Chat 后端，需要手动修改几处源码。源码典型路径为 /root/miniconda3/envs/graphrag-ollama/lib/python3.10/site-packages/graphrag，即 pip 安装后的站点包目录。主要涉及下面三个模块。

##### 1. 调整 llm/openai/openai_embeddings_llm.py
该文件定义了 Embedding 的调用方式，改写为通过 Ollama.Embeddings 完成本地向量化。具体修改代码如下。

[PRE12]

##### 2. 修改 query/llm/oai/embedding.py
此文件控制检索流程中 Embedding 的使用逻辑。需要把原先基于 OpenAI 的异步调用改为调用本地 Ollama.Embeddings 接口，同时去掉与 OpenAI 重试机制相关的依赖。

核心改动集中在 OpenAIEmbedding 类的同步嵌入方法 embed 以及辅助函数 _embed_with_retry，具体代码如下。

[PRE13]

##### 3. 修改 /query/llm/text_utils.py
为了更好地处理长文本分块，对 chunk_text 函数做了精简改动，使其能够按最大 token 数把字符串拆分为多段并以迭代器返回，供后续流程使用。修改后的代码如下。

#### 5.1.6 将 Ollama 接入微软 GraphRAG
完成模型配置、项目初始化与源码改造后，GraphRAG 已完全切换为本地运行模式，结合 Ollama 提供的本地大模型与嵌入模型即可正式开展检索增强生成任务。下面介绍两个关键流程：构建本地向量索引与执行本地问答。

##### 1. 构建本地向量索引
运行下方命令即可对 input 目录下的数据进行语义嵌入并生成图谱。

[PRE14]

执行期间会自动完成以下步骤：
- 读取 input 目录中的全部文本内容；
- 调用 nomic-embed-text 模型生成语义向量；
- 构建实体间的语义关系图谱；
- 将结果写入 output 目录，产出 .parquet 图谱文件与向量数据库。

运行时的控制台效果见图5.19。

[PRE15]

在执行过程中，Ollama 对话界面也能看到模型被调用的实时情况，见图5.20。

[PRE16]

> **注意**
>
> 若所用模型性能不足，索引阶段可能会出现 “Graph has no nodes” 的警告。建议在构建图谱时选用性能较强的模型，对话阶段的模型要求则相对宽松。

##### 2. 执行本地问答流程
本实验中 GraphRAG 支持两种发起本地问答的方式：命令行调用与 Python SDK 编程调用。

（1）命令行调用。执行如下命令。

[PRE17]

输出内容如下。