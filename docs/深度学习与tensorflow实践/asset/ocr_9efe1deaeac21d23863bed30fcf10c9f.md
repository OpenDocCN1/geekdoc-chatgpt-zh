# 4.2.6 TensorFlow 中的张量与 NumPy 数组

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

![](bbox=[0.301, 0.553, 0.801, 0.572])

```
01 import tensorflow as tf
02 import numpy as np
03
04 n0 = np.array(20, dtype = np.int32)
05 n1 = np.array(["b"Tensor", b"flow", b"is", b"great"])
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

# 运行结果

tensor0D : <tf.Variable 't_0:0' shape=() dtype=int32, numpy=20>
tensor1D : <tf.Variable 't_1:0' shape=(4,) dtype=string, numpy= array([b'Tensor',
b'flow', b'is', b'great'], dtype=object)>