
## 12.13 Twitter流API

免费的Twitter流API会动态地向应用程序推送随机选择的推文，推送的推文最多能达到每天推文量的百分之一。根据InternetLiveStats.com的统计结果，每秒大约有6000条推文，每天的推文超过了5亿条。因此，通过Twitter流API每天大约可以访问500万条推文。Twitter曾经允许免费访问10%的推文流，但这项服务（称为消防水带（fire hose））目前已经改为付费服务。本节将使用一个类定义和一个IPython会话来完成推文流处理过程的示例。请注意，接收推文流的代码需要放在一个自定义类中，该自定义类通过继承另一个已有类而得到创建。关于继承的相关知识已在第10章讨论。

### 12.13.1 创建StreamListener的子类

推文流API会在匹配搜索条件时发送推文。需要注意，推文流并不是在每次调用方法时才连接到Twitter，而是通过一个持久连接将推文推送（即发送）到应用程序。根据搜索条件的不同，推文在推送速度上的差别会很大。一个话题越流行，则推文就越有可能更快速地被推送。

处理推文流时，需要对Tweepy的StreamListener类进行继承，创建一个子类。该子类的对象是一个侦听器，用于接收每条新推文（或Twitter发送的其他消息）的到达通知。Twitter发送的每条消息都会导致一个StreamListener类的方法被自动调用，下表列出了几个会被自动调用的方法。StreamListener类已经定义了这些方法，因此在创建子类时只需要对所需的方法进行重新定义（即重写）即可。关于StreamListener类的其他方法，请参阅https://github.com/tweepy/tweepy/blob/master/tweepy/streaming.py。

| 方法 | 描述 |
| --- | --- |
| on_connect(self) | 成功连接到 Twitter 流时被调用。该方法中应包括仅在应用程序连接到流时才执行的语句 |
| on_status(self, status) | 当推文到达时被调用。status 是 Tweepy 的 Status 类的一个对象 |
| on_limit(self, track) | 在限制通知到达时被调用。如果搜索条件匹配的推文数多于 Twitter 可以根据其当前的流速率限制提供的推文数，则会产生限制通知。该限制通知包含匹配但无法传递的推文数 |
| on_error(self, status_code) | 响应 Twitter 发送的错误代码时被调用 |
| on_timeout(self) | 如果连接超时（即 Twitter 服务器没有响应），则被调用 |
| on_warning(self, notice) | 如果 Twitter 发送断开连接警告以表明连接可能关闭，则此方法被调用。例如，Twitter 为每个应用程序维护着一个推送给它们的推文队列。如果一个应用程序没有足够快地读取推文，on_warning 的 notice 参数将包含一条警告消息，表示如果队列已满，连接将会终止 |

### TweetListener类

下面的代码在tweetlistener.py中定义了StreamListener类的子类 TweetListener，其中第6行表示TweetListener类是 tweepy.StreamListener类的子类。通过继承，TweetListener这个新类具有在StreamListener类中实现的那些方法。

```
# tweetlistener.py
"""tweepy.StreamListener subclass that processes tweets as they arrive."""
import tweepy

from textblob import TextBlob

class TweetListener(tweepy.StreamListener):
    """Handles incoming Tweet stream."""
```

### TweetListener类：__init__方法

下面的代码定义了TweetListener类的__init__方法，该方法在创建新的TweetListener对象时被调用。api参数是TweetListener用于与Twitter交互的Tweepy API对象；limit参数是要处理的推文总数，默认为10，通过此参数可以控制要接收的推文数量。后面将会看到，在达到该限制时Twitter流会自动终止；如果将limit设置为None，则Twitter流不会自动终止。第11行代码创建了一个实例变量，用于跟踪目前为止处理的推文数量（初始值为0）；第12行代码创建了一个常量，用于保存推文数量限制；第13行代码调用父类的__init__方法，用于保存api对象以供侦听器对象使用。

```
def __init__(self, api, limit=10):
    """Create instance variables for tracking number of tweets."""
    self.tweet_count = 0
    self.TWEET_LIMIT = limit
    super().__init__(api) # call superclass's init
```

### TweetListener类：on_connect方法

