语言模型的工作原理可以概括为：喂进一段词序列，模型吐出一个概率向量，向量中的每个分量对应词表里的一个词，表示该词在现有序列之后出现的可能性。早期用于提升语音识别和机器翻译准确率的模型里，就已经出现了把词表索引通过一种叫做 LUT 的特殊线性层映射成稠密嵌入向量的做法。经过上百万条文本的训练后，这些向量会保留输入词的有用信息，语义相近的词比如“狗”“猫”会落在向量空间的邻近位置。随着深度学习的发展，顶尖的语言模型几乎都转向了深度神经网络。

2008 到 2011 年间，在 NEC 普林斯顿实验室工作的布雷顿人罗南·科洛伯尔和英国人贾森·韦斯顿对嵌入的思路做了延伸。他们用卷积网络证明系统不仅能猜下一个词，还能完成文本理解与文本分析。这一成果在自然语言处理圈里当初遭遇强烈质疑，两人的论文甚至被拿来调侃。后来证明方向是正确的，2018 年他们获得了 ICML 的时间检验奖，用以表彰近十年 ICML 上最具影响力的文章。^([\[2\]](#part0094.html#note60n)) 如今两人都在脸书从事研发。

2013 年，正在谷歌工作的捷克年轻研究员托马斯·米科洛夫重拾训练语言模型的思路，提出了一个结构极简的方案并命名为 Word2vec。用词嵌入来表征文本的效果非常好，方法迅速扩散。^([\[3\]](#part0094.html#note61n)) 人们开始用它来把握文本的含义或语气。随后托马斯加入脸书，入职不久便与法国研究员彼得·博亚诺夫斯基、爱德华·格雷夫和阿曼德·约林一起推出 FastText 项目，这是一款快速文本分类器，在保留 Word2vec 优点基础上大幅提升性能，扩展到 157 种语言并以开源形式发布，至今全球有数万工程师在使用。^([\[4\]](#part0094.html#note62n))

研究热度在 2014 年底达到一个高点。杰弗里·辛顿的前学生伊利亚·萨茨克威尔在 NeurIPS 上发表了引人注目的论文。^([\[5\]](#part0094.html#note63n)) 他构建了一个规模较大的神经网络完成跨语言翻译，效果略微超过此前所有技术。传统的统计翻译依赖平行语料的计数，比如“see you later”对应“à plus tard”出现了多少次，“bank”对应“banque”或“rive”各多少次，再按语法规则重排。这样的系统在语言差异很大或词序变化的语言对上表现不佳，比如法语对汉语普通话，英语对德语。伊利亚采用的是循环网络，具体是 1997 年由德国研究人员泽普·霍赫赖特和于尔根·施米德胡贝在瑞士提出的 LSTM 长短期记忆结构。^([\[6\]](#part0094.html#note64n)) 他用多层 LSTM 把整句编码成一个向量，再用另一个 LSTM 逐词生成译文，这类任务被称为 seq2seq。缺点是句子稍长 LSTM 就会遗忘开头，且计算开销大难以大规模使用。

2015 年，蒙特利尔约书亚·本吉奥实验室的韩国博士后赵京勋和德国实习生德米特里·巴赫达瑙提出了一个巧妙改进：不把整句压成固定大小向量，而是让网络在翻译时把注意力聚焦到源句的相关片段。以“这所房子有两个浴室，妻子有她自己的，丈夫也有他自己的”为例，法语里代词要与宾语一致，翻译到“而丈夫……”时系统必须判断“his own”该译成 le sien 还是 la sienne。^([\[7\]](#part0094.html#note65n)) 注意力机制让模型聚焦到与“浴室”相关的部分。初期效果就比伊利亚的系统更高效、内存和计算成本更低。^([\[8\]](#part0094.html#note66n)) 数月后，斯坦福克里斯·曼宁实验室在国际机器翻译大赛 WMT 上采用这一思路并夺冠，随后各研发团队纷纷跟进。来自加州 FAIR 的德国研究员迈克尔·奥利团队在此基础上做了增强注意力的卷积网络，在 2019 年 WMT 夺冠。^([\[9\]](#part0094.html#note67n))

2017 年底，谷歌的研究组把大规模注意力引入翻译，命名为 Transformer，并发表了《Attention is all you need》。几个月后，另一篇谷歌论文震动学界，介绍了 BERT，即来自 Transformers 的双向编码表示。命名沿用了首字母缩写的传统，之前西雅图的艾伦人工智能研究所把自己的系统叫 ELMo，源自语言模型的嵌入。Elmo 与 Bert 都是《芝麻街》的角色，研究者借此自娱。

ELMo、BERT 以及 Bengio、Word2vec、FastText 等思路都源自自监督学习。举例到翻译，就是给一个大型的 Transformer 网络输入由文本提取的词序列，随机遮住 10% 到 20% 的词，训练它预测被遮住部分的能力。网络由此学习词义和句法结构。用数十亿句训练后，学到的内部表征足以支撑翻译或理解任务。^([\[10\]](#part0094.html#note68n)) 相关论文 2018 年 10 月先出现在 arXiv^([\[11\]](#part0094.html#note69n))，随后投稿 ICLR，正式发表在 2019 年 5 月，但投稿前数周已被引用超过 600 次，显示出思想传播的速度。

[PRE0]

#### 图 7—4 “seq 2 seq”结构用于自动翻译，BERT、Transformer和多语言BERT

编码器模块设定句子含义的表示。在编码器的输入处，每个单词都由一个向量表示，与每个单词相关的向量都会被学习。编码器在一个叫作“Transformer”的网络中将这些向量与一个复杂系统结构相结合，该复杂结构以向量序列的形式表示意义。解码器一个一个地生成要翻译的单词。为了生成这些单词中的每个单词，它将先前生成的单词以及注意力模块的输出作为输入，该注意力模块使其可以关注输入句子中与其正在生成的单词相对应的部分。

资料来源：迈克尔·奥利

中间的 BERT 结构通过预测先前已被隐藏的输入句子中的单词来学习表示文本。下方的多语言版本学会同时表示一个句子及其翻译，并建立一个独立于语言的内部表示形式。资料来源：纪尧姆·朗普勒和亚历克西斯·科诺^([\[12\]](#part0094.html#note70n))

受 BERT 启发，脸书团队在 2019 年 7 月推出 RoBERTa，即稳健优化的 BERT，命名上的双关传统得以延续。该模型在庞大语料上训练后在 GLUE 通用语言理解评估排行榜上领先。^([\[13\]](#part0094.html#note71n)) 竞争十分激烈，不到一年原始 BERT 跌至第十二位，截至 2019 年 7 月前三名分别是 RoBERTa、XLNet、MT-DNN，第四名才是人类。需要辩证看待的是 GLUE 包含威诺格拉德模式挑战赛^([\[14\]](#part0094.html#note72n))，例如“la sculpture ne rentre pas dans la boîte parce qu’elle est trop grande”与“...parce qu’elle est trop petite”，代词“它”指代雕塑还是盒子需要世界常识。过去最先进系统正确率不足 60%，现在最好接近 90%，仍略低于人类约 95%。

2019 年初，巴黎 FAIR 的两名年轻研究人员提出用于翻译的 BERT 变体 XLM，跨语言模型。做法是同时输入法语和英语句子，让系统预测缺失词，从而学会用英语的“blue”推断法语缺失词是“bleu”。训练后得到与语言无关的通用表征，也能提升译者效率。^([\[15\]](#part0094.html#note73n))

[\[1\]](#part0094.html#note59) Y. Bengio, R. Ducharme, P. Vincent, C. Jauvin, A neural probabilistic language model,Journal of Machine Learning Research, 2003 , 3 ( 6 ), pp. 1137——1155.

[\[2\]](#part0094.html#note60) Ronan Collobert, Jason Weston,A unified architecture for natural lan guage processing:Deep neural networks with multitask learning ,Proceedings of the 25 th International Conference on Machine Learning (ICML‘ 08), ACM, New York, 2008 , pp. 160——167.

[\[3\]](#part0094.html#note61) T. Mikolov, I. Sutskever, K. Chen, G. S. Corrado, J. Dean, Distributed representations of words and phrases and their compositionality ,Advances in Neural Information Processing Systems, 2013.

[\[4\]](#part0094.html#note62) https://fasttext.cc/.

[\[5\]](#part0094.html#note63) Ilya Sutskever, Oriol Vinyals, Quoc V. Le, Sequence to sequence learning with neural networks,Advances in Neural Information Processing Systems, 2016 , pp. 3104 –3112.

[\[6\]](#part0094.html#note64) Sepp Hochreiter, Jürgen Schmidhuber: Long short term memory,Neural Computation, 1997, 9(8), pp. 1735–1780.

[\[7\]](#part0094.html#note65) 法语中表示“他的/她的”的主有代词具有阴阳性之分，阳性形式为 le sien, 阴性形式为 la sienne。使用阳性还是阴性形式取决于所代替的名词的阴阳性。——译者注

[\[8\]](#part0094.html#note66) D. Bahdanau, K. Cho, Y. Bengio, Neural machine translation by jointly learning to align andtranslate, ICLR 2015 , http://arXiv.org/abs/ 1409.0473.

[\[9\]](#part0094.html#note67) Nathan Ng, Kyra Yee, Alexei Baevski, Myle Ott, Michael Auli, Sergey Edunov, Facebook FAIR’S WMT 19 News Translation Task Submission, 2012, artXiv : 1907. 06616.

[\[10\]](#part0094.html#note68) J. Devlin, M.-W. Chang, K. Lee, K. Toutanova, BERT : Pre-training of deep bidirectional transformers for language understanding , https://arxiv.org/abs/1810.04805.

[\[11\]](#part0094.html#note69) arXiv 是由康奈尔大学运营维护的一个非营利性数据库，由于免费，学术研究人员可以在其他会议或期刊没有录用之前，将自己最新的研究成果发布到该平台上，一方面是为了扩大宣传提升自己的影响力，另一方面是为了保护自己的科研成果。因为无论会议和期刊从投出到最终可以检索，都需要长时间的等待，很难保证期间自己的成果不被别人剽窃，arXiv 可以证明论文的原创性。——编者注

[\[12\]](#part0094.html#note70) Alexis Conneau, Guillaume Lample,Cross lingual Language Model Pretraining, 2019 ,arXiv : 1901.07291.

[\[13\]](#part0094.html#note71) https://gluebenchmark.com/leaderboard.

[\[14\]](#part0094.html#note72) “威诺格拉德模式挑战赛”是图灵测试的一个变种，由加拿大多伦多大学的计算机科学家赫克托·莱韦斯克（Hector Levesque）发起，挑战赛的名字是为了向特里·威诺格拉德（Terry Winograd）教授致敬，威诺格拉德是斯坦福大学的一位教授，人工智能领域的开拓者。

[\[15\]](#part0094.html#note73) G. Lample, A. Conneau, Cross-lingual language model pretraining , https://arxiv.org/abs/ 1901.07291（代码：https://github.com/facebookresearch/ XLM）.

6 智能预测

人们喜欢预测经济，例如预测库存，预测产品的需求，预测一只股票或财务价值的演变曲线……在最后一种情况下，信号是很难捕捉的。若非如此，那么所有人都可以预测股市价格，财经新闻的趣味也就大大降低了。

预测能量消耗使得法国电力公司（EDF）或任何一家电力公司可以更好地管理发电厂的发电量，并尽可能有效地分配资源，最大限度地减少损失。具体是如何做到的呢？