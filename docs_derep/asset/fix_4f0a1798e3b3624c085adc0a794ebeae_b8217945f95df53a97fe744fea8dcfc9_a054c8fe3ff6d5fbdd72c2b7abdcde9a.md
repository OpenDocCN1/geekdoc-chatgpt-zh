[PRE0]
引入额外依赖并调用 NumPy 的计算函数后，语料清洗效果更明显，处理完的数据会更干净，参考示意图见图8-3。

[PRE1]
图8-3 清理后的AG_news数据

### 8.1.2 停用词剔除实践
对已分词的语料做整体浏览会发现，除了承载语义的名词、动词之外，还存在大量功能性虚词，像 is、are、the 这类。它们对表达贡献有限，却因高频出现干扰后续的词向量建模。为压缩词表规模、降低流程复杂度，需要把这类无意义高频词去掉。业界常用 NLTK 库来完成该任务，安装示例如下：

[PRE2]
- 3. 依据已训练的 Word2Vec 模型，为重排后的文档生成规模受限的词矩阵
练习留给读者自行完成。
- 4. 为 TF-IDF 单独封装一个类
把 TF-IDF 的计算逻辑收进独立类，便于后续调用，示例代码如下。
> 【程序8-7】
[PRE3]
调用方式如下：
[PRE4]
其中 agnews_text 代表从文档中抽取的主体文本，标题或整篇文档同样适用。

### 8.1.5 主题抽取：TextRank 方法
TextRank 的设计灵感来自经典的网页排序算法 PageRank，示意图见图8-13。PageRank 由 Sergey Brin 和 Larry Page 于 1998 年在 WWW7 会议上提出，主要解决链接分析中的网页排序问题。对网页排名的直觉是：
- 被引用的来源越多，排名通常越靠前。
- 排名靠前的页面投票权重更大，被高排名页面链接的页面重要性也会随之提升。
TextRank 与 PageRank 思路相近，它把文本拆解为最小单元即词汇作为图节点，构建词汇网络。迭代求权重时理论上需计算边权，为简化运算通常假设初始权重相同，并在相邻词之间均分权重，如图8-14 所示。

[PRE5]
图8-13 PageRank算法

[PRE6]
图8-14 TextRank算法

### 1. TextRank 使用说明
TextRank 主要用于关键词抽取，流程如下：
- 1. 先把输入文本 T 按完整句子切分。
- 2. 对每个句子做分词与词性标注，剔除停用词后仅保留目标词性，如名词、动词、形容词等。
- 3. 构建候选关键词图 G = ( V , E )，其中 V 为节点集合，边由词间相似度决定。
- 4. 依据下面公式迭代传播节点权重，直至收敛。
$WS(V_i) = (1-d) + d \times \sum_{V_j \in In(V_i)} \frac{w_{ji}}{\sum_{V_k \in Out(V_j)} w_{jk}} WS(V_j)$
按权重从高到低排序节点，即可得到重要性递减的关键词列表。

### 2. TextRank 类实现
整体实现示例如下。
> 【程序8-8】
[PRE7]
TextRank 是一种可行的关键词抽取方案。除此之外还有基于相似度聚类的思路等。对于本书所用的数据集，文本抽取并非必需。本节属选学内容，有兴趣可自行深入。
仅安装 NLTK 并不够使用停用词，还需额外下载停用词资源包，建议通过控制台进入 NLTK，随后执行如图8-4 所示的代码，打开下载界面。

[PRE8]
## 图8-4 安装NLTK并打开控制台
控制台界面如图8-5所示。

| Identifier | Name | Size | Status |
|---|---|---|---|
| sentiwordnet | SentiWordNet | 4.5 MB | not installed |
| shakespeare | Shakespeare XML Corpus Sample | 464.3 KB | not installed |
| sinica_treebank | Sinica Treebank Corpus Sample | 878.2 KB | not installed |
| smultron | SMULTRON Corpus Sample | 162.3 KB | not installed |
| state_union | C-Span State of the Union Address Corpus | 789.8 KB | not installed |
| stopwords | Stopwords Corpus | 17.1 KB | not installed |
| subjectivity | Subjectivity Dataset v1.0 | 509.6 KB | not installed |
| swadesh | Swadesh Wordlists | 22.3 KB | not installed |
| switchboard | Switchboard Corpus Sample | 772.6 KB | not installed |
| timit | TIMIT Corpus Sample | 21.2 MB | not installed |
| toolbox | Toolbox Sample Files | 244.7 KB | not installed |
| treebank | Penn Treebank Sample | 1.7 MB | not installed |
| twitter_samples | Twitter Samples | 15.3 MB | not installed |
| udhr | Universal Declaration of Human Rights Corpus | 1.1 KB | not installed |
| udhr2 | Universal Declaration of Human Rights Corpus (Unico... | 1.6 KB | not installed |
| unicode_samples | Unicode Samples | 1.2 KB | not installed |

## 图8-5 NLTK控制台
切换到 Corpora 栏目，找到 stopwords 后点击 Download 完成下载。下载完成后可按如下方式校验：
[PRE9]
stoplist 会把停用词整理成列表形式，输出结果如图8-6所示。