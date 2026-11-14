```
'nor', 'not', 'now', 'o', 'of', 'off', 'on', 'once', 'only',
'or', 'other', 'our', 'ours', 'ourselves', 'out', 'over',
'own', 're', 's', 'same', 'shan', "shan't", 'she', "she's",
'should', "should've", 'shouldn', "shouldn't", 'so', 'some',
'such', 't', 'than', 'that', "that'll", 'the', 'their',
'theirs', 'them', 'themselves', 'then', 'there', 'these',
'they', 'this', 'those', 'through', 'to', 'too', 'under',
'until', 'up', 've', 'very', 'was', 'wasn', "wasn't", 'we',
'were', 'weren', "weren't", 'what', 'when', 'where', 'which',
'while', 'who', 'whom', 'why', 'will', 'with', 'won',
"won't", 'wouldn', "wouldn't", 'y', 'you', "you'd", "you'll",
"you're", "you've", 'your', 'yours', 'yourself',
'yourselves']
```

NLTK库还列出了其他几种语言的停用词，在使用NLTK的停用词表之前，必须先使用nltk模块的download函数把它下载下来：

```
In [1]: import nltk
In [2]: nltk.download('stopwords')
[nltk_data] Downloading package stopwords to
[nltk_data]     C:\Users\PaulDeitel\AppData\Roaming\nltk_data...
[nltk_data]   Unzipping corpora\stopwords.zip.
Out[2]: True
```

对此示例，我们将加载'english'停用词表，首先从nltk.corpus模块导入stopwords，然后利用stopwords类中的words方法来加载'english'停用词表：

```
In [3]: from nltk.corpus import stopwords
In [4]: stops = stopwords.words('english')
```

下面创建一个TextBlob对象并从中移除停用词：

```
In [5]: from textblob import TextBlob
In [6]: blob = TextBlob('Today is a beautiful day.')
```

最后，为了删除停用词，在一个列表推导式中访问TextBlob对象中的单词，只有当该单词不在stops中时才将其返回：