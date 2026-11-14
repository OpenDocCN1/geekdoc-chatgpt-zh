
![](img/bf43b6caad271ba92b343a48c79c2155_854_0.png)

## 16.8.5 创建一个Python PubNub订阅服务器

PubNub提供pubnub Python模块，用于方便地执行发布/订阅操作，还提供了7个样本流供用户使用4个实时流和3个模拟流进行实验：

- Twitter Stream（Twitter流）—从Twitter实时流每秒提供多达50条推文，并且不需要用户的Twitter凭据。
- Hacker News Articles（黑客新闻文章）—该网站的最新文章。
- State Capital Weather（州首府天气）—提供美国州首府的天气数据。
- Wikipedia Changes（Wikipedia更改）—Wikipedia编辑流。
- Game State Sync（游戏状态同步）—模拟来自多人游戏的数据。
- Sensor Network（传感器网络）—来自辐射、湿度、温度和环境光传感器的模拟数据。
- Market Orders（市场订单）—模拟五家公司的股票订单。

这里，将使用pubnub模块订阅其模拟的Market Orders流，然后将不断变化的股票价格可视化为Seaborn原型，如下所示。

![](img/bf43b6caad271ba92b343a48c79c2155_855_0.png)

当然，也可以将消息发布到流。有关详细信息，请参阅 https://www.pubnub.com/docs/python/pubnub-python-sdk。

要准备在Python中使用PubNub，请执行以下命令安装最新版本的 pubnub模块

```
pip install "pubnub>=4.1.2"
```

使用脚本stocklistener.py订阅该流，并且可视化股价，该流在ch16文件夹的pubnub子文件夹中定义。为了便于讨论，我们把脚本分成几部分。

### 消息格式

模拟的Market Orders流返回包含五个键-值对的JSON对象，这些键-值对具有键'bid_price'、'order_quantity'、'symbol'、'timestamp'和'trade_type'，这里仅使用'bid_price'和'symbol'。PubNub客户端将JSON数据作为Python字典返回。

### 导入库

第3～13行导入了此示例中使用的库，这里讨论第10～13行导入的PubNub类型：

```
# stocklistener.py
"""Visualizing a PubNub live stream."""
from matplotlib import animation
import matplotlib.pyplot as plt
import pandas as pd
import random
import seaborn as sns
import sys

from pubnub.callbacks import SubscribeCallback
from pubnub.enums import PNStatusCategory
from pubnub.pnconfiguration import PNConfiguration
from pubnub.pubnub import PubNub
```

### 用于存储公司名称和价格的列表和DataFrame

companies列表包含在Market Orders流中报告的公司名称，pandas DataFrame companies_df存储每个公司的最新的股票价格。将这个DataFrame与Seaborn一起使用来显示柱状图：

```
companies = ['Apple', 'Bespin Gas', 'Elerium', 'Google', 'Linen Cloth']
# DataFrame to store last stock prices
companies_df = pd.DataFrame(
    {'company': companies, 'price': [0, 0, 0, 0, 0]})
```

### SensorSubscriberCallback类

订阅PubNub流时必须添加一个侦听器，该侦听器接收来自频道的状态通知和消息，类似于前面定义的Tweepy侦听器。要创建侦听器，必须定义SubscribeCallback的子类（模块pubnub.callbacks），我们将在代码之后讨论它：

```
class SensorSubscriberCallback(SubscribeCallback):
    """SensorSubscriberCallback receives messages from PubNub."""
    def __init__(self, df, limit=1000):
        """Create instance variables for tracking number of tweets."""
        self.df = df # DataFrame to store last stock prices
        self.order_count = 0
        self.MAX_ORDERS = 1000 # 1000 by default
        super().__init__() # call superclass's init

    def status(self, pubnub, status):
        if status.category == PNStatusCategory.PNConnectedCategory:
            print('Connected to PubNub')
        elif status.category == PNStatusCategory.PNAcknowledgmentCategory:
            print('Disconnected from PubNub')

    def message(self, pubnub, message):
        symbol = message.message['symbol']
        bid_price = message.message['bid_price']
        print(symbol, bid_price)
        self.df.at[companies.index(symbol), 'price'] = bid_price
        self.order_count += 1

        # if MAX_ORDERS is reached, unsubscribe from PubNub channel
        if self.order_count == self.MAX_ORDERS:
            pubnub.unsubscribe_all()
```

