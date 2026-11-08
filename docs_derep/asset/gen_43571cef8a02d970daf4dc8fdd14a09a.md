[PRE0]

##### 3. 视频元数据清洗与结构化

拿到搜索结果后，我们会把每条视频的关键信息挑出来做整理和清洗，涵盖标题、UP主、分区、链接、播放量、评论量、标签等字段。特别要注意标题和描述里常夹带的 B站 HTML 标记，比如 <em class="keyword">，会用正则或字符串替换的方式把它抹掉，保证文本干净利落、方便后续处理。相关实现代码如下。

[PRE1]

处理完的视频会按统一的字段顺序排好，形成标准化的条目，方便直接封装成 LangChain 的 Document 对象做向量化。

get_bilibili.py 运行起来长什么样，可以参考图8.9 的输出示例。

[PRE2]

#### 8.1.6 数据转换器搭建与基础 RAG 流程封装

接下来看项目数据层里的另一个关键模块，示意图见图8.10。本部分的核心任务是造一个数据转换器，把 B站 API 回来的原始信息转化为大模型能理解和调用的向量表示，为 RAG 系统提供燃料。

[PRE3]

图8.10 BiliAgent 项目中的数据转换器架构

具体实现位于 BiliAgent-main/bili_server/document_loader.py，里面定义了 DocumentLoader 类，代码如下。

[PRE4]

[PRE5]

[PRE6]

[PRE7]

该类主要承担三类工作。

##### 1. 数据抓取 get_docs

内部调用已封装好的 get_bilibili.bilibili_detail_pipeline，按关键词异步拉取 B站视频内容，并把结果转成 LangChain 标准格式的 Document。每个 Document 同时携带正文和元数据，为后续检索或增强问答准备好素材。

##### 2. 向量存储 create_vector_store

拿到文本后先用 RecursiveCharacterTextSplitter 做智能切分，单段上限 1000 字、重叠 300 字，再交给 OpenAI 的 Embedding 模型生成向量，最后写入本地 FAISS 向量库。传入 store_path 时还能把库持久化到本地。

##### 3. 文档召回 get_retriever

把已建好的向量库包装成 Retriever 对象，用户输入查询词就能实时做向量相似度检索，返回语义最匹配的文档列表，直接接入 RAG 问答链路。

#### 8.1.7 首个 LangChain 生成链的搭建

LangChain 的设计思想就是链式组合，把各种模块当作可复用、可拼装的积木，通过流水线完成复杂任务。以最简形式为例，就能搭出一个专门回答 B站相关问题的生成链。它接收用户提问和上下文，用提示模板组装输入，调用大模型生成答案，并对结果做结构化解析。

完整代码在 BiliAgent-main/bili_server/generate_chain.py，这是项目里第一个可用的生成链，包含三大核心部件。

##### 1. 提示模板 PromptTemplate

这里定义统一的提示模板，明确模型的身份、限制条件、使用方式和输入格式。用户问题与视频描述等上下文会被分别填入 <context> 和 <question> 标签，形成清晰的结构化输入，具体代码如下。