# 清理推文

下面做一些基本的推文清理工作，这些清理操作将在本章后面的例子中使用。`tweet-preprocessor`库的模块名称是`preprocessor`，其文档建议以如下方式导入模块：

```
In [1]: import preprocessor as p
```

通过调用`preprocessor`模块的`set_options`函数，可以设置所使用的清理选项。本例中，要删除网址和Twitter保留字：

```
In [2]: p.set_options(p.OPT.URL, p.OPT.RESERVED)
```

下面的代码对一个包含保留字（RT）和网址的示例推文完成了清理：

```
In [3]: tweet_text = 'RT A sample retweet with a URL https://nasa.gov'

In [4]: p.clean(tweet_text)
Out[4]: 'A sample retweet with a URL'
```