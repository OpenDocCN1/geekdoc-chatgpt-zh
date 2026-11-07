Python 已有列表类型，为什么需要一个数组对象？如图 3-3 所示，NumPy 更好地支持数学运算，同样做乘法运算，列表是把元素复制了一遍，而数组是对每个元素做了乘法。列表存储的是一维数组，而数组则能存储多维数据。比如列表 e 虽然包含 3 个小表，但结构是一维，而数组 f 则是 3 行 2 列的二维结构。

```
import numpy as np
a = np.array([1,2,3])
print (a)

[1 2 3]
```

图 3-2

```
import numpy as np
a=[1,2,3,4]
b=np.array([1,2,3,4])
print(a)
print(b)
c=a*2
d=b*2
print(c)
print(d)
e=[[1,2],[3,4],[5,6]]
f=np.array([[1,2],[3,4],[5,6]])
print(e)
print(f)

[1, 2, 3, 4]
[1 2 3 4]
[1, 2, 3, 4, 1, 2, 3, 4]
[2 4 6 8]
[[1, 2], [3, 4], [5, 6]]
[[1 2]
 [3 4]
 [5 6]]
```

图 3-3

# 3.2.2 创建数组的方法

创建数组的方法有两种：

方法一：通过列表来创建数组，如图 3-4 所示，创建一维数组 a1 及列表 lst1，将列表 lst1 转换成为 ndarray。

方法二：使用 NumPy 中的函数创建 ndarray 数组，如 arange、ones、zeros 等。如图 3-5 所示，使用 np.arange(n) 函数（第一个参数为起始值，第二个参数为终止值，第三个参数为步长）创建数组；使用 np.linspace() 根据终止数据等间距地填充数据，形成数组。NumPy 中还有一些常用的用来产生随机数的函数，如 randn() 和 rand()。

```
import numpy as np
a1=np.array([1,2,3,4])
lst1=[3.14,2.17,0,1,2]
a2=np.array([[1,2],[3,4],[5,6]])
a3=np.array(lst1)
print(a2)
print(a3)

[[1 2]
 [3 4]
 [5 6]]
[3.14 2.17 0. 1. 2.]
```

图 3-4

```
x1=np.arange(12)
x2=np.arange(5,10)
x3=np.random.randn(2,3)
x4=np.random.random([3,3])
x5=np.ones((2,4))
x6=np.zeros([3,4])
x7=np.linspace(2,8,3, dtype=np.int32)
print(x1)
print(x2)
print(x3)
print(x4)
print(x5)
print(x6)
print(x7)

[0 1 2 3 4 5 6 7 8 9 10 11]
[5 6 7 8 9]
[-1. 0. 0. ...]
[[0. 0. 0.]
 ...]
[[1. 1. 1. 1.]
 [1. 1. 1. 1.]]
[[0. 0. 0. 0.]
 ...]
[2 5 8]
```

图 3-5

# 3.2.3 NumPy 的算术运算

NumPy 最强大的功能便是科学计算与数值处理。比如有一个较大的列表，需要将每一个元素的值都变为原来的十倍，NumPy 的操作就比 Python 要简单得多。