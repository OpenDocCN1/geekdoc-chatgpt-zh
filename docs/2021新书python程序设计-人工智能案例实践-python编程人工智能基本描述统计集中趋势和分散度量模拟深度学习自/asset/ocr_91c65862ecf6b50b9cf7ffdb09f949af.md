+   - is和looks被标注为VBZ—第三人称单数现在时动词。
+   - a被标注为DT—限定词。
+   - beautiful和bad被标注为JJ—形容词。
+   - Tomorrow被标注为NNP—专有单数名词。
+   - like被标注为IN—从属连词或介词。

# ## 11.2.4 提取名词短语

假如我们准备买滑水板，会先上网查一下，搜索“最好的滑水板”，这里“滑水板”是一个名词短语，如果搜索引擎没有正确解析名词短语，可能无法给出最佳搜索结果，这时候可以尝试搜索“最好的水”“最好的滑板”和“最好的滑水板”，看看分别能搜出什么。

TextBlob类的noun_phrases属性会返回一个WordList对象，它是一个包含多个Word对象的文本列表，文本中每个名词短语对应一个Word对象：

```
In [9]: blob
Out[9]: TextBlob("Today is a beautiful day. Tomorrow looks like bad weather.")
In [10]: blob.noun_phrases
Out[10]: WordList(['beautiful day', 'tomorrow', 'bad weather'])
```

请注意，表示名词短语的Word对象可以包含多个单词，WordList是Python内置列表类型的扩展，提供了额外的用于词干提取、词形还原、单数化和复数化的方法。

# ## 11.2.5 使用TextBlob的默认情感分析器进行情感分析

最常见和最有价值的NLP任务之一是情感分析，用来确定文本是正面的、中性的还是负面的，例如，有些公司可能会用它来判断人们是否在网上对他们的产品做出正面或负面的评论。我们都知道表达积极的单词“好”和表达消极的单词“坏”，但不能仅仅因为一个句子包含“好”或“坏”来判定它表达的情感必然是积极的或消极的。例如下面的句子：

```
The food is not good.
```