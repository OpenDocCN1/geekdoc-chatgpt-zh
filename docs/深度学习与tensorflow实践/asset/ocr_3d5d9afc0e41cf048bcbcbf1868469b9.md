考虑到这种组合方法比较常见，TensorFlow 提供了这两种操作“合二为一”方法——堆叠，对应的方法是 tf.stack(tensors, axis)。该方法可以同时以堆叠的方式合并多个张量（数量大于或等于 2），同时创建一个新的维度来区分合并前的张量。

需要注意的是，堆叠操作是有限制条件的，它操作的张量尺寸必须完全一致，否则编译器会报错。

在该方法中，通过多个张量用列表表示参数 axis 指定新维度插入的位置，axis 的用法与前面提及到的 tf.expand_dims()的用法一致，axis = n 的含义为：当 axis ≥ 0 时，在 axis 之前插入新维度。如当 axis = 0 时，表示在原来张量的第 0 个轴前添加一个新维度；当 axis < 0 时，在 axis 之后插入新维度；当 axis = -1 时，表示在原来张量的倒数第 1 个轴之后插入一个新的维度。

若我们按照前面张量 A 和张量 B 的值进行堆叠，则会发生以下错误。

```
In [11]: tf.stack([A,B], axis = 0).shape
InvalidArgumentError: Shapes of all inputs must match: values[0].shape = [4,32,8] ! = values[1].shape = [6,32,8] [Op:Pack] name: stack
```

发生错误的原因很简单，即张量 A 与张量 B 的尺寸不一致。堆叠操作就好像“垒砖头”一样，所有砖头必须大小一致才行。

```
In [12]: B = tf.random.normal([4,32,8]) #重新定义张量 B
In [13]: tf.stack([A ,B], axis = 0).shape #张量 A 与张量 B 的尺寸一致
Out[13]: TensorShape([2, 4, 32, 8])
```

从上面的输出可以看到，除张量合并成功外，而且还自动在原有张量第 0 个轴之前添加了一个新维度。我们可以很容易利用下标访问合并后的张量，代码如下所示。

```
In [14]: tf.stack([A ,B], axis = 0)[0] == A
<tf.Tensor: shape=(4, 32, 8), dtype=bool, numpy=
array([[[ True, True, True, ..., True, True, True],
...,
[ True, True, True, ..., True, True, True]]])
```

从上面的输出可以看出，tf.stack([A ,B], axis = 0)[0]表示合并张量后的第 0 个分量，它就等价于原有张量 A。类似地，tf.stack([A ,B], axis = 0)[1]就是张量 B。从上面的分析可知，增加维度可以更为轻松地操作数据。

# 4.7.2 张量分割

合并操作的逆过程就是分割，即将一个张量分拆为多个张量。我们还以学生成绩为例，假设我们得到整个学校的学生成绩张量，其尺寸为[10, 35, 8]，表示的含义是有 10 个班，每个班有 35 名学生，每名学生都选了 8 门课。