当一个应用程序成功连接到Twitter流时，on_connect方法将会自动被调用。下面的代码重写了StreamListener类的on_connect方法以显示“Connection successful”消息。

```
def on_connect(self):
    """Called when your connection attempt is successful, enabling
    you to perform appropriate application tasks at that point."""
    print('Connection successful\n')
```

### TweetListener类：on_status方法

当一条推文到达时，Tweepy会自动调用on_status方法。该方法的第二个参数status用于接收表示推文的Tweepy Status对象。第23~26行代码用于获取推文的文本内容。首先，假设推文使用新的280个字符限制，因此在try子句中尝试访问tweet的extended_tweet属性并获取其full_text。如果推文没有extended_tweet属性，则会发生异常，此时将在except子句中访问text属性。然后，第28~30行代码显示发送推文的用户的screen_name、推文的lang（即语言）和tweet_text。如果语言不是英语（'en'），则第32~33行代码会使用TextBlob翻译推文并以英语显示。第36行代码将self.tweet_count的值增1，然后在第39行代码中将self.tweet_count与self.TWEET_LIMIT进行比较并通过return将比较结果返回。如果on_status返回True，则Twitter流保持打开状态。否则，如果on_status返回False，则Tweepy会断开Twitter流连接。

```
def on_status(self, status):
    """Called when Twitter pushes a new tweet to you."""
    # get the tweet text
    try:
        tweet_text = status.extended_tweet.full_text
    except:
        tweet_text = status.text
    print(f'Screen name: {status.user.screen_name}')
    print(f'Language: {status.lang}')
    print(f'Status: {tweet_text}')
    if status.lang != 'en':
        print(f'Translated: {TextBlob(tweet_text).translate()}')
    print()
    self.tweet_count += 1  # track number of tweets processed
    # if TWEET_LIMIT is reached, return False to terminate streaming
    return self.tweet_count != self.TWEET_LIMIT
```

### 12.13.2 启动流处理

这里使用一个IPython会话来测试TweetListener类。

### 身份验证

首先，必须进行Twitter身份验证并创建一个Tweepy API对象：

```
In [1]: import tweepy

In [2]: import keys

In [3]: auth = tweepy.OAuthHandler(keys.consumer_key,
   ...:                         keys.consumer_secret)

In [4]: auth.set_access_token(keys.access_token,
   ...:                        keys.access_token_secret)

In [5]: api = tweepy.API(auth, wait_on_rate_limit=True,
   ...:               wait_on_rate_limit_notify=True)
```

### 创建一个TweetListener对象

接下来，创建一个TweetListener类的对象并使用api对象初始化它：

```
In [6]: from tweetlistener import TweetListener

In [7]: tweet_listener = TweetListener(api)
```

这里没有指定limit参数，因此这个TweetListener对象会在接收10条推文之后终止。

### 创建一个Stream对象

Tweepy的Stream对象用于管理与Twitter流的连接，并将信息传递给TweetListener对象。Stream构造函数的auth关键字参数接收api对象的auth属性，该属性包含了前面配置的OAuthHandler对象。listener关键字参数接收侦听器对象tweet_listener：

```
tweet_stream = tweepy.Stream(auth=api.auth,
 listener=tweet_listener)
```

### 启动推文流

通过Stream对象的filter方法开始进行流处理。这里跟踪关于NASA Mars rovers的推文，并使用track参数传递搜索词列表：

```
tweet_stream.filter(track=['Mars Rover'], is_async=True)
```

流API将返回匹配推文的完整JSON对象，进行匹配时考虑了推文中的所有数据项，不仅包括推文的文本信息，还包括@某用户、主题标签、扩展的URL以及在一个推文对象的JSON中维护的其他Twitter信息。因此，在推文的文本中有可能找不到正在跟踪的搜索词（即搜索词可能出现在@某用户、主题标签等其他Twitter信息中）。

### 异步流与同步流

参数is_async = True表示filter方法应启动异步推文流。这使得代码在侦听器等待接收推文时可以继续执行，同时也允许提前终止流。当在IPython中执行异步推文流时，将会看到下一个In [ ]提示，并可以通过将Stream对象的running属性设置为False来终止推文流，如下所示：

