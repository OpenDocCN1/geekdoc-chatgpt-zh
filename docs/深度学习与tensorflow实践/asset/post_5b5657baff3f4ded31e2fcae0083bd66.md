
## 4.2.4 布尔类型

```
Out[23]:
<tf.Tensor: id=3, shape=(3, 3), dtype=string, numpy=
array([[b'en', b'eve', b'lve'],
       [b'hirt', b'urt', b'te'],
       [b'ixtee', b'vente', b'hteen']], dtype=object)>
```

从上面 In [23] 的操作可以看到，TensorFlow 中的字符串操作，其最大的特色是，它处理的对象通常是字符串类型张量（高维数组）。

由于深度学习模型的操作主要是以数值类型张量的运算为主，字符串类型张量使用频率相对较低，因此此处不做过多描述，读者在遇到相关问题时，可直接查询 TensorFlow 官方文档。

在 TensorFlow 中，经常会用到逻辑判断。例如，在分类算法中，在计算预测准确率时，会比较输出向量和预期向量（即标准答案）是否相等，这时就需要利用布尔类型张量来解决问题。

因此，TensorFlow 还支持布尔类型（Bool）张量。我们只需传入 Python 语言的布尔类型数据，TensorFlow 会将其自动转换为内部的布尔类型张量，例如：

```
In [24]: vec_bool = tf.constant([True, False])    # 创建布尔类型张量
In [25]: vec_bool
Out[25]:
<tf.Tensor: id=66, shape=(2,), dtype=bool, numpy=array([ True, False])>
```

与普通张量一样，布尔类型张量的优势主要体现在批量操作上。例如，我们想计算预测分类算法的准确率，而得到的原始数据是实际输出的张量（vec_actual）和预期值张量（vec_pre），这时我们利用布尔类型张量的批处理操作，即可非常方便地完成预测准确度的计算，如下代码所示。

```
In [26]: vec_actual = tf.constant([1,1,2,0,1])
    #假设为分类实际值
In [27]: vec_pre = tf.constant([1,0,1,0,1])
    #假设为算法预测的分类值
In [28]: acc_bool = vec_actual == vec_pre    #判断是否预测准确
In [29]: acc_bool    #输出布尔类型张量
Out[29]: <tf.Tensor: id=69, shape=(5,), dtype=bool, numpy=array( [ True, False,
False, True, True])>
```

所谓的准确率，是指预测的正确个数和预测的总个数之间的比值。
在 In [28]处，我们先用两个等号 “==” 做逻辑判断，让两个整数张量做比较，这时遵循的是“元素对元素（Element-Wise）”比较原则，比较的结果可能是 True（对应元素相等），也可能是 False（对应元素不等），它们在一起构成了布尔类型张量，其维度信息与原始的整数张量一致。然后将这个布尔张量赋值（=）给 acc_bool。在 In [29]处，我们验证了逻辑比较之后得到的布尔类型张量。

需要特别注意的是，不同于 NumPy，在 TensorFlow 中，布尔类型张量中的变量是不可以直接求和的。因此，需要将布尔类型张量 acc_bool 中的数据转换为浮点数再使用。这时我们就需要利用 tf.cast()方法，将布尔类型张量转换为浮点数类型（tf.float32）张量，这样做的话，False 被看成 0.0，True 被看成 1.0。于是，布尔类型张量[ True, False, False, True, True]被转换为浮点数类型张量[1.0, 0.0, 0.0, 1.0, 1.0]。

```
In [30]: acc_float = tf.cast(acc_bool, tf.float32) #请思考①
In [31]: tf.math.reduce_mean(acc_float)
Out[31]: <tf.Tensor: id=66, shape=(), dtype=float32, numpy=0.6>
```

当所有布尔类型张量都转换为“1.0”或“0.0”的浮点数类型张量时，利用 tf.math.reduce_mean 可以很容易计算出它们的平均值（代码 In [31]），这个平均值恰好就是准确率。如[1.0, 0.0, 0.0, 1.0, 1.0]，它表示共预测 5 次，有 3 次预测正确，正确率为 3/5=0.6。而在 tf.math.reduce_mean 方法的处理下，这个张量的“均值”恰恰好就是 0.6。

张量思维的核心是张量进，张量出 (Tensor In, Tensor Out).

从上面的操作可以看出，即使我们处理了很多数据的运算，但甚少涉及到 for 循环。利用 TensorFlow，就要求我们具备张量思维，即输入的是张量，输出的也是张量，我们要把张量当作一个整体看待，而不是考虑如何用 for 循环去遍历它们中的每个元素。

## 4.2.5 张量类型转换

有时，系统中不同计算模块使用的张量类型、数值精度都可能不尽相同。这时，为了适应调用模块的需要，就需要进行类型转换，这时需要用到的方法是 tf.cast()。这个方法，在前面的代码中，我们已经“牛刀小试”。

在英文中，“cast”有“铸造”之意。可以想象一下，火红滚烫的钢水流进磨具的场面，磨具是什么形态，钢水就“定格”为什么形态。后来，这个词被引进到计算机编程语言领域中，表示将一种形态的数据“变形”为另外一种形态的数据。tf.cast()方法的原型如下。

