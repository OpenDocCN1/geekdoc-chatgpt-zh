IP ADDRESS” 按钮。在 “Add Whitelist Entry” 对话框中，可以添加当前计算机的IP地址，也可以设置允许从任何地方进行访问，但不建议用于商业数据库，不过可以用于学习。单击 “ALLOW ACCESS FROM ANYWHERE” 按钮，然后单击 “Confirm” 按钮以返回 “Connect to Atlas” 教程页面。

# Connect to your cluster

在教程窗口中，单击 “Connect to your cluster” 继续按教程前进，按照屏幕上的提示查看集群的 “Connect to YourClusterName” 对话框，从Python连接到MongoDB Atlas数据库需要用到一个连接语句。单击 “Connect Your Application” 按钮获取连接语句，再单击 “Short SRV connection string” 按钮，连接字符串语句将显示在 “Copy the SRV address” 下方。单击 “COPY” 按钮复制字符串，并将此字符串粘贴到keys.pyfile中作为mongo_connection_string的值。用密码替换连接字符串中的 “<PASSWORD>”，并将数据库名称 “test” 替换为 “senators”，这将是此示例中的数据库名称。在 “Connect to YourClusterName” 的底部，单击 “Close” 按钮。现在已经能够和Atlas集群进行交互了。

# 16. 4. 2 将推文存入MongoDB中

首先，我们将展示一个连接到MongoDB数据库的交互式IPython会话，通过Twitter流媒体下载当前的推文，并按推文数量汇总排名前10的参议员。接下来，类TweetListener处理传入的推文并将它们的JSON数据存储在MongoDB中。最后，将通过创建一个交互式Folium地图来继续IPython会话，该地图用于显示我们存储的推文信息。

# 使用Tweepy通过Twitter进行身份验证

首先，使用Tweepy通过Twitter进行身份验证：