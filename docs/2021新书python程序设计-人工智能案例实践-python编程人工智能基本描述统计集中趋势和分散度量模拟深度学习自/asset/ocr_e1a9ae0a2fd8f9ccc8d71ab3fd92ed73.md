# 12.11 热门话题发现: Twitter热门话题API

如果一个话题传播得非常快速，则可能会有成千上万甚至数百万人同时发布该话题相关的推文。Twitter将这些话题视为热门话题，并维护全球热门话题列表。通过Twitter热门话题API，可以获得有热门话题的地点的列表以及每个地点的前50个热门话题的列表。

# 12.11.1 有热门话题的地点

Tweepy API的trends_available方法调用Twitter API的trends/available方法，以获取Twitter中具有热门话题的所有地点的列表。trends_available方法返回用于表示这些地点的字典列表。当执行下面的代码时，能够得到467个（撰写本书时）具有热门话题的地点：

```
```python
In [31]: trends_available = api.trends_available()

In [32]: len(trends_available)
Out[32]: 467
```
```

trends_available返回的列表中，每个字典类型的元素具有各种信息，包括地点的名字和woeid（下面将会讨论）：

```
```python
In [33]: trends_available[0]
Out[33]:
{'name': 'Worldwide',
 'placeType': {'code': 19, 'name': 'Supername'},
 'url': 'http://where.yahooapis.com/v1/place/1',
 'parentid': 0,
 'country': '',
 'woeid': 1,
 'countryCode': None}

In [34]: trends_available[1]
Out[34]:
{'name': 'Winnipeg',
 'placeType': {'code': 7, 'name': 'Town'},
 'url': 'http://where.yahooapis.com/v1/place/2972',
 'parentid': 23424775,
 'country': 'Canada',
 'woeid': 2972,
 'countryCode': 'CA'}
```
```

Twitter热门话题API的trends/place方法使用Yahoo! WOEID（Where on Earth IDs）查找热门话题。WOEID用1代表全世界，其他地点的唯一WOEID值大于1。在接下来的两个小节中将使用WOEID来获取全球的热门话题和特定城市的热门话题。下表给出了几个地标、