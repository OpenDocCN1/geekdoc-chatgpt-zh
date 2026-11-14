据库中没有反义词，则返回空列表）。在代码段 [4]中， 看到有四个 “happy” 的Synset。 首先，我们在synsets列表的索引0处获取相应 Synset的Lemmas：

```
```python
In [8]: lemmas = happy.synsets[0].lemmas()

In [9]: lemmas
Out[9]: [Lemma('happy.a.01.happy')]
```
```

lemmas返回一个包含Lemma对象的列表。现在可以检查数据库中是否包含该Lemma的反义词：

```
```python
In [10]: lemmas[0].antonyms()
Out[10]: [Lemma('unhappy.a.01.unhappy')]
```
```

结果是包含反义词的一个Lemmas列表， 可以看到WordNet数据库中有 “happy” 的一个反义词 “unhappy” 。

# 11.2.13 删除停用词

停用词是文本中的常用词， 通常在分析文本之前进行删除， 因为它们通常不能提供有用的信息。 下面显示了NLTK库中的英语停用词表， 该列表由NLTK中stopwords模块的words函数返回：

# NLTK的英语停用词表

```
```python
['a', 'about', 'above', 'after', 'again', 'against', 'ain',
 'all', 'am', 'an', 'and', 'any', 'are', 'aren', "aren't",
 'as', 'at', 'be', 'because', 'been', 'before', 'being',
 'below', 'between', 'both', 'but', 'by', 'can', 'couldn',
 "couldn't", 'd', 'did', 'didn', "didn't", 'do', 'does',
 'doesn', "doesn't", 'doing', 'don', "don't", 'down',
 'during', 'each', 'few', 'for', 'from', 'further', 'had',
 'hadn', "hadn't", 'has', 'hasn', "hasn't", 'have', 'haven',
 "haven't", 'having', 'he', 'her', 'here', 'hers', 'herself',
 'him', 'himself', 'his', 'how', 'i', 'if', 'in', 'into',
 'is', 'isn', "isn't", 'it', "it's", 'its', 'itself', 'just',
 'll', 'm', 'ma', 'me', 'mightn', "mightn't", 'more', 'most',
 'mustn', "mustn't", 'my', 'myself', 'needn', "needn't", 'no',
```
```