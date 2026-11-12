
从技术角度讲，“==”运算符也被重载了，但它不会返回一个布尔型的张量对象。它所判断的是两个张量对象名是否引用了同一个对象，若是，则返回 True；否则，返回 False。这个功能主要是在 TensorFlow 内部使用。如果希望检查张量值是否相同，请使用 tf.equal()。

## 3.4.5 TensorFlow 的 Graph 对象

到目前为止，我们对数据流图的了解仅限于在 TensorFlow 中无处不在的某种抽象概念，而且对于开始编码时 Op 如何自动依附于某个数据流图并不清楚。既然已经接触了一些例子，下面来研究 TensorFlow 的 Graph 对象，学习如何创建更多的数据流图，以及如何让多个流图协同工作。

创建 Graph 对象的方法非常简单，它的构造方法不需要接收任何参数，代码如下。

```python
import tensorflow as tf

#创建一个新的数据流图
g = tf.Graph()
```

Graph 对象初始化完成后，便可利用 Graph.as_default()方法访问其上下文管理器，为其添加 Op。

结合 with 语句，可利用上下文管理器通知 TensorFlow 需要将一些 Op 添加到某个特定 Graph 对象中，代码如下。

```python
with g.as_default():
    #像往常一样创建一些 Op; 它们将被添加到 Graph 对象 g 中
    a = tf.multiply(2, 3)
    ...
```

为什么在上面的例子中不需要指定将 Op 添加到哪个 Graph 对象？原因是这样的：为方便起见，当 TensorFlow 库被加载时，它会自动创建一个 Graph 对象，并将其作为默认的数据流图。因此，在 Graph.as_default()上下文管理器之外定义的任何 Op、张量对象都会自动放置在默认的数据流图中。

```python
#放置在默认数据流图中
in_default_graph = tf.add(1, 2)
```

```python
#放置在数据流图 g 中
with g.as_default():
    in_graph_g = tf.multiply(2, 3)
```

```python
#由于不在 with 语句块中，下面的 Op 将放置在默认数据流图中
also_in_default_graph = tf.sub(5, 1)
```

如果写获得默认数据流图的句柄，可使用 tf.get_default_graph()函数，代码如下。

```python
default_graph = tf.get_default_graph()
```

在大多数 TensorFlow 程序中，只使用默认数据流图就足够了。然而，如果需要定义多个相互之间不存在依赖关系的模型，则创建多个 Graph 对象十分有用。当需要在单个文件中定义多个数据流图时，最佳实践是不使用默认数据流图，或为其立即分配句柄。这样可以保证各节点按照一致的方式添加到每个数据流图中。

- （1）正确的实践——创建新的数据流图，将默认数据流图忽略，代码如下。

```python
import tensorflow as tf

g1 = tf.Graph()
g2 = tf.Graph()
with g1.as_default():
    #定义 g1 中的 Op、张量等
    ...
with g2.as_default():
    #定义 g2 中的 Op、张量等
    ...
```

- （2）正确的实践——获取默认数据流图的句柄，代码如下。

```python
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

- （3）错误的实践——将默认数据流图和用户创建的数据流图混合使用，代码如下。

```python
import tensorflow as tf
g2 = tf.Graph()

#定义默认数据流图的 Op，张量等
    ...

with g2.as_default():
    #定义 g2 中的 Op、张量等
    ...
```

此外，从其他 TensorFlow 脚本中加载之前定义的模型，并利用 Graph.as_graph_def() 和 tf.import_graph_def() 函数将其赋给 Graph 对象也是可行的。这样，用户便可在同一个 Python 文件中计算和使用若干个独立模型的输出。

## 3.4.6 TensorFlow 的 Session

在之前的练习中，我们曾经介绍过 Session 类负责数据流图的执行。构造方法 tf.Session() 接收 3 个可选参数。

.target 指定了所要使用的执行引擎。对于大多数应用，该参数为默认的空字符串。在分布式设置中使用 Session 对象时，该参数用于连接不同的 tf.train.Server 实例。

.graph 参数指定了将要在 Session 对象中加载的 Graph 对象，其默认值为 None，表示将使用当前默认数据流图。当使用多个数据流图时，最好的方式是显式传入希望运行的 Graph 对象（而非在一个 with 语句块内创建 Session 对象）。

.config 参数允许用户指定配置 Session 对象所需的选项，如限制 CPU 或 GPU 的使用数目，为数据流图设置优化参数及日志选项等。

在典型的 TensorFlow 程序中，创建 Session 对象时无须改变任何默认构造参数。

```python
import tensorflow as tf

