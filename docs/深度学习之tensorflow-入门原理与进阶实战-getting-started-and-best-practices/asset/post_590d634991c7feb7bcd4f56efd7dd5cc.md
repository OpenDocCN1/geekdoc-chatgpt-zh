
### 表 4-5 标量向量和矩阵的阶数

| rank | 实例 | 例子 |
|---|---|---|
| 0 | 标量(只有大小) | a = 1 |
| 1 | 向量(大小和方向) | b = [1,1,1,1] |
| 2 | 矩阵(数据表) | C = [[1,1],[1,1]] |
| 3 | 3阶张量(数据立体) | D = [[[1],[1]],[[1],[1]]] |
| n | n阶 | E = [[[[...[[1],[1]]]...]]] (n层中括号) |

### (3) shape（形状）

shape（形状）用于描述张量内部的组织关系。“形状”可以通过 Python 中的整数列表或元组（int list 或 tuples）来表示，也可以用 TensorFlow 中的相关形状函数来表示。
举例：一个二阶张量 _a_ = [[1, 2, 3], [4, 5, 6]]形状是两行三列，描述为（2,3）。

## 2. 张量相关操作

张量的相关操作包括类型转换、数值操作、形状变换和数据操作。

### (1) 类型转换

类型转换的相关函数如表 4-6 所示。

### 表 4-6 类型变换相关函数

| 函数 | 描述 |
|---|---|
| tf.string_to_number(string_tensor,out_type=None,name=None) | 字符串转为数字 |
| tf.to_double(x, name="ToDouble") | 转为64位浮点类型 |
| tf.to_float(x, name="ToFloat") | 转为32位浮点类型 |
| tf.to_int32(x, name="ToInt32") | 转为32位整型 |
| tf.to_int64(x, name="ToInt64") | 转为64位整型 |
| tf.cast(x,dtype,name=None) | 将x或者x.values转换为dtype所指定的类型。例如：W = tf.Variable(1.0) tf.cast(W, tf.int32) ===> W=1 # dtype=tf.int32 |

### (2) 数值操作

数值操作的相关函数如表 4-7 所示。

### 表 4-7 数值操作相关函数

| 函数 | 描述 |
|---|---|
| tf.ones(shape,dtype) | 按指定类型与形状生成值为1的张量。例如：tf.ones([2, 3], tf.int32)==> [[1 1 1] [1 1 1]] |
| tf.zeros(shape,dtype) | 按指定类型与形状生成值为0的张量。例如：tf.zeros([2, 3], tf.int32)==> [[0 0 0] [0 0 0]] |
| tf.ones_like(input) | 生成和输入张量一样形状和类型的1。例如：tensor=[[1, 2, 3], [4, 5, 6]] tf.ones_like(tensor) => [[1 1 1] [1 1 1]] |
| tf.zeros_like(input) | 生成和输入张量一样形状和类型的0。例如：tensor=[[1, 2, 3], [4, 5, 6]] tf.zeros_like(tensor) => [[0 0 0] [0 0 0]] |
| tf.fill(shape,value) | 为指定形状填值。例如：tf.fill([2,3],1) => [[1 1 1] [1 1 1]] |
| tf.constant(value, shape) | 生成常量。例如：tf.constant(1,[2,3]) => [[1 1 1] [1 1 1]] |
| tf.random_normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None) | 正态分布随机数，均值mean,标准差stddev |
| tf.truncated_normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None) | 截断正态分布随机数，均值mean,标准差stddev,只保留[mean-2*stddev,mean+2*stddev]范围内的随机数 |
| tf.random_uniform(shape,minval=0, maxval=None, dtype=tf.float32, seed=None, name=None) | 均匀分布随机数，范围为[minval,maxval] |
| tf.random_crop(value, size, seed=None, name=None) | 将输入值value按照size尺寸随机剪辑 |
| tf.set_random_seed(seed) | 设置随机数种子 |
| tf.linspace(start,stop,num,name=None) | 在[start,stop]范围内产生num个数的等差数列。注意，start和stop要用浮点数表示，否则会报错。例如：tf.linspace(start=1.0,stop=5.0,num=5,name=None) [ 1. 2. 3. 4. 5.] |
| tf.range(start,limit=None,delta=1,name='range') | 在[start,limit]范围内以步进值delta产生等差数列。注意，不包括limit在内的。例如：tf.range(start=1,limit=5,delta=1) [1 2 3 4] |

### (3) 形状变换

形状变换的相关函数如表 4-8 所示。

### 表 4-8 形状变换的相关函数

