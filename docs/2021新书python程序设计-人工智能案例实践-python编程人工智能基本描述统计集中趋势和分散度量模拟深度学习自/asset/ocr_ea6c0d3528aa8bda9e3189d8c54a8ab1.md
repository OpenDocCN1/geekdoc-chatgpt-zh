如果已将一个TextBlob对象标记为WordList，则可以通过count方法计算列表中指定单词的词频：

```
In [7]: blob.words.count('joy')
Out[7]: 14

In [8]: blob.noun_phrases.count('lady capulet')
Out[8]: 46
```

# 11.2.12 从WordNet中获取单词定义、同义词和反义词

WordNet是普林斯顿大学创建的一个单词数据库，TextBlob库使用NLTK库的WordNet接口，能够查找单词定义，并获取其同义词和反义词。更多相关信息请查看NLTK的WordNet接口文档，见https://www.nltk.org/api/nltk.corpus.reader.html#module-
nltk.corpus.reader.wordnet。

# 获取定义

首先，创建一个Word对象：

```
In [1]: from textblob import Word

In [2]: happy = Word('happy')
```

Word类中的definitions属性会返回WordNet数据库中该单词所有定义的一个列表：

```
In [3]: happy.definitions
Out[3]:
['enjoying or showing or marked by joy or pleasure',
 'marked by good fortune',
 'eagerly disposed to act or to be of service',
 'well expressed and to the point']
```

这个数据库不一定包含给定单词的所有定义。另外，define方法能够将词性作为参数传递，这样就可以获得仅与特定词性匹配的单词定义。

# 获取同义词