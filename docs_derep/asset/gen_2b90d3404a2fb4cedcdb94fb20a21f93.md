[PRE0]

大模型接入目前还没有完全统一的范式，比如 GLM 家族的模型就只能通过官方的 zhipuai 库来调用。

[PRE1]

读取答案的路径都不一样，有的走 completion.choices[0].message.content，有的走 response.choices[0].message，返回结构差异明显。用原生 SDK 直接调大模型时，API 客户端、消息格式、参数写法都要手动维护，换一个模型或服务商，调用代码乃至前后处理都需要重写，Agent 项目里更换底座会带来比较大的工程负担。

在 LangChain 里，模型调用被高度抽象并统一。下面是它封装后的使用方式。

[PRE2]

切换模型只需更换类名和对应的参数，Prompt 组织、消息封装、上下文管理、工具注入等环节保持不动，所有交互都收敛到 llm.invoke(messages) 一条路径上。

#### 8.1.5 B站数据采集与实时处理实战

本项目目标是做一个面向 B 站动态和舆情的 Agent，第一件事就是解决数据源。以结构化方式持续从 bilibili 拉取动态、视频、评论，作为后续语义分析与问答的基础，我们选用了 GitHub 上的开源库 bilibili-api。它是用 Python 实现的 B 站 API 调用工具，覆盖视频、音频、直播、动态、专栏、用户、番剧等多个领域。

安装很简单，执行对应命令即可，建议装开发版。

[PRE3]

随后还需要自行装一个异步请求库，比如 aiohttp、httpx 或 curl_cffi。

[PRE4]

在完整代码里，BiliAgent-main/bilibili_tools/get_bilibili.py 就是基于该开源库获取数据的实现。我们封装了 bilibili_detail_pipeline 函数，负责按关键词从 B 站实时拉取内容，如图 8.1 所示，包含视频信息 Video Information、评论信息 Comment Information 和热度信息 Popularity Information，完成初步结构化，为后续向量化和知识抽取打底。

##### 1. 关键词搜索与分页拉取

先用 search 方法按关键词检索，分页拿到结果。为控制调用频率，代码里会限制页数。相关实现如下。

[PRE5]

把上面代码里加粗的部分跑一下，就能拿到搜索返回，截取一部分见图 8.8，信息量相当丰富。

[PRE6]

图 8.8 search 方法返回的示例片段

##### 2. 评论异步抓取

拿到单个视频的基础信息后，可以继续拉评论。我们预留了 fetch_comments 方法，基于视频 oid 异步获取评论内容，代码如下。

[PRE7]

##### 3. 视频元数据清洗与结构化

检索完成后，对每条视频的核心字段做抽取和清洗，包括标题、作者、分区、链接、播放量、评论数、标签等。标题和描述里常带 HTML 标记，如 <em class="keyword">，会用正则或字符串替换清理掉，保证文本干净可处理。代码示例如下。

[PRE8]

处理完的每条记录会按统一顺序组织成结构化条目，方便后续封装成 LangChain 的 Document 对象做嵌入。

完整的 get_bilibili.py 运行效果见图 8.9。

[PRE9]

#### 8.1.6 数据转换器与 RAG 流程封装

下面进入项目数据层另一个模块，如图 8.10。重点是搭建一个数据转换器，把 bilibili API 拿到的原始信息转成大模型可理解、可检索的向量形式，供 RAG 系统使用。

[PRE10]

图 8.10 BiliAgent 数据转换器示意

在 BiliAgent-main/bili_server/document_loader.py 中，我们实现了 DocumentLoader 类，代码如下。