```
tf.cast(x, dtype, name=None)
```

在参数列表中，x 是要被强制进行类型转换的张量，dtype 是转换后的类型，name 表示这个操作的名称，通常不用。下面举例说明 tf.cast()方法的应用。

```python
In [32]: import numpy as np
In [33]: a_const = tf.constant(np.pi, dtype=tf.float16)
    # 创建 tf.float16 低精度张量
In [34]: a_double = tf.cast(a_const, tf.double)
    # 转换为双精度 tf.double 张量
In [35]: a_const
    # 输出验证
Out[35]: <tf.Tensor: id=346, shape=(), dtype=float16, numpy= 3.14>
In [36]: a_double
    # 输出验证
Out[36]: <tf.Tensor: id=347, shape=(), dtype=float64, numpy= 3.140625>
```

In [33]处 np.pi 表示 Numpy 定义的 π，默认这是一个双精度浮点数，如果想改变这个默认设置，就需要指定它的数据类型，这里数据类型为 tf.float16 低精度张量（单精度浮点数）。In [34]将这个低精度的浮点数转换成高精度的浮点数。In [35]和 In [36]分别“验明正身”，检测它们的类型信息。从上面的输出可以看出，张量的精度不同，保存的数据小数点位数也是不同的。

将张量从低精度张量转到高精度是安全的，但反过来，将高精度张量转换为低精度张量，则不然。这是因为，所谓的高精度是指张量需要的内存空间较大。例如，一个 tf.float16 类型的张量，需要 16 位的存储空间，而 tf.double 等同于 tf.float64，该类型的一个变量需要 64 位的存储空间。低精度的张量向高精度的张量转换，就好比把一个小房间换成大房间，原来住小房间的人，自然会“毫发未损”住进大房间。

反之，一个原来住大房间的人（好比高精度数据），如果住进小房间，即使人能勉强住进来了（数据的主体部分得以保留），那么这个人的物品肯定也得做出割舍（数据精度降低或溢出）。

TensorFlow 同样可以把布尔类型与数值型张量之间实施转换。遵循 Python 的传统，TensorFlow 把 True 当作 1 或 1.0，而 False 当作 0 或 0.0。反过来，我们也可以把数值类型转换为布尔类型，例如：

```python
In [37]: a_int = tf.constant([-1.1, 0, 1.0, 123, -100])
In [38]: b_bool = tf.cast(a_int, tf.bool)
In [39]: b_bool
Out[39]: <tf.Tensor: id=38, shape=(5,), dtype=bool, numpy= array([ True, False, True, True, True])>
```

从上面的输出可以看到，TensorFlow 遵循了它的“母语”——C/C++的规范：“非零即为真”，也就是说，只有“0”代表“False（假）”，其他所有数值，不论正负，统统都视为“True（真）”。

这里的“母语”是指，为了提升性能，TensorFlow 的大多数 API 函数是由 C/C++编写而成。

## 4.2.6 TensorFlow 中的张量与 NumPy 数组

从前面的分析可以看出，TensorFlow 与 NumPy 有着紧密的联系。我们知道，NumPy 是久负盛名的高性能计算和数据分析基础包。如果说，TensorFlow 的“面子”是做机器学习任务的，那么从第一性原理来看，它的“里子”还是做数值计算的。因此，如果 TensorFlow 不想重造轮子，自然就得充分借助经典的数值计算库——NumPy。事实上，TensorFlow 正是这么做的。

TensorFlow 中的很多数据类型，完全就是基于 NumPy 数据类型而设计的。我们可以在 Python 的交互模式下用以下代码验证一下。

```
In [1]:import tensorflow as tf
In [2]:import numpy as np
In [3]:np.int8 == tf.int8
Out[3]: True
In [4]:np.int16 == tf.int16
Out[4]: True
In [5]:np.int64 == tf.int64
Out[5]: True
```

从上面的测试结果可以看出，TensorFlow 的数据类型与 NumPy 的数据类型是一致的。因此，任何合法的 NumPy 数据类型都可以传递给 TensorFlow，而成为 TensorFlow 的各类张量，验证代码如下。

![](img/aa01495b859013c80481c58ec0894987_107_0.png)

```
01 import tensorflow as tf
02 import numpy as np
03
04 n0 = np.array(20, dtype = np.int32)
05 n1 = np.array([b"Tensor", b"flow", b"is", b"great"])
06 n2 = np.array([[True, False, False],
07     [False, True,False]],
08     dtype = np.bool)
09 tensor0D = tf.Variable(n0, name = "t_0")
10 tensor1D = tf.Variable(n1, name = "t_1")
11 tensor2D = tf.Variable(n2, name = "t_2")
12
13 print("tensor0D : {0}".format(tensor0D))
14 print("tensor1D : {0}".format(tensor1D))
15 print("tensor2D : {0}".format(tensor2D))
```

### 运行结果

