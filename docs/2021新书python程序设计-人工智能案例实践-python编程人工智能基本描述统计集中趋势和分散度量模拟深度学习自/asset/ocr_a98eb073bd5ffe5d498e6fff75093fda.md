# # 12.15.1 获取和映射推文

这里以交互方式编写用于绘制推文位置的代码，其中使用了tweeputilities.py文件中的实用函数和locationlistener.py中的LocationListener类。关于这些实用函数和类的详细信息，将在后续内容中讨论。

# ## 获取API对象

与其他关于流的示例一样，首先进行Twitter身份验证并获取Tweepy API对象。本示例通过tweetutilities.py中的get_API实用函数完成此操作：

```
```python
In [1]: from tweeputilities import get_API
In [2]: api = get_API()
```
```

# ## LocationListener类所需的数据

LocationListener类需要两个数据：一个列表tweets，用于存储所接收的推文；一个字典counts，用于记录接收的推文的总数和包含位置数据的推文数量。

```
```python
In [3]: tweets = []
In [4]: counts = {'total_tweets': 0, 'locations': 0}
```
```

# ## 创建LocationListener对象

本示例将通过LocationListener对象接收50条关于“football”的推文：

```
```python
In [5]: from locationlistener import LocationListener
In [6]: location_listener = LocationListener(api, counts_dict=counts,
   ...:    tweets_list=tweets, topic="football", limit=50)
   ...:
```
```