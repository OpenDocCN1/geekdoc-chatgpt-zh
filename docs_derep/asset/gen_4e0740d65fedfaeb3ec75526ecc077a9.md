[PRE0]python

import csv

agnews_train = csv.reader(open("./dataset/train.csv","r"))

for line in agnews_train:

    print(line)

[PRE1]

## 2. 文本清洗

AG_news 原始文本里除了常规标点，还夹杂大量特殊符号，直接使用会影响后续分析，所以先做清洗。
实际操作里最方便的是用正则表达式，把不在 a-z、A-Z、0-9 这三个区间里的字符统一筛掉并换成空格，这样就不用把每一种标点都列出来，示例如下：
[PRE2]

re 是 Python 提供的正则模块，模式中的“^”起到取反作用，也就是只保留我们指定的字符，其它一律替换。要注意的是，替换完后很容易出现连续空格，或者文本首尾残留空白，这些同样会干扰读取，所以第一轮替换之后还需要再做一次收尾整理。
### 【程序8-3】
[PRE3]

引入新的清洗组件后，读取数据的环节就可以接上自定义函数，先处理再存库，写法如下：
### 【程序8-4】
[PRE4]

这一步借助了额外的库和 NumPy 的处理函数，处理完的语料会明显干净很多，效果见图8-3。
[PRE5]

### 图8-3 清理后的AG_news数据

把语料切词后会发现，除了能表意的主干词，每组文本里都充斥着 is、are、the 这类高频低信息词。它们对语义贡献有限，却因为出现次数太多会干扰词向量建模。因此需要把停用词剔除，以减小词汇量、降低模型复杂度。常用工具是 NLTK，安装方式如下：
[PRE6]

仅安装 NLTK 还无法直接使用停用词表，还需要单独下载停用词语料。建议在终端启动 NLTK，然后运行如图8-4所示的命令，打开下载控制台。
[PRE7]

### 图8-4 安装NLTK并打开控制台

控制台界面如图8-5所示。

| Identifier | Name | Size | Status |

|------------|------|------|--------|

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

| udhr | Universal Declaration of Human Rights Corpus | 1.1 MB | not installed |

| udhr2 | Universal Declaration of Human Rights Corpus (Unico... | 1.6 MB | not installed |

| unicode_samples | Unicode Samples | 1.2 KB | not installed |

### 图8-5 NLTK控制台

切换到 Corpora 标签，找到 stopwords，点击 Download 按钮完成下载。下载完毕后可以用下面方式验证：
[PRE8]

stoplist将停用词获取到一个数组列表中，打印结果如图8-6所示。