```
tweet_stream.running=False
```

如果不指定参数is_async = True，则filter方法将启动同步推文流。  
这种情况下，IPython在流终止后才会显示下一个In [ ]提示符。异步流在GUI（即图形用户界面）应用程序中特别有用，其使得用户可以在推文到达时继续与应用程序的其他部分进行交互。以下显示了两条推文的部分输出内容：

![](img/bf43b6caad271ba92b343a48c79c2155_577_0.png)

### filter方法的其他参数

filter方法还具有用于按Twitter用户ID号（用来跟踪来自特定用户的推文）和按位置来细化推文搜索结果的参数。有关这些参数的详细信息，请参阅 https://developer.twitter.com/en/docs/tweets/filter-realtime/guides/basic-stream-parameters。

### 关于Twitter限制的注意事项

营销人员、研究人员和其他人经常将他们从流API接收到的推文存储起来。Twitter要求用户在接收到一条删除信息时，将该删除信息对应的推文或位置数据从已存储的推文数据中删除。如果一名Twitter用户在Twitter推送其发布的推文后删除了推文或位置数据，那么Twitter就会向接收了这些数据的用户发送删除信息。当接收到删除信息时，侦听器的on_delete方法会被自动调用。有关删除规则和详细信息，请参阅 https://developer.twitter.com/en/docs/tweets/filter-realtime/guides/streaming-message-types。

## 12.14 推文情感分析

第11章已经展示了句子的情感分析方法，许多研究人员和公司会对推文进行情感分析。例如，政治研究人员可能会在选举时期通过推文情感分析，了解人们对特定政治家和问题的看法；公司可能会通过推文情感分析，了解人们对其产品和竞争对手产品的看法。

本节将使用前一节中介绍的技术创建一个脚本（sentimentlistener.py），用于分析特定主题的推文的情感。该脚本将分别统计具有正面、中性和负面情感的推文的总数，并显示统计结果。

该脚本接收两个命令行参数，分别表示希望接收的推文的主题以及用于分析情感的推文的数量。使用的推文中，不包括那些被删除的推文；对于有大量转推的“病毒”主题，也不计算在内。因此，需要花费一些时间来获取指定数量的推文。从ch12文件夹运行脚本

```
ipython sentimentlistener.py football 10
```

则会产生下面的输出结果。具有正面情感的推文的前面有一个+，具有负面情感的推文的前面有一个-，而具有中性情感的推文的前面有一个空格：

- ftblNeutral: Awful game of football. So boring slow football complete waste of another 90 minutes of my life that I'll never get back #BURMUN
+ TBulmer28: I've seen 2 successful onside kicks within a 40 minute span.
I love college football
+ CMayDay12: The last normal Sunday for the next couple months. Don't text me don't call me. I am busy. Football season is finally here?
rpimusic: My heart legitimately hurts for Kansas football fans
+ DSCunningham30: @LeahShieldswPSD It's awesome that u like college football, but my favorite team is ND - GO IRISH!!!
damanr: I'm bummed I don't know enough about football to roast @sames-fandiani properly about the Raiders
+ jamesianosborne: @TheRochaSays @WatfordFC @JackHind Haha.... just when you think an American understands Football...... so close. Wat...
+ Tshanerbeer: @PennStateFball @PennStateOnBTN Ah yes, welcome back college football. You've been missed.
- cougarhokie: @Hokiehack @skipytyler I can verify the badness of that football
+ Unite_Reddevils: @Pablo_di_Don Well make yourself clear it's football not soccer we follow European football not MLS soccer
Tweet sentiment for "football"
Positive: 6
Neutral: 2
Negative: 2

下面分析脚本sentimentlistener.py中的代码，这里只关注此示例中的新功能。

### 导入库

第4~8行导入keys.py文件和整个脚本中使用的库：

```
# sentimentlistener.py
"""Script that searches for tweets that match a search string
and tallies the number of positive, neutral and negative tweets."""
import keys
import preprocessor as p
import sys
from textblob import TextBlob
import tweepy
```

### SentimentListener类：__init__方法

除了与Twitter交互的API对象之外，__init__方法还接收其他三个参数：