| 函数 | 描述 |
|---|---|
| tf.shape(input, name=None) | 返回一个张量，其值为输入参数input的shape。这个input可以是个张量，也可以是一个数组或list。例如：t=[1,2,3,4,5,6,7,8,9] print(np.shape(t)) #输出（9，）<br>tshape = tf.shape(t) #返回一个张量，值为python自有类型t的shape<br>tshape2 = tf.shape(tshape) #返回一个张量，值为张量tshape的shape<br>sess = tf.Session()<br>print(sess.run(tshape)) #输出[9], 表示t的shape的值<br>print(sess.run(tshape2)) #输出[1], 表示tshape的shape的值<br>t=[[[1, 1, 1], [2, 2, 2]], [[3, 3, 3], [4, 4, 4]]] |
| tf.size(input, name=None) | 返回一个张量，其内容为输入数据的元素数量。例如：<br>t=[[[1, 1, 1], [2, 2, 2]], [[3, 3, 3], [4, 4, 4]]]<br>sizet = tf.size(t)<br>sess = tf.Session()<br>print(sess.run(sizet)) #输出12, 表示列表t中的元素个数 |
| tf.rank(input, name=None) | 返回一个张量，其内容为输入数据input的rank。注意，此rank不同于矩阵的rank，详见4.2.1节中的rank介绍。例如：<br>t=[[[1, 1, 1], [2, 2, 2]], [[3, 3, 3], [4, 4, 4]]]<br>rankt = tf.rank(t)<br>sess = tf.Session()<br>print(sess.run(rankt)) #输出4, 表示列表t的阶（一共有4层中括号，[[[[]]]]) |
| tf.reshape(input, shape, name=None) | 将原有输入数据的shape按照指定形状进行变化，生成一个新的张量。例如：t=[1,2,3,4,5,6,7,8,9]<br>tt=tf.reshape(t,[3,3])<br>sess = tf.Session()<br>print(sess.run(tt))<br>#此时输出的张量如下：<br>#[[1, 2, 3],<br># [4, 5, 6],<br># [7, 8, 9]]<br>#如果shape有元素[-1]，则表示在该维度下按照原有数据自动计算。见下面的代码：<br>ttt=tf.reshape(tt, [1, -1])<br>print(ttt.shape) #输出 (1,9) 表示ttt的shape，9是自动计算得来的<br>print(tt.shape) #输出 (3,3) 表示tt并没有被修改 |
| tf.expand_dims(input, dim, name=None) | 插入维度1进入一个tensor中。例如：<br>t = [[2,3,3],[1,5,5]]<br>t1 = tf.expand_dims(t, 0)<br>t2 = tf.expand_dims(t, 1)<br>t3 = tf.expand_dims(t, 2)<br>t4 = tf.expand_dims(t, -1)<br>#如果写成t4 = tf.expand_dims(t, 3)，则会出错，因为只有两个维度<br>print(np.shape(t)) # 输出(2, 3)<br>print(np.shape(t1)) # 输出(1, 2, 3)<br>print(np.shape(t2)) # 输出(2, 1, 3)<br>print(np.shape(t3)) # 输出(2, 3, 1)<br>print(np.shape(t4)) # 输出(2, 3, 1) |
| tf.squeeze(input, dim, name=None) | 将dim指定的维度去掉（dim所指定的维度必须为1，如果不为1则会报错）。例如：<br>t = [[[1,2],[1,1]]]<br>t1 = tf.squeeze(t, 0)<br>t2 = tf.squeeze(t, 1)<br>t3 = tf.squeeze(t, 3)<br>t4 = tf.squeeze(t, -1)<br>#如果写成t4 = tf.squeeze(t, 2)会出错，因为2对应的维度为2，不为1<br>print(np.shape(t1)) # (1, 2, 1)<br>print(np.shape(t1)) # (1, 2, 1)<br>print(np.shape(t2)) # (1, 2, 1)<br>print(np.shape(t3)) # (1, 1, 2)<br>print(np.shape(t4)) # (1, 1, 2) |

### (4) 数据操作

数据操作的相关函数如表 4-9 所示。

### 表 4-9 数据操作相关函数

