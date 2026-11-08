[PRE0]

输出效果见图8-2。

[PRE1]

图8-2 AG_news数据集中的数据形式

train 文件里每行默认按逗号切分，字段会按顺序落到不同位置。为方便后续分类，可以把数据按标签拆到各自的数组里。虽说 Pandas 也能处理，但考虑到后续计算效率，这里更倾向用 Python 内置方法配合 NumPy 来完成。

【程序8-2】

[PRE2]

可见不同类别的内容被分别放到不同的数组中，同时全部转为小写，保证后续处理时保持统一。

### 2. 文本清洗

原始文本里除了普通标点，还混杂了不少特殊符号，这些会干扰建模，所以必须先做清洗。

常用做法是用正则把不在 a-z、A-Z、0-9 范围内的字符全部替换成空格，这样不用逐个列出标点，示例代码如下：

[PRE3]

这里的 re 是 Python 的正则模块，模式里的 ^ 表示取反，也就是只保留指定的字符，其余全部替换掉。要注意，替换后容易出现多余空格或首尾残留空格，这些同样会影响读取，所以还要做一次二次整理。

【程序8-3】

[PRE4]

引入清洗函数后，读取阶段就能直接调用自定义方法对文本做处理并存下来，示例代码如下：

【程序8-4】

[PRE5]

借助额外库和 NumPy 的函数，数据会变得干净很多，效果见图8-3。

[PRE6]

图8-3 清理后的AG_news数据

### 8.1.2 停用词过滤

把文本分词后会发现，除了动词名词等有实际语义的词，还有大量高频但信息量低的词，像 is、are、the 这类。它们重复度高却不贡献太多语义，反而会拖慢词向量分析。所以需要先把停用词去掉，常用的工具是 NLTK，安装语句如下：

[PRE7]

单纯安装 NLTK 并不够，还得单独下载停用词语料包。可以进入交互环境执行下图8-4所示命令，打开下载控制台。

[PRE8]

图8-4 安装NLTK并打开控制台

控制台如图8-5所示。

| Identifier | Name | Size | Status |

|---|---|---|---|

| sentiwordnet | SentiWordNet | 4.5 MB | not installed |

| shakespeare | Shakespeare XML Corpus Sample | 464.3 KB | not installed |

| sinica_treebank | Sinica Treebank Corpus Sample | 878.2 KB | not installed |

| smultron | SMULTRON Corpus Sample | 162.3 KB | not installed |

| state_union | C-Span State of the Union Address Corpus | 789.8 KB | not installed |

| stopwords | Stopwords Corpus | 17.1 KB | not installed |

| subjectivity | Subjectivity Dataset v1.0 | 509.4 KB | not installed |

| swadesh | Swadesh Wordlists | 22.3 KB | not installed |

| switchboard | Switchboard Corpus Sample | 772.6 KB | not installed |

| timit | TIMIT Corpus Sample | 21.2 MB | not installed |

| toolbox | Toolbox Sample Files | 244.7 KB | not installed |

| treebank | Penn Treebank Sample | 1.7 MB | not installed |

| twitter_samples | Twitter Samples | 15.3 MB | not installed |

| udhr | Universal Declaration of Human Rights Corpus | 1.1 KB | not installed |

| udhr2 | Universal Declaration of Human Rights Corpus (Unico... | 1.6 KB | not installed |

| unicode_samples | Unicode Samples | 1.2 KB | not installed |

图8-5 NLTK控制台

在 Corpora 标签页找到 stopwords，点击 Download 完成下载。下载完后可用以下方式验证是否成功：

[PRE9]

stoplist 会把停用词拉到一个列表里，打印结果如图8-6。

[PRE10]

图8-6 停用词数据

接下来把停用词表接入清洗流程。英文单词形态变化多，常见后缀如 ing、ed 会变化，ies 也常换成 y，虽然词干可能不完整，但把同族词归一到同一词根即可。NLTK 提供了 PorterStemmer 来做词干还原，函数示例如下：

[PRE11]

完整实现如下：

[PRE12]

处理完后的样例如图8-7。

[PRE13]

图8-7 生成的数据

对比前后可以看出，数据明显干净了。下面把常见的清洗步骤汇总一下：

- Tokenization：把句子拆成单词或字符级别存储，text.split 就是典型实现。
- Normalization：将词语正则化，lower 和 PorterStemmer 分别负责转小写和词干还原。
- Rare Word Replacement：对低频词做替换，常用做法是把词频小于5的词换成特殊 Token <UNK>，可降噪并压缩词表。
- Add <BOS> <EOS>：为每个句子加上开始和结束标识。
- Long Sentence Cut-Off or Short Sentence Padding：过长句子截断，过短句子补齐。

受限于模型需求，本次并未完整执行全部步骤，具体项目可自行取舍。

### 8.1.3 Word2Vec 词向量训练入门

Word2Vec 是 Google 于 2013 年开源的 NLP 工具，核心思想是把词汇映射成稠密向量，从而能量化词与词之间的关系，挖掘语义关联。其原理图见图8-8。

[PRE14]

图8-8 Word2Vec模型

词向量化并非 Word2Vec 首创。早期的表示方式是 One-Hot，维度等于词表大小，对应词位置置 1，其余为 0。

举例来说，词表有5个词，Queen 排第2，则其向量为(0,1,0,0,0)；Woman 排第4，则为(0,0,0,1,0)。这种编码常称为 1-of-N 或 One-Hot。

One-Hot 实现简单，但缺点明显。词表动辄百万级，用百万维向量表示每个词不现实；向量极度稀疏，只有1个1其余全0，信息密度低，在 CNN 等模型中也容易导致收敛困难。

Word2Vec 的目的就是把稀疏的 One-Hot 压缩成低维稠密向量，通过训练让每个词落在共享的向量空间里，再用统计方法衡量词间相似度。

训练范式主要有两种：CBOW 和 Skip-Gram。

CBOW 是一个三层网络，输入上下文词，预测中心词，示意图见图8-9。

Skip-Gram 则反过来，以中心词预测周围上下文，见图8-10。

[PRE15]

图8-9 CBOW模型# INPUT

# PROJECTION

# OUTPUT

[PRE16]

# Skip-Gram

具体训练细节本文不展开，下面聚焦如何用现成工具快速训练并使用 Word2Vec。

其中最便捷的是用 Gensim 包。

## 1. 训练Word2Vec模型

训练代码很简洁：

[PRE17]

先导入 Gensim，然后调用 Word2Vec。核心参数说明如下：

[PRE18]

其中 sentences 为训练语料，worker 控制并行线程数，size 指定向量维度，min_count 设定最低词频，window 为上下文窗口大小，sample 用于高频词下采样，iter 代表迭代次数。无特殊需求时保持默认即可。

训练完成后可用 save 保存模型以便后续调用。

## 2. Word2Vec模型的使用

使用也十分直接，示例代码如下：

[PRE19]

其中 text 为待转换文本，同样先走 text_clear 清洗流程，再用已训练模型映射。转换结果如下：