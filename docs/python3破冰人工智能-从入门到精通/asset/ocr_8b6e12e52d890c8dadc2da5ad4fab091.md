关键词抽取的任务就是从一段给定的文本中自动抽取出若干有意义的词语或词组。TextRank 算法是利用局部词汇之间关系（共现窗口）对后续关键词进行排序，直接从文本本身抽取，其主要步骤如下。

+   1. 把给定的文本 T 按照完整句子进行分割，即 T = [S1, S2, …, Sm]。
+   2. 对于每个句子 Si ∈ T，进行分词和词性标注处理，并过滤掉停用词，只保留指定词性的单词 Si = [ti,1, ti,2, …, ti,n]，如名词、动词、形容词，即其中 ti,j ∈ Sj 是保留后的候选关键词。
+   3. 构建候选关键词图 G = (V, E)，其中 V 为节点集，由步骤 2 生成的候选关键词组成，然后采用共现关系（co-occurrence）构造任意两点之间的边，两个节点之间存在边仅当它们对应的词汇在长度为 K 的窗口中时共现。K 表示窗口大小，即最多共现 K 个单词。
+   4. 根据原理中的衡量重要性的权重公式，初始化各节点的权重，然后迭代计算各节点的权重，直至收敛。
+   5. 对节点权重进行倒序排序，从而得到最重要的 T 个单词作为候选关键词。
+   6. 由步骤 5 得到最重要的 T 个单词，在原始文本中进行标记，若形成相邻词组，则组合成多词关键词。例如，文本中有句子“Matlab code for plotting ambiguity function”，如果“Matlab”和“code”均属于候选关键词，则组合成“Matlab code”加入关键词序列。

小贴士：
以 PageRank 为基础的算法不仅有在文本中起着重要作用的 TextRank，还有在推荐算法中起着重要作用的 PersonalRank 算法，有兴趣的读者可以整体浏览并学习这 3 个算法，效果更佳。

# 2. 基于 TextRank 关键词抽取

下面基于 TextRank 算法抽取关键词。首先我们来观察一下基于 Jieba 分词的关键词抽取参数。

```
jieba.analyse.textrank(sentence, topK=20, withWeight=False, allowPOS=('ns', 'n', 'vn', 'v'))
```

+   - sentence: 待提取的文本。
+   - topK: 返回关键词个数，默认值为 20。
+   - withWeight: 关键词权重值，默认值为 False。
+   - allowPOS: 指定词性，默认值为空，即不筛选。

# 【例 8-5】基于 TextRank 算法的关键词抽取

输入：

```
keywords1=jieba.analyse.textrank(text)
print(keywords1)
```

输出：