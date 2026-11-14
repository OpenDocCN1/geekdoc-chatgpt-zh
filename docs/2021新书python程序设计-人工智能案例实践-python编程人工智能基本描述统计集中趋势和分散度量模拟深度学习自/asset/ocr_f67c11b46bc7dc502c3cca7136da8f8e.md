量，用于保存推文数量限制；第13行代码调用父类的__init__方法，用于保存api对象以供侦听器对象使用。

```
def __init__(self, api, limit=10):
    """Create instance variables for tracking number of tweets."""
    self.tweet_count = 0
    self.TWEET_LIMIT = limit
    super().__init__(api) # call superclass's init
```

# ## TweetListener类：on_connect方法

当一个应用程序成功连接到Twitter流时，on_connect方法将会自动被调用。下面的代码重写了StreamListener类的on_connect方法以显示“Connection successful”消息。

```
def on_connect(self):
    """Called when your connection attempt is successful, enabling
    you to perform appropriate application tasks at that point."""
    print('Connection successful\n')
```

# ## TweetListener类：on_status方法

当一条推文到达时，Tweepy会自动调用on_status方法。该方法的第二个参数status用于接收表示推文的Tweepy Status对象。第23~26行代码用于获取推文的文本内容。首先，假设推文使用新的280个字符限制，因此在try子句中尝试访问tweet的extended_tweet属性并获取其full_text。如果推文没有extended_tweet属性，则会发生异常，此时将在except子句中访问text属性。然后，第28~30行代码显示发送推文的用户的screen_name、推文的lang（即语言）和tweet_text。如果语言不是英语（'en'），则第32~33行代码会使用TextBlob翻译推文并以英语显示。第36行代码将self.tweet_count的值增1，然后在第39行代码中将self.tweet_count与self.TWEET_LIMIT进行比较并通过return将比较结果返回。如果on_status返回True，则Twitter流保持打开状态。否则，如果on_status返回False，则Tweepy会断开Twitter流连接。