# 第 5 章
深度学习框架 TensorFlow 入门

深度学习已经广泛应用于各个领域，这里抛砖引玉，简单介绍一下时下非常热门的深度学习开源框架 TensorFlow。TensorFlow 是 Google 开源的机器学习工具，在 2015 年 11 月实现正式开源，开源协议为 Apache 2.0。TensorFlow 作为谷歌重要的开源项目，有非常火热的开源社区推动其发展。TensorFlow 中的实现代码可能跟通常的 Python 程序有点不一样，因为 TensorFlow 有它自己的框架和体系，会用它自己更加适配的方式来描述过程。

# 5.1 第一个 TensorFlow 的 “Hello world”

TensorFlow 是一个开源的、基于 Python 的机器学习框架，它由 Google 开发，并在图形分类、音频处理、推荐系统和自然语言处理等场景下有着丰富的应用，是目前最热门的机器学习框架之一。

在任何计算机语言中学习的第一个程序是都是 Hello world，我们也将遵循这个惯例，从程序 Hello world 开始，如图 5-1 所示。

```
import tensorflow as tf
message=tf.constant('Hello, world')
with tf.Session() as sess:
    print(sess.run(message).decode())

Hello, world
```

对图 5-1 中的代码分析如下：

+   （1）首先导入 TensorFlow，这将导入 TensorFlow 库，并允许使用其功能。
+   （2）由于要打印的信息是一个常量字符串，因此使用 tf.constant。
+   （3）为了执行计算图，利用 with 语句定义 Session 会话，并使用 run 来运行。
+   （4）最后是打印信息，这里的输出结果是一个字符串。

图 5-1

# 5.2 TensorFlow 程序结构

TensorFlow 与其他编程语言非常不同。TensorFlow 这个单词由两部分组成：Tensor 代表张量，是数据模型；Flow 代表流，是计算模型。张量表示某种相同数据类型 的多维数组，因此，张量有两个重要属性：数据类型（如浮点数、整型、字符串）、数组形状（各个维度的大小）。