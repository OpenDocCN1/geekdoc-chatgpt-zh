
![](img/57b180d92f84b0150a5b5e4fc7583b76_107_0.png)

![](img/57b180d92f84b0150a5b5e4fc7583b76_107_1.png)

目前，基于注意力机制的生成式文本摘要模型是生成式文本摘要的主流框架，但是序列到序列模型在应用于摘要生成时还存在三个主要问题：1）难以准确复述原文的事实细节；2）无法处理原文中的未登录词（Out of Vocabulary，OOV）；3）生成的摘要中存在重复的片段。针对这三个问题，Abigail See 等人于 2017 年提出了指针-生成器网络（Pointer-Generator Network）以及覆盖率机制（Coverage Mechanism）[20]，近两年的生成式摘要模型大多以这个模型为基础模型，该模型的结构如图 3-20 所示。

指针-生成器网络是基于序列到序列模型和指针网络（Pointer Network）的混合模型，一方面通过序列到序列模型保持抽象生成的能力，另一方面通过指针网络直接从原文中取词，提高摘要的准确度和缓解 OOV 问题。在预测的每一步，通过动态计算一个生成概率 p_gen，把二者软性地结合起来。这样，每一步单词的概率分布计算如下：

\( P(\boldsymbol{w})=p_{gen} P_{vocab}(\boldsymbol{w})+(1-p_{gen}) \sum_{i:w_i=w} a_i^t \quad (3-39) \)

其中，\( p_{gen} \) 表示使用序列到序列模型生成该单词的概率，\( P_{vocab}(\boldsymbol{w}) \) 是词汇表中所有单词的概率分布，\( a_i^t \) 是注意力系数。指针-生成器网络相当于在每次摘要生成过程中，都会把原文动态地加入词表中，并且在每一步的预测过程中，相比于单纯的序列到序列模型，选取原文中出现的词作为摘要的概率要更大一些。

![](img/57b180d92f84b0150a5b5e4fc7583b76_108_0.png)

文本生成问题通常面临着重复问题，将用于机器翻译的覆盖机制应用到摘要生成问题上，取得了很好的效果。覆盖机制就是在预测的过程中，维护一个 Coverage 向量。Coverage 向量表示过去每一步预测中注意力分布的累计和，记录着模型已经关注过原文的哪些词并且让这个 Coverage 向量影响当前步的注意力计算，这样就能有效避免模型持续关注到某些特定的词上。

自动文本摘要的评价方法主要有两种：一种是人工评价方法，一种是自动评价方法。人工评价方法虽然简单但是耗时耗力，成本较高，无法应用于大规模自动文本摘要数据的评价。自动评价方法中的主要评价指标是 ROUGE（Recall-Oriented Understudy for Gisting Evaluation）[21]，它是一个指标集合，包括一些衍生指标，最常用的有 ROUGE-n，ROUGE-L。

- 1) ROUGE-n：该指标旨在通过比较生成的摘要和参考摘要的 N-gram（连续 n 个单词）评价摘要的质量，常用的有 ROUGE-1、ROUGE-2 和 ROUGE-3。

- 2) ROUGE-L：不同于 ROUGE-n，该指标基于最长公共子序列（Longest Common Subsequence，LCS）评价摘要。如果生成的摘要和参考摘要的最长公共子序列越长，那么认为生成的摘要质量越高。该指标的不足之处在于它要求 N-gram 一定是连续的。

## 3.5.3 机器阅读理解

机器阅读理解（Machine Reading Comprehension，MRC）任务是一个有监督学习问题，给定一组训练实例 \(\{(p_i,q_i,a_i)\}^n_{i=1}\)，目标是学习一个预测器，它以一段文本 \(p\) 和一个给定的问题 \(q\) 作为输入，然后将答案 \(a\) 作为输出。

\(f(p,q) \to a\) (3-40)

根据答案的类型不同，答案 \(a\) 可以有不同的形式，一般来说，现有的机器阅读理解任务可以分为 4 类：

- 1) 完形填空类型（Cloze Style）：基于文章来猜测哪些词或者实体可以被用来完善句子，答案要么是从一组预先定义好的候选集中选择，要么是从一个完整的词汇表中选择。