```
tensor0D : <tf.Variable 't_0:0' shape=() dtype=int32, numpy=20>
tensor1D : <tf.Variable 't_1:0' shape=(4,) dtype=string, numpy= array([b'Tensor',
b'flow', b'is', b'great'], dtype=object)>
tensor2D : <tf.Variable 't_2:0' shape=(2, 3) dtype=bool, numpy=
array([[ True, False, False],
       [False, True, False]])>
```

### 代码分析

第 04 ~ 08 行分别定义了 NumPy 的 0D（标量）、1D（1 维）和 2D（2 维）张量 n0、n1 和 n2，然后在第 09 ~ 11 行，分别定义了 3 个 TensorFlow 变量 tensor0D、tensor1D 和 tensor2D，分别用 n0、n1 和 n2 作为它们的数据源。

然后，在第 13 ~ 15 行，分别运行并打印输出 tensor0D、tensor1D 和 tensor2D 变量的值，从运行结果可以看出，用 NumPy 张量对 TensorFlow 张量赋值是切实可行的。

值得一提的是，如果在 TensorFlow 1.x 时代，即使是简单的张量操作（第 13 ~ 15 行），也不甚繁琐，我们不得不先创建一个 Session，然后运行（run）它，在 Session 中完成数据的读取。好在我们已然身处 TensorFlow 2 时代，对这个流程做了大大简化，可以直接使用 Python 的 print 语句输出张量的信息。

但我们发现，TensorFlow 除输出来自 NumPy 的张量值外，它还会输出很多额外信息（如张量类型和尺寸等）。如果我们只想查看张量的值，该怎么办呢？如第三章所述，这时就需要使用张量的专有输出方法——tf.print()。

将范例 4-2 中的第 13 ~ 15 行改为如下 3 行代码。

```
13 tf.print(tensor0D)
14 tf.print(tensor1D)
15 tf.print(tensor2D)
```

再次运行范例 4-2，就可以得到我们想要的结果。

```
20
[["Tensor" "flow" "is" "great"]]
[[1 0 0]
 [0 1 0]]
```

前面我们提到，可以将 NumPy 数组轻易地转换成 TensorFlow 张量。那该如何将 TensorFlow 张量转换为普通 Python 程序可用的 NumPy 数组呢？

如果我们注意到 TensorFlow 张量输出的细节，就可以发现，每个 TensorFlow 张量对象都有一个 numpy()方法，它负责将 TensorFlow 张量转换为 Python 程序可用的 NumPy 数组。一旦运行范例 4-2 之后，范例中的变量已经在内存中准备就绪，然后我们可以在 Jupyter 或 IPython 环境下用如下代码测试。

```
In [6]: tensor0D.numpy() #将零维张量（标量）转换成 NumPy 数组
Out[6]: 20
In [7]: tensor1D.numpy() #将一维张量（即向量）转换成 NumPy 数组
Out[7]: array([b'Tensor', b'flow', b'is', b'great'], dtype= object)
In [8]: tensor2D.numpy() #将二维张量（即矩阵）转换成 NumPy 数组
Out[8]:
array([[ True, False, False],
       [False, True, False]])
```

显然，NumPy 数组并不是 TensorFlow 唯一的数据来源。当操作的数据并不是 NumPy 数组时，我们还可以利用 TensorFlow 提供的一个特殊方法 tf.convert_to_tensor，将不同的数据类型转换成符合要求的张量。例如，可以让普通的 Python 列表或元组转换成张量。请参见范例 4-3 中的代码。

### 范例 4-3 Python 数组与张量类型转换（tensor_convert.py）

```
01 import tensorflow as tf
02
03 a_list = list([1,2,3,])      #这是一个列表对象
04 b_tuple = (11.0, 22.2, 33)   #这是一个元组
05 c_str_tuple = "a", "b", "c", "d"  #这是一个（字符串）元组
06
07 tensor_a = tf.convert_to_tensor(a_list,dtype = tf.float32)
08 tensor_b = tf.convert_to_tensor(b_tuple)
09 tensor_c = tf.convert_to_tensor(c_str_tuple)
10 tensor_add = tf.math.add(tensor_a, tensor_b)
11
12 print(type(a_list))      #输出 Python 列表类型
13 print(type(tensor_a))   #输出 Python 列表转换的张量类型
14
15 print(type(b_tuple))      #输出 Python 元组类型
16 print(type(tensor_b))   #输出 Python 元组转换的张量类型
17 print(type(c_str_tuple))  #输出 Python 元组类型
18 print(type(tensor_c))   #输出 Python 元组转换的张量类型
19 print(type(tensor_add)) #输出求和结果的张量类型
20
21 tf.print(tensor_c)      #输出张量 tensor_c 中的数据
22 tf.print(tensor_add)      #输出张量求和后的数据
```

### 运行结果

```
<class 'list'>
<class 'tensorflow.python.framework.ops.EagerTensor'>
<class 'tuple'>
<class 'tensorflow.python.framework.ops.EagerTensor'>
<class 'tuple'>
```
