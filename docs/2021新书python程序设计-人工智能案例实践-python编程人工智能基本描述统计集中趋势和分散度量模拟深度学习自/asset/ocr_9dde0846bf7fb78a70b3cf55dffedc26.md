# 7.3 数组属性

数组对象提供的属性使读者能够发现关于其结构和内容的信息。
在本节中，我们将使用以下数组：

```
In [1]: import numpy as np

In [2]: integers = np.array([[1, 2, 3], [4, 5, 6]])

In [3]: integers
Out[3]:
array([[1, 2, 3],
       [4, 5, 6]])

In [4]: floats = np.array([0.0, 0.1, 0.2, 0.3, 0.4])

In [5]: floats
Out[5]: array([ 0. ,  0.1,  0.2,  0.3,  0.4])
```

NumPy在浮点值中不显示小数点右侧尾随的0。

# 确定数组的元素类型

array函数根据参数的元素类型确定数组的元素类型。可以用数组
的dtype属性检查元素类型：

```
In [6]: integers.dtype
Out[6]: dtype('int64') # int32 on some platforms

In [7]: floats.dtype
Out[7]: dtype('float64')
```

在下一节中，将通过配置各种数组创建函数的dtype关键字参数，
来指定数组的元素类型。

出于性能原因，NumPy是用C编程语言编写的，并使用C的数据类
型。默认情况下，NumPy把整数存储为NumPy的int64类型—对应C语言
的64位整数（8字节），把浮点数存储为NumPy的float64类型—对应C
语言的64位（8字节）浮点值。在我们的例子中，通常会看到到int64类
型、float64类型、bool（布尔）类型以及非数值数据的对象（如字符
串）类型。NumPy支持的完整的类型列表可在网址
https://docs.scipy.org/doc/numpy/user/basics.types.html上查
看。