- 2) 多项选择类型（Multiple Choice）：正确答案从 \(k\) 个假设答案中选取，给出的假设答案中有一个是正确的。

- 3) 范围预测类型（Span Prediction）：也称为抽取式问答（Extractive Question Answering），答案是给定文章中一定范围内的文字，答案可以被表示成 \((a_{start}, a_{end})\)。

- 4) 自由形式回答类型（Free-Form Answer）：允许答案是任何形式的文本（即任意长度的单词序列）。

机器阅读理解算法主要包含基于规则的方法、基于机器学习的方法与基于深度学习的方法，目前基于深度学习的方法是主流方法，这里主要介绍基于深度学习的方法。

2015 年，DeepMind 公司的研究人员 Karl Moritz Hermann 等人提出了一个面向机器阅读理解任务的基于注意力机制的 LSTM 模型[22]。2016 年，Danqi Chen 等人创建了大规模有监督训练数据集 CNN/Daily Mail[23]，在 CNN/Daily Mail 数据集中，将一篇新闻文章看作一个段落（Passage），通过使用占位符（Placeholder）来替换段落中实体（Entity）的方式将段落转换成一个完形填空问题，而答案就是这个被替换的实体。由于数据集的创建方法问题，CNN/Daily Mail 数据集存在一些噪声，这限制了机器阅读理解方法的进一步研究。

为了解决这些限制，Pranav Rajpurkar 等人构建了一个名为 SQuAD（Stanford Question Answer Dataset）的数据集[24]，该数据集包含了 536 篇文档、107,785 个问答对，每个问题的答案对应文章中的一段文本。SQuAD 数据集是第一个大规模机器阅读理解数据集，由于质量高并可进行在线自动评估，该数据集引起了研究人员的极大关注。截止到 2022 年 12 月，所有在 SQuAD 上表现比较好的模型都建立在深度学习模型之上，这些模型从编码文章和问题开始，然后经过交互层，最后进行答案的预测。

图 3-21 展示了现有大部分机器阅读理解模型的基础结构[25]，主要包含四层。

![](img/57b180d92f84b0150a5b5e4fc7583b76_110_0.png)

- 1) 向量化层：分别将原文和问题中的 Token 映射为向量表示（如可以使用 Word2Vec、Glove 等向量化方法）。

- 2) 编码层：主要使用循环神经网络对原文和问题进行编码，这样编码后每个 Token 的向量表示就蕴含了上下文的语义信息。

- 3) 交互层：主要负责分析问题和原文之间的交互关系，并输出编码了问题语义信息的原文表示，即 Query-Aware 原文表示。

- 4) 答案层：基于 Query-Aware 原文表示来预测答案范围，即答案的开始位置和结束位置。

经典的机器阅读理解模型主要有 R-Net[26]、BiDAF[27]、FastQA[28]、ReasoNet[29] 和 Mnemonic Reader[30] 等，这里主要介绍 R-Net 和 BiDAF 模型。

### 1. R-Net

R-Net[26] 是由 Yuxin Wang 等人提出的机器阅读理解模型。对于该模型，其向量化层使用 Glove 词向量和字符嵌入（Char Embedding）两种方法进行向量化以丰富输入特征。编码层使用循环神经网络对问题和原文进行编码。交互层是一个双层交互结构，其中，第一层使用基于门限注意力的循环神经网络（Gated-Attention Based Recurrent Networks）匹配问题（Question）和段落（Passage），获取问题相关的段落表示（Question-Aware Passage Representation）；第二层基于自匹配注意力机制的循环神经网络将段落和它自己进行匹配，从而实现整个段落的高效编码。答案层使用指针网络定位答案所在的位置。R-Net 的网络结构如图 3-22 所示。

![](img/57b180d92f84b0150a5b5e4fc7583b76_111_0.png)

### 2. BiDAF