#创建 Op、张量对象等（使用默认数据流图）
a = tf.add(2, 5)
b = tf.multiply(a, 3)
```

```python
#利用默认数据流图启动一个 Session 对象
sess = tf.Session()
```

注意，下列两种调用方式是等价的。

```python
sess = tf.Session()
sess = tf.Session(graph=tf.get_default_graph())
```

一旦创建完 Session 对象，便可利用其主要的方法 run()来计算所期望的张量对象的输出。

```python
sess.run(b) #返回 21
```

Session.run()方法接收一个参数 fetches，以及其他 3 个可选参数：feed_dict、options 和 run_metadata。本书不打算对 options 和 run_metadata 进行介绍，因为它们尚处在实验阶段（因此以后很可能会有变动），且目前用途非常有限，但理解 feed_dict 非常重要，下文将对其进行讲解。

### 1. fetches 参数

fetches 参数接收任意的数据流图元素（ Op 或张量对象），后者指定了用户希望执行的对象。如果请求对象为张量对象，则 run()的输出将为一 NumPy 数组；如果请求对象为一个 Op，则输出将为 None。

在上面的例子中，将 fetches 参数取为张量 b (tf.mul Op 的输出）。TensorFlow 便会得到通知，Session 对象应当找到为计算 b 的值所需的全部节点，顺序执行这些节点，然后将 b 的值输出。我们还可传入一个数据流图元素的列表：

```python
sess.run([a, b]) #返回 [7, 21]
```

当 fetches 为一个列表时，run()的输出将为一个与所请求的元素对应的值的列表。在本例中，请求计算 a 和 b 的值，并保持这种次序。由于 a 和 b 均为张量，因此会接收到作为输出的它们的值。

### 2. feed_dict 参数

参数 feed_dict 用于覆盖数据流图中的张量对象值，它需要 Python 字典对象作为输入。字典中的“键”为指向应当被覆盖的张量对象的句柄，而字典的“值”可以是数字、字符串、列表或 NumPy 数组。这些“值”的类型必须与张量的“型”相同，或能够转换为相同的类型。下面通过一些代码来展示如何利用 feed_dict 重写之前的数据流图中 a 的值。

```python
import tensorflow as tf

#创建 Op、Tensor 对象等（使用默认数据流图）
a = tf.add(2, 5)
b = tf.multiply(a, 3)

#利用默认数据流图启动一个 Session 对象
sess = tf.Session()

#定义一个字典，比如将 a 的值替换为 15
replace_dict = {a: 15}

#运行 Session 对象，将 replace_dict 赋给 feed_dict
sess.run(b, feed_dict=replace_dict) #返回 45
```

请注意，即便 a 的计算结果通常为 7，我们传给 feed_dict 的字典也会将它替换为 15。在相当多的场合中，feed_dict 都极为有用。由于张量的值是预先提供的，数据流图不再需要对该张量的任何普通依赖节点进行计算。这意味着如果有一个规模较大的数据流图，并希望用一些虚构的值对某些部分进行测试，TensorFlow 将不会在不必要的计算上浪费时间。对于指定输入值，feed_dict 也十分有用，在稍后的占位符一节中我们将对此进行介绍。

Session 对象使用完毕后，需要调用其 close()方法将不再需要的资源释放。

```python
#创建 Session 对象
sess = tf.Session()

#运行该数据流图，写入一些统计量等
...

#关闭数据流图，释放资源
sess.close()
```

或者，也可以将 Session 对象作为上下文管理器加以使用，这样当代码离开其作用域后，该 Session 对象将自动关闭：

```python
with tf.Session() as sess:
    #运行数据流图，写入统计量等
    ...
    #Session 对象自动关闭
