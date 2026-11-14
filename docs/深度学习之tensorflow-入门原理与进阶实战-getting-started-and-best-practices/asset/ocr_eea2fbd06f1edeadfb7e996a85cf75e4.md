# 代码4-13 作用域与操作符的受限范围（续）

```
32 with tf.variable_scope("scope"):
33     with tf.name_scope("bar"):
34         v = tf.get_variable("v", [1])
35         x = 1.0 + v
36     with tf.name_scope(""):
37         y = 1.0 + v
```

将 var4 和 y 的值打印出来，得出如下信息：

```
var4: scope1//v4:0
y.op: add
```

可以看到， y 变成顶层了， 而 var4 多了一个空层。

# 4.4 实例 19：图的基本操作

前面接触了一些图（一个计算任务）的概念， 这里来系统地了解一下 TensorFlow 中的图可以做哪些事情。

# 实例描述

（1）本例演示使用 3 种方式来建立图， 并依次设置为默认图， 使用 get_default_graph() 方法来获取当前默认图， 验证默认图的设置生效。

（2）演示获取图中相关内容的操作。

一个 TensorFlow 程序默认是建立一个图的， 除了系统自动建图以外， 还可以手动建立， 并做一些其他的操作。

# 4.4.1 建立图

可以在一个 TensorFlow 中手动建立其他的图， 也可以根据图里的变量获得当前的图。下面代码演示了使用 tf.Graph 函数建立图， 使用 tf.get_default_graph 函数获得图， 以及使用 reset_default_graph 的过程来重置图的过程。

# 代码4-14 图的基本操作

```
01 import numpy as np
02 import tensorflow as tf
03 c = tf.constant(0.0)
04
05 g = tf.Graph()
06 with g.as_default():
07     cl = tf.constant(0.0)
08     print(cl.graph)
09     print(g)
10     print(c.graph)
11
```