SensorSubscriberCallback类的__init__方法存储每个新股票价格的DataFrame。每次新的状态消息到达时，PubNub客户端都会调用重写方法status，这里将检查指示已订阅或取消订阅频道的通知。

当新消息从频道到达时，PubNub客户端将调用重写方法message（第36~45行）。第37和38行从消息中获取公司名称和价格，可将其打印出来以便看到消息正在到达。第40行使用DataFrame方法定位相应公司的行及其'price'列，然后将新价格分配给该元素。一旦order_count达到MAX_ORDERS，第45行就会调用PubNub客户端的unsubscribe_all方法来取消订阅该频道。

### 函数更新

本例使用在第6章的“数据科学入门”部分学到的动画技术来可视化股票价格。函数update()指定了绘制一个动画帧的方式，并由稍后定义的FuncAnimation反复调用。我们利用Seaborn函数barplot，并通过使用x轴上的'company'列值和y轴上的'price'列值来可视化companies_df DataFrame中的数据：

```
def update(frame_number):
    """Configures bar plot contents for each animation frame."""
    plt.cla() # clear old barplot
    axes = sns.barplot(
        data=companies_df, x='company', y='price', palette='cool')
    axes.set(xlabel='Company', ylabel='Price')
    plt.tight_layout()
```

### 配置图

在脚本的主要部分首先设置Seaborn绘图样式，接着创建Figure对象，然后在该对象中显示柱状图：

```
if __name__ == '__main__':
    sns.set_style('whitegrid') # white background with gray grid lines
    figure = plt.figure('Stock Prices') # Figure for animation
```

### 配置FuncAnimation并显示窗口

接下来设置FuncAnimation来调用函数update，然后调用Matplotlib的show方法来显示该图。通常，该方法会阻止脚本继续执行，直到关闭该图为止。在这里，传递关键词参数block = False，以允许脚本继续运行，这样就可以配置PubNub客户端并订阅频道：

```
# configure and start animation that calls function update
stock_animation = animation.FuncAnimation(
    figure, update, repeat=False, interval=33)
plt.show(block=False) # display window
```

### 配置PubNub客户端

接下来，我们配置PubNub订阅密钥，PubNub客户端将其与频道名称结合使用以订阅频道。该密钥被指定为PNConfiguration对象（模块pubnub.pnconfiguration）的一个属性，第69行将其传递给新的PubNub客户端对象（模块pubnub.pubnub），第70~72行创建了SensorSubscriberCallback对象，并且将其传递给PubNub客户端的add_listener方法，以对其进行注册并从该频道接收消息。我们使用命令行参数来指定要处理的消息总数：

```
64     # set up pubnub-market-orders sensor stream key
65     config = PNConfiguration()
66     config.subscribe_key = 'sub-c-4377ab04-f100-11e3-bffd-02ee2ddab7fe'
67
68     # create PubNub client and register a SubscribeCallback
69     pubnub = PubNub(config)
70     pubnub.add_listener(
71         SensorSubscriberCallback(companies_df,
72         limit=int(sys.argv[1] if len(sys.argv) > 1 else 1000))
73
```

### 订阅频道

以下语句完成了订阅过程，表示希望从名为'pubnub-market-orders'的频道接收消息，其中execute方法用来启动流：

```
74     # subscribe to pubnub-sensor-network channel and begin streaming
75     pubnub.subscribe().channels('pubnub-market-orders').execute()
76
```

### 确保图保留在屏幕上

第二次调用Matplotlib的show方法，以确保在关闭图窗口之前图一直显示在屏幕上：

```
77     plt.show()  # keeps graph on screen until you dismiss its window
```

