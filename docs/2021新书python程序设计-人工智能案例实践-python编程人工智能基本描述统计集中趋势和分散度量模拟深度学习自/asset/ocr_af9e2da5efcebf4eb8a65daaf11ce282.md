```
In [20]: blob.sentiment
Out[20]: Sentiment(classification='neg', p_pos=0.47662917962091056,
p_neg=0.5233708203790892)
```

整体情感被归类为负面（classification='neg'），但 Sentiment 对象中的 p_pos 显示该 TextBlob 对象有 47.66% 的正面情感，p_neg 显示有 52.34% 的负面情感，由于整体情感略微偏负面，我们认为这个 TextBlob 对象的情感为中性。

下面来获取每个句子的情感：

```
In [21]: for sentence in blob.sentences:
    ...:     print(sentence.sentiment)
    ...:
Sentiment(classification='pos', p_pos=0.8117563121751951,
p_neg=0.18824368782480477)
Sentiment(classification='neg', p_pos=0.174363226578349,
p_neg=0.8256367734216521)
```

请注意，我们从 NaiveBayesAnalyzer 得到的 Sentiment 对象不是 polarity 值和 subjectivity 值，而是情感类别—'pos'（正面的）或 'neg'（负面的），即从 0.0 到 1.0 的 p_pos（正面百分比）值和 p_neg（负面百分比）值。从结果中可以再次看到 TextBlob 对象的第一句话是正面的，第二句是负面的。

# 11.2.7 语言检测与翻译

跨语言翻译在自然语言处理和人工智能中是一个具有挑战性的问题。随着机器学习、人工智能和自然语言处理的进步，Google Translate（100多种语言）和微软 Bing 翻译器（60多种语言）等可以在不同语言之间实现即时翻译。

对于前往国外旅行的人来说，跨语言翻译也很有用。他们可以使用翻译 APP 来翻译菜单、路标等，甚至还可以现场语音翻译，以便与不了解自己语言的人实时交谈。有些智能手机现在可以通过耳机提供许多语言的实时翻译。在第 13 章中，我们将会开发一个可以在 Watson 支持的语言中实现近乎实时跨语言翻译的脚本。

TextBlob 库使用 Google Translate 来检测文本语言并将 TextBlob、Sentence 和 Word 对象转换成其他语言。下面使用 detect_language 方法来检测文本语言（'en' 代表英语）：