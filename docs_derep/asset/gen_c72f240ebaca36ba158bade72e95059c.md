# TensorFlow 核心编程模型：张量、图与会话实战解析

掌握 TensorFlow 的第一步，是理解其“定义图 -> 执行图”的延迟执行机制。本文将通过代码实例，带你拆解张量运算、计算图管理、会话生命周期以及数据喂入等核心环节。

## 基础张量运算与操作节点

一切从数据开始。我们先用 NumPy 创建两个整型数组，再借助 TensorFlow 完成加法运算。

[PRE0]

```python
import tensorflow as tf
import numpy as np

# 准备底层数据
a = np.array([2, 3], dtype=np.int32)
b = np.array([4, 5], dtype=np.int32)

# 构建加法操作节点，c 指向该节点的输出张量
c = tf.add(a, b)
```

为操作节点命名是良好的工程习惯，方便后续在 TensorBoard 中可视化调试。

[PRE1]

```python
# 为操作指定唯一标识符
c = tf.add(a, b, name="my_add_op")
```

得益于运算符重载，TensorFlow 允许我们像操作原生 Python 对象一样直接对张量做算术运算，前提是形状兼容。

[PRE2]

```python
# 假设 a、b 均为张量且维度一致
c = a + b
```

## 计算图：隔离与管理计算逻辑

TensorFlow 1.x 时代，计算图是承载所有计算逻辑的容器。显式创建图对象能有效隔离不同模型的命名空间。

[PRE3]

```python
import tensorflow as tf

# 实例化一个独立的计算图容器
g = tf.Graph()
```

通过上下文管理器 `as_default()`，我们可以将后续定义的操作自动归属到指定图中。

[PRE4]

```python
with g.as_default():
    # 此处定义的乘法操作将被注册入图 g
    a = tf.multiply(2, 3)
    # ... 更多操作构建
```

若不指定图上下文，操作默认落入全局默认图。下面的示例展示了如何区分“默认图”与“自定义图”中的节点归属。

[PRE5]

```python
# 该加法节点属于默认图
in_default_graph = tf.add(1, 2)
```

[PRE6]

```python
# 进入图 g 的上下文
with g.as_default():
    # 该乘法节点属于图 g
    in_graph_g = tf.multiply(2, 3)
```

[PRE7]

```python
# 退出 with 块后，回到默认图上下文
# 此减法节点再次归属默认图
also_in_default_graph = tf.sub(5, 1)
```

获取当前线程的默认图对象，可用 `tf.get_default_graph()`。

[PRE8]

```python
default_graph = tf.get_default_graph()
```

### 多图共存与切换策略

实际项目中常需同时维护多个图（如训练图与推理图）。以下三种模式演示了不同的管理方式。

**模式一：两个全新的自定义图**

[PRE9]

```python
import tensorflow as tf

g1 = tf.Graph()
g2 = tf.Graph()

with g1.as_default():
    # 构建 g1 的拓扑结构
    ...

with g2.as_default():
    # 构建 g2 的拓扑结构
    ...
```

**模式二：复用默认图 + 创建新图**

[PRE10]

```python
import tensorflow as tf

g1 = tf.get_default_graph()  # 捕获当前默认图
g2 = tf.Graph()              # 创建新图

with g1.as_default():
    # 向默认图添加节点
    ...

with g2.as_default():
    # 向新图添加节点
    ...
```

**模式三：默认图隐式构建 + 显式切换新图**

[PRE11]

```python
import tensorflow as tf

g2 = tf.Graph()

# 此处代码隐式写入默认图
...

with g2.as_default():
    # 显式切换至 g2 构建节点
    ...
```

## 会话：驱动图执行的运行时引擎

定义好图后，必须通过 `Session` 来分配资源、执行计算。

[PRE12]

```python
import tensorflow as tf

# 构建默认图上的计算链：a=7, b=21
a = tf.add(2, 5)
b = tf.multiply(a, 3)
```

### 会话的创建与关联

会话默认绑定当前线程的默认图，也可显式指定。

[PRE13]

```python
# 绑定默认图启动会话
sess = tf.Session()
```

[PRE14]

```python
# 等价的显式绑定写法
sess = tf.Session()
sess = tf.Session(graph=tf.get_default_graph())
```

### 执行计算与获取结果

`run()` 方法是触发计算的入口，支持单输出或多输出获取。

[PRE15]

```python
# 单节点求值，输出 21
sess.run(b)
```

[PRE16]

```python
# 批量求值，返回列表 [7, 21]
sess.run([a, b])
```

### `feed_dict`：运行时注入外部数据

利用 `feed_dict` 可在会话运行时临时替换图中张量的值，这是实现动态输入的关键。

[PRE17]

```python
import tensorflow as tf

# 定义计算图结构
a = tf.add(2, 5)
b = tf.multiply(a, 3)

sess = tf.Session()

# 构造喂入字典：将张量 a 的值强制替换为 15
replace_dict = {a: 15}

# 执行时注入数据，b = 15 * 3 = 45
sess.run(b, feed_dict=replace_dict)
```

### 会话资源的优雅释放

会话持有 GPU/CPU 资源，用完必须关闭。推荐使用上下文管理器自动托管生命周期。

**手动关闭模式（易忘、易泄露）**

[PRE18]

```python
sess = tf.Session()
try:
    # 执行训练、统计等逻辑
    ...
finally:
    # 确保资源释放
    sess.close()
```

**上下文管理器模式（推荐，自动异常安全）**

[PRE19]

```python
with tf.Session() as sess:
    # 块内自动绑定会话
    # 执行计算逻辑
    ...
# 块结束自动调用 sess.close()
```

### `InteractiveSession` 与 `eval()` 交互式用法

在 Jupyter Notebook 等交互环境中，可将会话设为默认，直接调用张量的 `eval()` 方法求值。

[PRE20]

```python
# 定义常量
a = tf.constant(5)

sess = tf.Session()

# 将 sess 设为当前上下文的默认会话
with sess.as_default():
    # 直接求值，无需显式 sess.run()
    print(a.eval())

# 手动关闭会话
sess.close()
```

## 占位符：构建动态输入管道的入口

`tf.placeholder` 是图定义阶段的“占位符”，不包含实际数据，仅定义数据类型与形状约束，运行时通过 `feed_dict` 灌入真实数据。

[PRE21]

```python
import tensorflow as tf
import numpy as np

# 定义一个 shape=(2,), dtype=int32 的占位符
# name 参数便于图可视化识别
a = tf.placeholder(tf.int32, shape=[2], name="my_input")
```

---

**总结**：TensorFlow 1.x 的核心工作流是**构建图（定义张量/操作/占位符） -> 启动会话（绑定图/分配资源） -> 运行计算（run/eval + feed_dict） -> 释放资源**。理解图与会话的分离，是驾驭静态图模式的关键。