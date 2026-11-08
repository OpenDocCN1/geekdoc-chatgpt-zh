[PRE0]

接下来借助已计算好的 IDF，为每一篇文档算出 TF-IDF 值：

[PRE1]

下面给出 TF-IDF 计算的完整实现代码。

### 【程序8-6】

[PRE2]

- 3. 依据训练好的 Word2Vec 向量，为重排后的文档构建一个规模受限的词矩阵
请读者自行实践。

- 4. 把 TF-IDF 计算封装成独立的类
把 TF-IDF 的计算逻辑抽离出来做成一个类，便于之后复用，下面给出相关代码。

### 【程序8-7】

[PRE3]

调用方式示例如下：

[PRE4]

这里的 agnews_text 指从文档里提取出来的正文集合，同样也可以换成标题或整篇文档来处理。

### 8.1.5 基于 TextRank 的文本主题抽取

TextRank 的核心思路借鉴了经典的网页排序算法 PageRank，如图8-13 所示。PageRank 由 Sergey Brin 与 Larry Page 于 1998 年在 WWW7 会议上提出，主要用来解决链接分析中的网页排序问题。在判断网页排名时，通常会认为：

- 被链接的网页越多，排名一般越靠前。
- 权重更高的网页投票更有分量，被高权重网页指向的页面重要性也会随之提升。

TextRank 与 PageRank 原理相近，它把文本拆解到最小单元词汇作为网络节点，构建词汇网络图。在迭代计算词权重时，理论上需要考虑边权，但为了简化，通常默认初始权重相同，并在分配邻居权重时做平均分配，如图8-14 所示。

[PRE5]

图8-13 PageRank算法

[PRE6]

图8-14 TextRank算法

### 1. TextRank 使用前的要点

TextRank 用来从文本中抽取关键词，具体流程如下：

- 1. 将输入文本 T 按完整句切分。
- 2. 对每个句子做分词和词性标注，去掉停用词，只保留名词、动词、形容词等指定词性。
- 3. 构建候选关键词图 G = ( V , E )，其中 V 为节点集合，边的值由词与词之间的相似度决定。
- 4. 按照下式迭代传递节点权重，直到收敛。

[PRE7]

把节点权重从高到低排序，就得到按重要度排列的关键词列表。

### 2. TextRank 类的代码实现

整体实现概括如下。

### 【程序8-8】

[PRE8]

TextRank 是实现关键词抽取的另一种思路。除此之外还有基于相似度聚类等方法。对于本书所用数据集而言，关键词抽取并非必须环节。本节属于选学内容，有兴趣的读者可自行深入。

## 8.1 文本数据预处理

无论是深度学习还是传统 NLP，都需要把自然语言转成机器可读的特征向量。文本预处理正是做这件事，通过分词、词向量训练、特征词抽取这三步，构建能代表文本内容的矩阵表示。

### 8.1.1 AG News 数据集概览与清洗

新闻分类语料 AG 由学术社区 ComeToMyHead 提供，汇聚了 2000 多家新闻源超过 100 万篇报道，可用于分类、聚类、信息检索等非商业研究。在此基础上，Xiang Zhang 抽取了 127600 条样本作为 AG News 数据集，其中 120000 条作训练集，7600 条作测试集，划分为 4 个类别：

- World
- Sports
- Business
- Sci/Tec

该数据集以 CSV 格式存储，打开后的样子如图8-1 所示。

[PRE9]

图8-1 AG_news数据集

第 1 列为新闻类别，第 2 列为新闻标题，第 3 列为新闻正文，文本中用“,”和“.”作断句标记。

由于语料是由社区自动化收集的，难免混入不少杂质，例如：

> Reuters - Was absenteeism a little high\on Tuesday among the guys at the office? EA Sports would like\to think it was because "Madden NFL 2005" came out that day,\and some fans of the football simulation are rabid enough to\take a sick day to play it.

Reuters - A group of technology companies\including Texas Instruments Inc. (TXN.N), STMicroelectronics\(STM.PA) and Broadcom Corp. (BRCM.O), on Thursday said they\will propose a new wireless networking standard up to 10 times\the speed of the current generation.

因此首要工作就是清洗数据，具体步骤如下。

### 1. 数据读取与存储

数据以 CSV 格式保存，需要按列读取，代码如下。