```

也可利用 Session 类的 as_default()方法将 Session 对象作为上下文管理器加以使用。类似于 Graph 对象被某些 Op 隐式使用的方式，可将一个 Session 对象设置为可被某些函数自动使用。这些函数中最常见的有 Operation.run()和 Tensor.eval()，调用这些函数相当于将它们直接传入 Session.run()函数。

```python
#定义简单的常量
a = tf.constant(5)
#创建一个 Session 对象
sess = tf.Session()

#在 with 语句块中将该 Session 对象作为默认 Session 对象
with sess.as_default():
    a.eval()

#必须手工关闭 Session 对象
sess.close()
```

关于 InteractiveSession 进行进一步讨论。

在本书之前的章节中，我们提到 InteractiveSession 是另外一种类型的 TensorFlow Session，但我们不打算使用它。InteractiveSession 对象所做的全部内容是在运行时将其作为默认 Session，这在使用交互式 Python Shell 的场合是非常方便的，因为可使用 a.eval()或 a.run()，而无须显式输入 sess.run([a])。然而，如果需要同时使用多个 Session，则事情会变得有些麻烦。可以发现在运行数据流图时，如果能够保持一致的方式，将会使调试变得更容易，因此我们坚持使用常规的 Session 对象。

既然已对运行数据流图有了切实的理解，下面来探讨如何恰当地指定输入节点，并结合它们来使用 feed_dict。

## 3.4.7 输入与占位符

之前定义的数据流图并未使用真正的“输入”，它总是使用相同的数值 5 和 3。我们真正希望做的是从客户那里接收输入值，这样便可对数据流图中所描述的变换以各种不同类型的数值进行复用，借助“占位符”可达到这个目的。正如其名称所预示的那样，占位符的行为与张量对象一致，但在创建时无须为它们指定具体的数值。它们的作用是为运行时即将到来的某个张量对象预留位置，因此实际上上变成了“输入”节点。利用 tf.placeholder Op 可创建占位符。

```python
import tensorflow as tf
import numpy as np

#创建一个长度为 2、数据类型为 int32 的占位向量
a = tf.placeholder(tf.int32, shape=[2], name="my_input")

#将该占位向量视为其他任意张量对象，加以使用
b = tf.reduce_prod(a, name="prod_b")
c = tf.reduce_sum(a, name="sum_c")

#完成数据流图的定义
d = tf.add(b, c, name="add_d")
```

调用 tf.placeholder()时，dtype 参数是必须指定的，而 shape 参数可选。

dtype 指定了将传给该占位符的值的数据类型。该参数是必须指定的，因为需要确保不出现类型不匹配的错误。

shape 指定了所要传入的张量对象的形状。请参考前文中对张量形状的讨论。shape 参数的默认值为 None，表示可接收任意形状的张量对象。

与任何 Op 一样，也可在 tf.placeholder 中指定一个 name 标识符。

为了给占位符传入一个实际的值，需要使用 Session.run()中的 feed_dict 参数。我们将指向占位符输出的句柄作为字典（在上述代码中，对应变量 a）的“键”，而将希望传入的张量对象作为字典的“值”。

```python
#定义一个 TensorFlow Session 对象
sess = tf.Session()

#创建一个将传给 feed_dict 参数的字典
#键：’a’ ，指向占位符输出张量对象的句柄
#值：一个值为[5, 3]、类型为 int32 的向量
input_dict = {a: np.array([5, 3], dtype=np.int32)}

#计算 d 的值，将 input_dict 的“值”传给 a
sess.run(d, feed_dict=input_dict)
```

必须在 feed_dict 中为待计算的节点的每个依赖占位符包含一个键值对。在上面的代码中，需要计算 d 的输出，而它依赖于 a 的输出。如果还定义了一些 d 不依赖的其他占位符，则无须将它们包含在 feed_dict 中。

Placeholder 的值是无法计算的，如果试图将其传入 Session.run()，将会引发一个异常。

## 3.4.8 Variable 对象

### 1. 创建 Variable 对象

张量对象和 Op 对象都是不可变的 ( immutable ), 但机器学习任务的本质决定了需要一种机制保存随时间变化的值。借助 TensorFlow 中的 Variable 对象，便可达到这个目的。Variable 对象包含了在对 Session.run()多次调用中可持久化的可变张量值。Variable 对象的创建可通过 Variable 类的构造方法 tf.Variable()完成。

```python
import tensorflow as tf

