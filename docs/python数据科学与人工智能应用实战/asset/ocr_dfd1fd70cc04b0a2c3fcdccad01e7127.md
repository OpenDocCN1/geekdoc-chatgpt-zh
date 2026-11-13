第 2 个 for/in 循环用于创建 BCCN 各版块的 URL 网址，使用列表和 “../” 路径来取代上一层的目录。在其运行结果中可以看到创建 python、include 和 asp 版块的 URL 路径，如下所示：

```
https://bbs.bccn.net/tag.php?name=python
https://bbs.bccn.net/tag.php?name=include
https://bbs.bccn.net/tag.php?name=asp
```

# 7.2 实战案例：majortests.com 的单词列表

majortests.com 是一个提供留学考试的模拟测验和教学资源的网站，如图 7-6 所示。

![](bbox=[0.251, 0.3, 0.8, 0.55])

图 7-6

图 7-6 方框中的内容是 1500 个英文基本单词，本书即获取此 URL 网址的英文单词列表。

# 步骤 1：识别出目标的 URL 网址

网络爬虫的第一步是识别出目标的单一 URL 网址或多个 URL 网址列表。以 majortests.com 中的 1500 个基本单词来说，前 9 页的 URL 网址如下所示：

+   http://www.majortests.com/word-lists/word-list-01.html
+   http://www.majortests.com/word-lists/word-list-02.html
+   http://www.majortests.com/word-lists/word-list-03.html
+   ...
+   http://www.majortests.com/word-lists/word-list-09.html

上述 URL 网址列表的 HTML 网页文件名只有最后 1 个数字不同，可以创建函数来产生这一系列的 URL 列表，如下所示：

```
URL = "http://www.majortests.com/word-lists/word-list-0{0}.html"
```