| 函数 | 描述 |
|---|---|
| tf.slice(input, begin, size, name=None) | 对输入数据input进行切片操作，begin与size可以为list类型。要求begin与size的值必须一一对应，并且begin中每个值都要大于等于0且小于等于size中对应的值。例如：<br>t=[[[1, 1, 1], [2, 2, 2]],[[3, 3, 3], [4, 4, 4]],[[5, 5, 5], [6, 6, 6]]]<br>slice1 = tf.slice(t, [1, 0, 0], [1, 1, 3])<br>slice2 = tf.slice(t, [1, 0, 0], [1, 2, 3])<br>slice3 = tf.slice(t, [1, 0, 0], [2, 1, 3])<br>sess = tf.Session()<br>print(sess.run(slice1)) #输出 [[[3 3 3]]]<br>print(sess.run(slice2)) #输出 [[[3 3 3] [4 4 4]]]<br>print(sess.run(slice3)) #输出 [[[3 3 3]] [[5 5 5]]] |
| tf.split(value, num_or_size_splits, axis=0, num=None, name="split") | 沿着某一维度将tensor分离为num_or_size_splits<br>Value是一个shape为[5, 30]的张量<br># 沿着第一列将value按[4, 15, 11]分成3个张量<br>split0, split1, split2 = tf.split(value, [4, 15, 11], 1)<br>tf.shape(split0) ==> [5, 4]<br>tf.shape(split1) ==> [5, 15]<br>tf.shape(split2) ==> [5, 11] |
| tf.concat(concat_dim, values, name='concat') | 沿着某一维度连接tensor<br>t1 = [[1, 2, 3], [4, 5, 6]]<br>t2 = [[7, 8, 9], [10, 11, 12]]<br>tf.concat([t1, t2], 0) ==> [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]]<br>tf.concat([t1, t2],1) ==> [[1, 2, 3, 7, 8, 9], [4, 5, 6, 10, 11, 12]]<br>如果想沿着Tensor新轴连接打包，则可以：<br>tf.concat(axis, [tf.expand_dims(t, axis) for t in tensors])<br>等同于tf.stack(tensors, axis=axis) |
| tf.stack(input, axis=0) | 将两个 N维张量列表沿着axis轴组合成一个沿着axis轴组合成一个N+1维的张量<br>tensor=[[1, 2, 3], [4, 5, 6]]<br>tensor2=[[10, 20, 30],[40, 50, 60]]<br>tf.stack([tensor, tensor2])<br>==>[[[ 1 2 3] [4 5 6]] [[10 20 30][40 50 60]]]<br>[[10 20 30][40 50 60]]]<br>tf.stack([tensor, tensor2], axis=1)<br>==>[[[ 1 2 3][10 20 30]]<br>[[ 4 5 6][40 50 60]]] |
| tf.unstack(value, num=None, axis=0, name="unstack") | 将输入value按照指定的行或列进行拆分，并输出含有num个元素的列表（list）<br>axis=0表示按行拆分， axis=1表示按列拆分。<br>num为输出list的个数，必须与预计输出的个数相等，否则会报错。可忽略这个参数<br>tensor=[[1, 2, 3], [4, 5, 6]]<br>tf.unstack(tensor) ==> [array([1, 2, 3]), array([4, 5, 6])]<br>tf.unstack(tensor,axis=1)<br>==>[array([1, 4]), array([2, 5]), array([3, 6])]<br>#tensor.shape=[2,3],axis=0,就是分成2个。 axis=1就是分成3个<br>#tensor.shape=[2,3,4],axis=2 就是分成4个 |
| tf.gather(params, indices, validate_indices=None, name=None) | 合并索引indices所指示params中的切片<br>y= tf.constant([0.,2.,-1.])<br>t = tf.gather(y, [2,0])<br>sess=tf.Session()<br>t2 = sess.run(t)<br>print(t2) #输出[array([-1., 0.], dtype=float32)] |
| tf.one_hot(indices, depth, on_value=None, off_value=None, axis=None, dtype=None, name=None) | 生成符合onehot编码的张量。<br>• indices： 要生成的张量。<br>• depth： 在 depth 长度的数组中，哪个索引的值为 onehot 值。<br>• on_value： 为 onehot 值时，该值为多少。<br>• off_value： 非 onehot 值时，该值为多少。<br>Axis 为-1时生成的shape为[indices长度， depth]，为0时 shape为[depth， indices长度]。还可以是1， 是指在类似时间序列（三维度以上）情况下，以时间序列优先而非batch优先， 即[depth,batch,indices长度]（这里的 indices长度可以当成样本中的feature特征维度）， 例如：<br>indices = [0, 2, -1, 1]<br>depth=3<br>on_value=5.0<br>off_value = 0.0<br>axis = -1<br><br>t=tf.one_hot(indices,depth,on_value,off_value,axis)<br>sess = tf.Session()<br>print(sess.run(t))<br><br>则输出如下:<br>[[ 5. 0. 0.] #0<br>[ 0. 0. 5.] #2<br>[ 0. 0. 0.] #-1<br>[ 0. 5. 0.]] #1 |
| tf.count_nonzero(input_tensor, axis=None, keep_dims=False, dtype=dtypes.int64, name=None, reduction_indices=None) | 统计非0个数 |

注意：TensorFlow 开头的代码都不能直接运行，必须放到 session 里面才可以。例如：

```
01 import numpy as np
02 import tensorflow as tf
03
04 x = tf.constant(2)
05 y = tf.constant(5)
06 def f1(): return tf.multiply(x, 17)
07 def f2(): return tf.add(y, 23)
08 r = tf.cond(tf.less(x, y), f1, f2)
09 print(r) #这样是错的
10
11 #生成两行三列的张量，值为 1
12 with tf.Session() as sess:
13 print(sess.run( r )) #这样才可以
```

## 4.2.2 算术运算函数

如表 4-10 中列出了 TensorFlow 关于算术运算方面的函数。

| 函数 | 描述 |
|---|---|
| tf.assign(x, y, name=None) | 令x=y |
| tf.add(x, y, name=None) | 求和 |
| tf.subtract(x, y, name=None) | 减法 |