# 为 Variable 对象传入一个初始值 3
my_var = tf.Variable(3, name="my_variable")
```

Variable 对象可用于任何可能会使用张量对象的 TensorFlow 函数或 Op 中，其当前值将传给使用它的 Op。

```python
add = tf.add(5, my_var)
mul = tf.multiply(8, my_var)
```

Variables 对象的初值通常是全 0、全 1 或用随机数填充的阶数较高的张量。为使创建具有这些常见类型初值的张量更加容易，TensorFlow 提供了大量辅助 Op，如 tf.zeros()、tf.ones()、tf.random_normal() 和 tf.random_uniform()，每个 Op 都接收一个 shape 参数，以指定所创建张量对象的形状。

```python
# 2*2 的零矩阵
zeros = tf.zeros([2, 2])

# 长度为 6 的全 1 向量
ones = tf.ones([6])

# 3*3*3 的张量，其元素服从 0~10 的均匀分布
uniform = tf.random_uniform([3, 3, 3], minval=0, maxval=10)

# 3*3*3 的张量，其元素服从 0 均值，标准差为 2 的正态分布
normal = tf.random_normal([3, 3, 3], mean=0.0, stddev=2.0)
```

除了 tf.random_normal()外，还经常会看到人们使用 tf.truncated_normal()，因为它不会创建任何偏离均值超过两倍标准差的值，从而可以防止有一个或两个元素与该张量中的其他元素显著不同的情况出现。

```python
# 该张量对象不会返回任何小于 3.0 或大于 7.0 的值
trunc = tf.truncated_normal([2, 2], mean = 5.0, stddev = 1.0)
```

可像手工初始化张量那样将这些 Op 作为 Variable 对象的初值传入。

```python
# 默认均值为 0，默认标准差为 1.0
random_var = tf.Variable(tf.truncated_normal([2, 2]))
```

### 2. Variable 对象的初始化

Variable 对象与大多数其他 TensorFlow 对象在 Graph 中存在的方式都比较类似，但它们的状态实际上是由 Session 对象管理的。因此，为使用 Variable 对象，需要采取一些额外的步骤——必须在一个 Session 对象内对 Variable 对象进行初始化。这样会使 Session 对象开始追踪这个 Variable 对象的值的变化。

Variable 对象的初始化通常是通过将 tf.global_variables_initializer() Op 传给 Session.run()完成的，代码如下。

```python
init = tf.global_variables_initializer()
sess = tf.Session()
sess.run(init)
```

如果只需要对数据流图中定义的一个 Variable 对象子集初始化，可使用 tf.initialize_variables()。该函数可接收一个要进行初始化的 Variable 对象列表，代码如下。

```python
var1 = tf.Variable(0, name="initialize_me")
var2 = tf.Variable(1, name="no_initialization")
init = tf.global_variables_initializer()
sess = tf.Session()
sess.run(init)
```

### 3. Variable 对象的修改

要修改 Variable 对象的值，可使用 Variable.assign()方法。该方法的作用是为 Variable 对象赋予新值。请注意，Variable.assign()是一个 Op，要使其生效必须在一个 Session 对象中运行，代码如下。

```python
#创建一个初值为 1 的 Variable 对象
my_var = tf.Variable(1)

#创建一个 Op，使其在每层运行时都将该 Variable 对象乘以 2
my_var_times_two = my_var.assign(my_var * 2)

#初始化 Op
init = tf.global_variables_initializer()

#启动一个会话
sess = tf.Session()

#初始化 Variable 对象
sess.run(init)

#将 Variable 对象乘以 2，并将其返回
sess.run(my_var_times_two)
#输出：2

#再次相乘
sess.run(my_var_times_two)
#输出：4

#再次相乘
sess.run(my_var_times_two)
#输出：8
```

对于 Variable 对象的简单自增和自减，TensorFlow 提供了 Variable.assign_add()方法和 Variable.assign_sub()方法，代码如下。

```python
#自增 1
sess.run(my_var.assign_add(1))
#自减 1
sess.run(my_var.assign_sub(1))
```