## 16.9 小结

本章介绍了大数据以及如何获取大数据，并讨论了处理大数据的硬件和软件基础设施。介绍了传统的关系数据库和结构化查询语言（SQL），并使用`sqlite3`模块在SQLite中创建和操作了一个`books`数据库。此外，还演示了如何将SQL查询结果加载到`pandas DataFrames`中。

本章讨论了NoSQL数据库的四种主要类型—键-值、文档、列式和图，并介绍了NewSQL数据库。将JSON `tweet`对象作为文档存储在基于云的MongoDB Atlas集群中，然后在Folium地图上显示的交互式可视化中对其进行汇总。

本章介绍了Hadoop及其在大数据应用程序中的使用，使用Microsoft Azure HDInsight服务配置了多节点Hadoop集群，然后使用Hadoop流创建、执行Hadoop MapReduce任务。

本章讨论了Spark及其在高性能、实时大数据处理中的应用。首先使用了Spark的函数式编程的过滤/映射/归约功能，然后在个人计算机上运行了Jupyter Docker堆栈，接着在Microsoft的HDInsight多节点Spark集群中本地运行了该堆栈。接下来介绍了Spark流用于处理小批量数据，并使用Spark SQL查询存储在Spark `DataFrame`中的数据。

本章最后介绍了物联网和发布/订阅模型。使用`freeboard.io`创建了来自PubNub的示例实时流的仪表板可视化，模拟了一个连接互联网的恒温器，该恒温器使用Python模块Dweepy向`dweet.io`服务发布消息，然后使用`freeboard.io`可视化了模拟设备的数据。最后，使用其Python模块订阅了PubNub示例实时流。

感谢你阅读本书。希望你喜欢这本书，觉得它有趣而且内容丰富。最重要的是，我们希望你有能力将所学到的技术应用到你的职业生涯中。

## 索引

索引中的页码为英文原书页码，与书中页边标注的页码一致。

### 符号

- ^ regex metacharacter (^, 正则表达式元字符), 206, 208
- ^ set difference operator (^, 集合差运算符), 150
- ^= set symmetric difference augmented assignment (^=, 集合对称差增强赋值运算符), 151
- _ (digit separator) (_, 数字分隔符), 77
- _ SQL wildcard character (_, SQL通配符), 512
- , (comma) in singleton tuple (, (逗号), 在单元素元组中使用), 107
- : (colon) (:, 冒号), 44
- != inequality operator (!=, 不等于运算符), 41, 45
- ? to access help in IPython (?, 在IPython中获取帮助信息), 74
- ?? to access help in IPython (include source code) (??，在IPython中获取帮助信息（包括源代码）) 74
- . regular expression metacharacter (., 正则表达式元字符), 210
- \' single-quote-character escape sequence (\', 单引号字符转义序列), 37
- relu (Rectified Linear Unit) activation function (整流线性单元激活函数), 475
- \" double-quote-character escape sequence (\\", 双引号字符转义序列), 37
- "is-a" relationships ("是" 关系运算), 267
- & regex metacharacter (&，正则表达式元字符), 209
- [] regex character class ([], 正则表达式字符类), 205
- [] subscript operator ([], 引用运算符), 103, 105
- {} for creating a dictionary ({}, 用于创建一个字典), 138
- {} placeholder in a format string ({}, 格式化字符串中的占位符), 196
- {n,} quantifier (regex) ({n,}, 用于正则表达式的量词), 206
- {n,m} quantifier (regex) ({n,m}, 用于正则表达式的量词), 207
- @-mentions (Twitter中的@某人), 337, 353
- * multiplication operator (*, 乘法运算符), 33, 45
- * operator for unpacking an iterable into function arguments (*, 用于将一个可迭代对象解压为若干函数参数的运算符), 86
- * quantifier (regex) (*, 用于正则表达式的量词), 206
- * SQL wildcard character (*, SQL通配符), 509
- * string repetition operator (*, 字符串重复运算符), 110, 196
