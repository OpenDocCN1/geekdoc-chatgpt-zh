在本例中，使用JupyterLab的“Terminal”窗口在一个选项卡中执行starttweetstream.py，在另一个选项卡中使用笔记本执行Spark任务。在Jupyter pyspark-notebook Docker容器运行的情况下，在Web浏览器中打开http://localhost:8888/lab。在JupyterLab中，选择“File” > “New” > “Terminal”，打开一个包含“Terminal”的新选项卡。这是一个基于Linux的命令行，输入ls命令，按Enter键，列出当前文件夹的内容。默认情况下，将看到容器的work文件夹。要执行starttweetstream.py，必须先使用以下命令导航至SparkHashtagSummarizer文件夹：

```
cd work/SparkHashtagSummarizer
```

现在可以使用表单的命令执行脚本：

```
ipython starttweetstream.py number_of_tweets search_terms
```

其中number_of_tweets指定要处理的推文总数，search_terms用一个或多个由空格分隔的字符串来过滤推文。例如，下面的命令将发送1000条关于足球的推文：

```
ipython starttweetstream.py 1000 football
```

此时，脚本将显示“Waiting for connection”，一旦Spark连接上，则开始以流式发送推文。

# starttweetstream.py import语句

为了便于讨论，我们将starttweetstream.py分成几个部分。首先，我们导入脚本中使用的模块。Python标准库的socket模块提供了使Python应用程序能够通过套接字进行通信的功能。

```
# starttweetstream.py
"""Script to get tweets on topic(s) specified as script argument(s)
and send tweet text to a socket for processing by Spark."""
import keys
import socket
import sys
import tweepy
```