BiDAF 是由 Minjoon Seo 等人 [27] 提出的一种经典的机器阅读理解模型，如图 3-23 所示。在该模型中，向量化层首先混合了单词和字符级别的表示，单词级别的词表示使用预训练的词向量 Glove 进行初始化，而字符级别的表示使用 CNN 进行编码。随后，单词级别的表示和字符级别的表示送入一层双向 LSTM 进行编码。交互层引入双向注意力机制，即首先计算一个段落和问题的对齐矩阵，然后基于该矩阵计算 Query2Context 和 Context2Query 两种注意力，并基于两种注意力计算 Query-Aware 的段落表示，接着使用双向 LSTM 进行语义信息的聚合。最后，该模型使用边界模型（Boundary Model）来预测答案的开始位置和结束位置。

对于范围预测类型的机器阅读理解任务，需要将预测的答案与正确答案进行比较，通常使用 Pranav Rajpurkar 等人 [24] 提出的两种评估指标：精确匹配（Exact Match，EM）和 F1 得分。

- 1) EM: 如果预测的答案等于正确答案，则 EM 将分配满分 1 分，否则分配 0 分。

![](img/57b180d92f84b0150a5b5e4fc7583b76_112_0.png)

图 3-23 BiDAF 的网络结构

- 2) F1 得分：计算预测答案和正确答案之间的平均单词重叠，预测答案和正确答案被看作一堆 Token，计算它们得分为

```
F1 = 2×precision×recall/( precision+recall ) (3-41)
```

对于自由形式回答的机器阅读理解任务，目前还没有最理想的评价标准，一种常见的方法是使用自然语言生成任务中的评估指标，如 BLEU、METEOR 和 ROUGE 等。

## 复习题

- 1. 简述循环神经网络的起源与发展。
- 2. 简述循环神经网络的训练过程。
- 3. 长短期记忆网络与经典的循环神经网络的主要区别在哪里，它的优点有哪些？
- 4. 简述 GRU 的主要思想以及与长短期记忆网络的区别。
- 5. 自动文本摘要包含哪两种主要方法？简述它们的主要思想以及优缺点。
- 6. 抽取式问答的基础框架有哪几层？简述它们的主要功能。

## 实验题

- 1. 基于给定的或自行下载的唐诗数据集，使用 RNN 或者 LSTM 设计并实现一个自动写诗的程序，要求程序写出的诗句尽可能满足汉语语法和表达习惯，如给定“湖光秋月两相和”，程序即可输出续写的诗句。要求能够完成数据读取、网络设计、网络构建、模型训练和模型推理等过程。

- 2. 基于给定的或自行下载的电影评论数据集，使用 RNN 或者 LSTM 设计并实现一个电影评论分析程序，能够对给定的电影评论给出正向或者负向的评价。要求能够完成数据读取、网络设计、网络构建、模型训练和模型测试等过程。

- 3. 基于 Penn TreeBank (PTB) 语料库，使用 RNN 或者 LSTM 设计并实现一个语言模型，能够根据输入单词预测下一个单词。要求能够完成数据读取、网络设计、网络构建、模型训练和模型测试等过程。PTB 语料库官方下载地址：https://www.ldc.upenn.edu/。

- 4. 基于 SQuAD 数据集，设计并实现一个机器阅读理解程序，能够支持抽取式问答。要求能够完成数据读取、网络设计、网络构建、模型训练和模型测试等过程。SQuAD 数据集官方下载地址：https://rajpurkar.github.io/SQuAD-explorer/。

## 参考文献

- [1] HOPFIELD J J. Neural networks and physical systems with emergent collective computational abilities [J]. Proceedings of the national academy of sciences of the United States of America, 1982, 79 (8) : 2554-2558.

- [2] JORDAN M I. Serial order: a parallel distributed processing approach [R]. Report 8604, institute for cognitive science, University of California, San Diego, 1986.

- [3] ELMAN J L. Finding structure in time [R]. CRL technical report 8801, center for research in language, University of California, San Diego, 1988.

- [4] WERBOS P J. Backpropagation through time: what it does and how to do it [J]. Proceedings of IEEE, 1990, 78 (10) : 1550-1560.

- [5] HOCHREITER S, SCHMIDHUBER J. Long short-term memory [J]. Neural computation, 1997, 9 (8) : 1735-1780.

