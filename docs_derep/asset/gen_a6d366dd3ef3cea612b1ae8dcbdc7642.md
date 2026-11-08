1）信息检索函数：retrieve_documents 目前只是一个简化的检索演示，模拟了从知识库里找资料的流程。实际部署时，这个部分完全可以升级为向量检索库或关系型数据库查询，以拿到更精准、覆盖更广的参考材料。

2）生成回答：generate_response 会先对用户问题做清洗预处理，再调用检索函数拉取相关文档，最后把文档内容作为上下文连同原始问题一起喂给 GPT 模型来组织答案。换句话说，先找相关资料，再把资料和问题一起输入模型生成初稿，并返回优化后的结果。

3）主程序：在主程序里我们设定了一个示例问题“什么是人工智能？”，并通过 `generate_response` 来驱动完整流程。

示例输出：

> 回答：人工智能（AI）是计算机科学的一个分支，旨在创建能够执行通常需要人类智能级别的任务的系统。它包括诸如学习、推理、问题解决、感知和语言理解等能力。通过模拟人类的认知功能，AI 系统能够在各种应用中提供智能化的解决方案，如自动驾驶、语音识别和医疗诊断等。

## 2.4 RAG 系统基准测试工具

要判断一个 RAG 系统好不好用，光靠主观感觉不够，必须借助标准化的测试工具和数据集来量化。本部分会介绍常用的 RAG 评测手段，帮助读者用统一的数据集和评测方法来衡量系统实效。我们重点拆解三大代表性问答集 Natural Questions、SQuAD 和 MS MARCO，并说明它们各自适用的场景。另外还会讲到自动化评测与人工评测两大方向，它们分别从准确率、性能和用户体验角度保证 RAG 系统既高效又可靠。

### 2.4.1 评测数据集

选对评测集是评估的第一步。不同数据集的难度和侧重点不同，适合不同的应用目标。下面这三种数据集在 RAG 研究中被广泛采用。

#### 1. Natural Questions

图 2-3 展示了 Natural Questions，即 NQ 数据集的样例。

NQ 是 Google 开源的大规模开放域问答集，目的在于推动开放问答的研究。该集包含 307,373 条训练样本、7,830 条开发样本和 7,842 条测试样本。每条样本由一个真实的 Google 搜索问题和对应的维基百科页面构成，页面中标注了长答案段落和一个或多个从段落中提炼的短答案。NQ 的难点在于，模型不仅要从段落里抽取答案，还要理解整篇文章的语义并做推理，比传统抽取式问答更复杂。

（1）NQ 数据集的典型特征

- 体量庞大：数十万训练样本，利于大模型训练和评测。
- 真实查询：来源于真实搜索日志，实用性强。
- 答案层次丰富：同时有长答案和短答案，考验抽取与理解。
- 主题多样：覆盖面广，提升模型的泛化能力。

（2）用 NQ 做 RAG 基准测试

在 RAG 场景下，NQ 能全面反映模型在开放域问答中的表现。下面是用 Hugging Face datasets 库加载 NQ 的示例：

[PRE0]

输出示例：

[PRE1]

借助这种方式，可以快速获取并处理 NQ 数据，为 RAG 的训练和评测做准备。

#### 2. SQuAD

SQuAD 即斯坦福问答数据集，是业界常用的经典评测集。图 2-4 展示了部分数据样例。

目前主流的有 SQuAD 1.1 和 SQuAD 2.0，两者挑战不同。

1）SQuAD 1.1：包含十万量级的问题，均来自维基百科文章，每个问题配一段上下文，答案是上下文中的连续片段。很适合检验抽取和理解能力。

2）SQuAD 2.0：在 1.1 基础上额外加入 50,000 个不可在段落中找到答案的问题。除了抽取能力，还要考验模型判断问题是否可答的能力，更贴近真实使用。

以下是一个基于 SQuAD 的 RAG 评估演示。

SQuAD 任务目标明确，适合测 RAG 在信息抽取和理解上的表现。示例代码使用 Hugging Face Transformers 和 datasets，并结合 Qwen/Qwen2.5-7B-Instruct 模型进行评估：

[PRE2]

运行后会得到类似下面的结果，包含单条问答和整体验证集的 Exact Match 与 F1 指标：

问题：What is the name of the repository for the SQuAD dataset?

参考答案：The Stanford Question Answering Dataset (SQuAD)

模型生成的答案：The Stanford Question Answering Dataset (SQuAD)

问题：What are the main components of a transformer model?

参考答案：Attention mechanisms and feed-forward neural networks

模型生成的答案：Attention mechanisms and feed-forward neural networks

Exact Match: 65.42%

F1 Score: 72.58%

这个演示说明了如何用预训练 QA 模型在 SQuAD 上推理。RAG 系统若加入检索模块，能显著提升回答的准确性和上下文契合度。

#### 3. MS MARCO

MS MARCO 是微软发布的机器阅读理解大规模数据集，聚焦真实用户提问与文档检索。图 2-5 说明了数据集构成。

| Question segment | Percentage of question |
| :--- | :--- |
| **Question contains** | |
| YesNo | 7.46% |
| What | 34.96% |
| How | 16.8% |
| Where | 3.46% |
| When | 2.71% |
| Why | 1.67% |
| Who | 3.33% |
| Which | 1.79% |
| Other | 27.83% |
| **Question classification** | |
| Description | 53.12% |
| Numeric | 26.12% |
| Entity | 8.81% |
| Location | 6.17% |
| Person | 5.78% |

图 2-5 MS MARCO 的数据集构成情况

该集来源于必应搜索的真实查询，每个查询对应多个文档片段，答案往往分散在多篇材料中。

（1）MS MARCO 数据集的特点

- 真实用户提问：直接来自必应，具有很强的现实价值。
- 多文档融合：答案可能跨片段，需要整合与推理。
- 规模大：约 100 万训练查询、1 万开发查询、1 万测试查询。
- 文档结构复杂：提升检索和理解难度。

（2）用 MS MARCO 做 RAG 评估

MS MARCO 不仅考查抽取，更考查整合推理能力。以下示例展示如何用 Hugging Face datasets 加载数据，并用预训练句子嵌入模型编码查询与文档，通过余弦相似度检索最相关文档：

[PRE3]

该示例体现了 RAG 中高效检索的关键机制。

### 2.4.2 常用的评测基准与步骤