```
with g1.as_default():
    #定义 g1 中的 Op、张量等
    ...
with g2.as_default():
```

(2) 正确的实践——获取默认数据流图的句柄，代码如下。

```
import tensorflow as tf
g1 = tf.get_default_graph()
g2 = tf.Graph()

with g1.as_default():
    #定义 g1 中的 Op、张量等
    ...

with g2.as_default():
    #定义 g2 中的 Op、张量等
    ...
```

(3) 错误的实践——将默认数据流图和用户创建的数据流图混合使用，代码如下。

```
import tensorflow as tf
g2 = tf.Graph()

#定义默认数据流图的 Op，张量等
    ...

with g2.as_default():
    #定义 g2 中的 Op、张量等
    ...
```

此外，从其他 TensorFlow 脚本中加载之前定义的模型，并利用 Graph.as_graph_def() 和 tf.import_graph_def() 函数将其赋给 Graph 对象也是可行的。这样，用户便可在同一个 Python 文件中计算和使用若干个独立模型的输出。

# 3.4.6 TensorFlow 的 Session

在之前的练习中，我们曾经介绍过 Session 类负责数据流图的执行。构造方法 tf.Session() 接收 3 个可选参数。

.target 指定了所要使用的执行引擎。对于大多数应用，该参数为默认的空字符串。在分布式设置中使用 Session 对象时，该参数用于连接不同的 tf.train.Server 实例。

.graph 参数指定了将要在 Session 对象中加载的 Graph 对象，其默认值为 None，表示将使用当前默认数据流图。当使用多个数据流图时，最好的方式是显式传入希望运行的 Graph 对象（而非在一个 with 语句块内创建 Session 对象）。

.config 参数允许用户指定配置 Session 对象所需的选项，如限制 CPU 或 GPU 的使用数目，为数据流图设置优化参数及日志选项等。

在典型的 TensorFlow 程序中，创建 Session 对象时无须改变任何默认构造参数。

```
import tensorflow as tf
```