- [6] SCHUSTER M, PALIWAL K K. Bidirectional recurrent neural networks [J]. IEEE transactions on signal processing, 1997, 45 (11) : 2673-2681.

- [7] CHUNG J, GULCEHRE C, CHO K, et al. Empirical evaluation of gated recurrent neural networks on sequence modeling [J]. arXiv, preprint arXiv: 1412.3555, 2014.

- [8] GRAVES A, WAYNE G, DANIHELKA I. Neural turing machines [J]. arXiv, preprint arXiv: 1410.5401, 2014.

- [9] JOULIN A, MIKOLOV T. Inferring algorithmic patterns with stack-augmented recurrent nets [C]. Advances in Neural Information Processing Systems 28, 2015: 190-198.

- [10] BRITZ D. Recurrent neural networks tutorial, part 3-backpropagation through time and vanishing gradients [EB/OL]. https://dennybritz.com/posts/wildml/recurrent-neural-networks-tutorial-part-3, 2015-10-08.

- [11] BENGIO Y, DUCHARME R, VINCENT P. A neural probabilistic language model [J]. The journal of machine learning research, 2003, 3: 1137-1155.

- [12] MIKOLOV T, KARAFIAT M, BURGET L, et al. Recurrent neural network based language model [C]. Proceedings of the 11th Annual Conference of the International Speech Communication Association, 2010: 1045-1048.

- [13] MORIN F, BENGIO Y. Hierarchical probabilistic neural network language model [C]. Proceedings of the 10th International Workshop on Artificial Intelligence and Statistics, 2005.

- [14] BROWN P F, PIETRA V J, DESOUZA P V, et al. Class-based n-gram models of natural language [J]. Computational linguistics, 1992, 18 (4): 467-480.

- [15] MIKOLOV T, ZWEIG G. Context dependent recurrent neural network language model [C]. Proceedings of 2012 IEEE Spoken Language Technology Workshop, 2012: 234-239.

- [16] ATHIWARATKUN B, STOKES J W. Malware classification with LSTM and GRU language models and a character-level CNN [C]. Proceedings of IEEE International Conference on Acoustics, Speech and Signal Processing, 2017: 2482-2486.

- [17] MALLICK C, DAS A K, DUTTA M, et al. Graph-based text summarization using modified textrank [C]. Proceedings of International Conference on Soft Computing in Data Analytics, 2018: 137-146.

- [18] EL-KASSAS W S, SALAMA C R, RAFEA A A, et al. Automatic text summarization: a comprehensive survey [J]. Expert systems with applications, 2021, 165.

- [19] CHOPRA S, AULI M, RUSH A M. Abstractive sentence summarization with attentive recurrent neural networks [C]. Proceedings of the 2016 Conference of the North American Chapter of the Association for Computational Linguistics: Human Language Technologies, 2016: 93-98.

- [20] SEE A, LIU P J, MANNING C D. Get to the point: summarization with pointer-generator networks [C]. Proceedings of the 55th Annual Meeting for the Association for Computational Linguistics, 2017: 1073-1083.

- [21] LIN C Y. ROUGE: a package for automatic evaluation of summaries [C]. Proceedings of Text Summarization Branches Out, 2004: 74-81.

- [22] HERMANN K M, KOCISKY T, GREFENSTETTE E. Teaching machines to read and comprehend [C]. Advances in Neural Information Processing Systems 28, 2015: 1693-1701.

- [23] CHEN D, BOLTON J, MANNING C D. A thorough examination of the CNN/Daily mail reading comprehension task [C]. Proceedings of the 54th Annual Meeting of the Association for Computational Linguistics, 2016: 2358-2367.

- [24] RAJPURKAR P, ZHANG J, LOPYREV K, et al. SQuAD: 100,000+ questions for machine comprehension of text [C]. Proceedings of the 2016 Conference on Empirical Methods in Natural Language Processing, 2016: 2383-2392.

- [25] WEISSENBORN D, WIESE G, SEIFFE L. Making neural QA as simple as possible but not simpler [C].
