# 16.9 小结

本章介绍了大数据以及如何获取大数据，并讨论了处理大数据的硬件和软件基础设施。介绍了传统的关系数据库和结构化查询语言（SQL），并使用`sqlite3`模块在SQLite中创建和操作了一个`books`数据库。此外，还演示了如何将SQL查询结果加载到`pandas DataFrames`中。

本章讨论了NoSQL数据库的四种主要类型—键-值、文档、列式和图，并介绍了NewSQL数据库。将JSON `tweet`对象作为文档存储在基于云的MongoDB Atlas集群中，然后在Folium地图上显示的交互式可视化中对其进行汇总。

本章介绍了Hadoop及其在大数据应用程序中的使用，使用Microsoft Azure HDInsight服务配置了多节点Hadoop集群，然后使用Hadoop流创建、执行Hadoop MapReduce任务。

本章讨论了Spark及其在高性能、实时大数据处理中的应用。首先使用了Spark的函数式编程的过滤/映射/归约功能，然后在个人计算机上运行了Jupyter Docker堆栈，接着在Microsoft的HDInsight多节点Spark集群中本地运行了该堆栈。接下来介绍了Spark流用于处理小批量数据，并使用Spark SQL查询存储在Spark `DataFrame`中的数据。

本章最后介绍了物联网和发布/订阅模型。使用`freeboard.io`创建了来自PubNub的示例实时流的仪表板可视化，模拟了一个连接互联网的恒温器，该恒温器使用Python模块Dweepy向`dweet.io`服务发布消息，然后使用`freeboard.io`可视化了模拟设备的数据。最后，使用其Python模块订阅了PubNub示例实时流。

感谢你阅读本书。希望你喜欢这本书，觉得它有趣而且内容丰富。最重要的是，我们希望你有能力将所学到的技术应用到你的职业生涯中。