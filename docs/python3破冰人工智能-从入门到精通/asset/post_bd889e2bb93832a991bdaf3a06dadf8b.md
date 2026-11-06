
输出：

```
[[1.2.]
[3.4.]]

[2.3.]

2.5

2.5

4.0

[1.753.75]

[0.50.5]

[0.250.25]

[[1.nan]
[3.4.]]

[0.0.25]
```

## 3.2.9 NumPy 排序、搜索

NumPy 支持对数组的排序与搜索操作，常见的搜索排序函数见表 3-6。

表 3-6 常用搜索排序函数

|  | 函数 | 说明 |
|---|---|---|
| 排序 | sort(a,axis,kind,order) | axis 参数默认值都为-1，即沿着数组的最后一个轴进行排序，不改变原始数组 |
|  | argsort(a,axis,kind,order) | 对数组 a 排序，返回一个排序后索引，a 不变 |
|  | lexsort(keys[,axis]) | 是间接排序，支持对数组按指定行或列的顺序排序；lexsort 不修改原数组，返回索引 |
| 搜索 | argmax(a[,axis,out]) | 找到数组 a 中最大值的下标，有多个最大值时得到第一个最大值的下标 |
|  | nanargmax(a[,axis]) | 找到数组 a 中最大值的下标，有多个最大值时得到第一个最大值的下标（不含 nan） |
|  | argmin(a[,axis,out]) | 找到数组 a 中最小值的下标，有多个最小值时得到第一个最小值的下标 |
|  | nanargmin(a[,axis]) | 找到数组 a 中最小值的下标，有多个最小值时得到第一个最小值的下标（不含 nan） |

### 1. 排序

（1）sort 排序

【例 3-39】sort 排序

输入：

```
a = np.array([[1,4],[3,1]])
```

```
```python
print(np.sort(a))      #沿着最后一个轴排序
print(np.sort(a, axis=None))      #折叠成一维的数组
print(np.sort(a, axis=0))      #沿着第一轴进行排序
```
```

输出：

```
```
[[1 4]
 [1 3]]

[1 1 3 4]

[[1 1]
 [3 4]]
```
```

输入：

```
```python
#针对一个结构化的数组，使用关键字顺序来指定一个字段排序
dtype = [('name', 'S10'), ('height', float), ('age', int)]
values = [('Arthur', 1.8, 41), ('Lancelot', 1.9, 38),('Galahad', 1.7, 38)]
a = np.array(values, dtype=dtype)  #create a structured array
print(np.sort(a, order='height'))
#Sort by age, then height if ages are equal
print(np.sort(a, order=['age', 'height']))
```
```

输出：

```
```
[(b'Galahad', 1.7, 38) (b'Arthur', 1.8, 41) (b'Lancelot', 1.9, 38)]

[(b'Galahad', 1.7, 38) (b'Lancelot', 1.9, 38) (b'Arthur', 1.8, 41)]
```
```

### (2) numpy.argsort 排序

对数组 a 排序，返回一个排序后索引，a 不变，使用 numpy.argsort，见例 3-40。

【例 3-40】numpy.argsort 排序

输入：

```
```python
x=np.array([3,1,2])
print(np.argsort(x))
y=np.array([[0,3],[2,2]])
print(np.argsort(y,axis=0))
print(np.argsort(y,axis=1))
#Sorting with keys
z = np.array([(1, 0), (0, 1)], dtype=[('x', '<i4'), ('y', '<i4')])
print(np.argsort(z, order=('x','y')))
print(np.argsort(z, order=('y','x')))
```
```

输出：

```
```
[1 2 0]
```

```
[[0 1]
[1 0]]

[[0 1]
[0 1]]

[1 0]

[0 1]
```

### （3）numpy.lexsort 排序

lexsort 支持对数组按指定行或列的顺序排序，是间接排序。lexsort 不修改原数组，返回数组索引。对应 lexsort 一维数组是 argsort，argsort 也不修改原数组，返回数组索引。默认按最后一行元素有小到大排序，返回最后一行元素排序后索引所在位置。

【例 3-41】numpy.lexsort 排序

输入：

```
#Sort two columns of numbers
a = [1,5,1,4,3,4,4] # First column
b = [9,4,0,4,0,2,1] # Second column
ind = np.lexsort((b,a)) # Sort by a, then by b
print(ind)
print([(a[i],b[i]) for i in ind])
print([(a[i],b[i]) for i in np.argsort(a)])
```

输出：

```
[2 0 4 6 5 3 1]

[(1, 0), (1, 9), (3, 0), (4, 1), (4, 2), (4, 4), (5, 4)]

[(1, 9), (1, 0), (3, 0), (4, 4), (4, 2), (4, 1), (5, 4)]
```

输入：

```
a=np.array([[ 2, 7, 4, 2],
            [35, 9, 1, 5],
            [22, 12, 3, 2]])
#按最后一列顺序排序
print(a[np.lexsort(a.T)])
#按最后一列逆序排序
print(a[np.lexsort(-a.T)])
#按第一列顺序排序
print(a[np.lexsort(a[:,::-1].T)])
#按最后一行顺序排序
print(a.T[np.lexsort(a)].T)
#按第一行顺序排序
print(a.T[np.lexsort(a[::-1,:])].T)
```

输出：

```
[[22 12  3  2]
 [ 2  7  4  2]
 [35  9  1  5]]

[[35  9  1  5]
 [ 2  7  4  2]
 [22 12  3  2]]

[[ 2  7  4  2]
 [22 12  3  2]
 [35  9  1  5]]

[[ 2  4  7  2]
 [ 5  1  9 35]
 [ 2  3 12 22]]

[[ 2  2  4  7]
 [ 5 35  1  9]
 [ 2 22  3 12]]
```

### 2. 搜索

检索数组中最大值与最小值所在的下标，用 argmax()和 argmin()可以求最大值和最小值的下标。如果不指定 axis 参数，则返回平坦化之后的数组下标。

【例 3-42】搜索

输入：

```
a = np.arange(6).reshape(2,3)
print(np.argmax(a))
print(np.argmax(a, axis=0))
print(np.argmax(a, axis=1))
b = np.arange(6)
b[1] = 5
print(np.argmax(b))    #得到第一个最大值下标
c= np.array([[np.nan, 4], [2, 3]])
print(np.argmax(a))
print(np.nanargmax(a))
print(np.nanargmax(a, axis=0))
print(np.nanargmax(a, axis=1))
```

输出：

```
5
[1 1 1]
[2 2]
1
5
5
[1 1 1]
[2 2]
```

## 3.2.10 NumPy 数据的保存

NumPy 模块下保存的数据有 np.loadtxt 和 np.savetxt 可以读写一维和二维数组，如果想将多个数组保存到一个文件中，可以用 numpy.savez 函数。本书主要介绍 np.load 和 np.save 如何将数组以二进制格式保存到磁盘。np.load 和 np.save 是读写磁盘数组数据的两个主要函数，默认情况下，数组是以未压缩的原始二进制格式保存在扩展名为.npy 的文件中，见例 3-43。

【例 3-43】NumPy 保存操作

输入：

```
```
A = np.arange(15).reshape(3,5)
#在当前目录下保存 A
np.save("A.npy",A)    #如果末尾没有扩展名.npy, 则该扩展名会被自动加上
B=np.load("A.npy")
print(B)
```
```

输出：

```
```
[[ 0  1  2  3  4]
 [ 5  6  7  8  9]
 [10 11 12 13 14]]
```
```

注：保存为 NumPy 专用的二进制格式后，就不能用 notepad++等工具查看文件内容了，会出现乱码，因此建议在不需要看保存文件内容的情况下使用这种方式。

## 第 4 章 常用科学计算模块快速入门

![](img/b804a95e802d3360c99b6f8e75c7d047_94_0.png)

为什么 Python 适合科学计算？
科学计算领域最流行的软件当属 Matlab，而 Python 是一种面向对象的、动态的程序设计语言，它具有非常简洁而清晰的语法，适合完成各种复杂任务。
并且，随着 NumPy、Pandas、SciPy、Matplotlib 等众多程序库的发布和发展，Python 越来越适合做科学计算。它既可以用来快速开发程序脚本，也可以用来开发大规模的软件。

## 4.1 Pandas 科学计算库

## 4.1.1 初识 Pandas

Pandas 由 AQR Capital Management 于 2008 年开发，并于 2009 年底开源发布，目前由专注于 Python 数据包开发的 PyData 开发团队继续开发和维护。本书中使用的版本是 Pandas-0.22.0。Pandas 基于 NumPy 开发，提供了大量快速便捷的数据处理方法，对数据处理工作十分有用，它是支撑 Python 成为强大而高效的科学计算语言的重要因素之一。

【例 4-1】查看 Pandas 版本

输入：

```
import pandas as pd
print(pd.__version__)
```

输出：

```
0.22.0
```

【例 4-2】初识 Pandas 操作

输入：

```
dates = pd.date_range("20130101",periods=6)
df = pd.DataFrame(np.random.rand(6,4),index=dates,columns=list
("ABCD"))
#1.获取数据
print("获取df数据:\n{}".format(df))
#2.观察数据
print("获取前两行数据:\n{}".format(df.head(2)))
print("获取后两行数据:\n{}".format(df.tail(2)))
#3.查看属性和原始ndarray
print("获取数据结构中索引:\n{}".format(df.index))
print("获取维度基本属性:\n{}".format(df.shape))
print("获取数据结构中的实际数据:\n{}".format(df.values))
print("获取数据结构中 A列的实际数据:\n{}".format(df[["A"]].values))
#4.描述统计量
print("描述统计量:\n{}".format(df.describe()))
```

输出：

```
获取 df 数据：
           A       B       C       D
2013-01-01  0.020064 0.835501 0.441907 0.180441
2013-01-02  0.331319 0.698038 0.019154 0.351405
2013-01-03  0.366511 0.204791 0.047254 0.446674
2013-01-04  0.570611 0.225375 0.043786 0.802882
2013-01-05  0.608821 0.375899 0.010477 0.259417
2013-01-06  0.394449 0.189670 0.450094 0.700865
获取前两行数据：
           A       B       C       D
2013-01-01  0.020064 0.835501 0.441907 0.180441
2013-01-02  0.331319 0.698038 0.019154 0.351405
获取后两行数据：
           A       B       C       D
2013-01-05  0.608821 0.375899 0.010477 0.259417
2013-01-06  0.394449 0.189670 0.450094 0.700865
获取数据结构中索引：
DatetimeIndex(['2013-01-01', '2013-01-02', '2013-01-03', '2013-01-04',
               '2013-01-05', '2013-01-06'],
              dtype='datetime64[ns]', freq='D')
获取维度基本属性：
(6, 4)
获取数据结构中的实际数据：
[[0.0200644  0.83550056 0.44190724 0.18044119]
 [0.33131098 0.69803846 0.01915373 0.35140474]
 [0.36651099 0.20479141 0.04725439 0.4466739 ]
 [0.5706111  0.22537529 0.04378595 0.8028824 ]
 [0.6088207  0.37589875 0.01047748 0.25941727]
 [0.39444884 0.18966954 0.45009361 0.70086478]]
获取数据结构中 A 列的实际数据：
[[0.0200644 ]
[0.33131908]
[0.36651099]
[0.5706111 ]
[0.6088207 ]
[0.39444884]
```

描述统计量：

|  | A | B | C | D |
|---|---|---|---|---|
| count | 6.000000 | 6.000000 | 6.000000 | 6.000000 |
| mean | 0.381963 | 0.421546 | 0.168779 | 0.456947 |
| std | 0.210230 | 0.278970 | 0.215209 | 0.247340 |
| min | 0.020064 | 0.189670 | 0.010477 | 0.180441 |
| 25% | 0.340117 | 0.209937 | 0.025312 | 0.282414 |
| 50% | 0.380480 | 0.300637 | 0.045520 | 0.399039 |
| 75% | 0.526571 | 0.617504 | 0.343244 | 0.637317 |
| max | 0.608821 | 0.835501 | 0.450094 | 0.802882 |

在例4-2中，我们利用NumPy构建了一个Pandas下的DataFrame对象，并对其进行了一些基本操作，以方便读者对Pandas的理解。可以看出，Pandas在数据预处理与数据挖掘过程中起着非常重要的作用。下面我们将根据Pandas官方文档中的众多功能，结合实际操作，选取常用操作进行介绍。

## 4.1.2 Pandas 基本操作

### 1. Pandas 中的数据结构

目前，Pandas中的数据结构有3种：Series、DataFrame和Panel。如表4-1所示，本书主要围绕着Series和DataFrame这两个核心数据结构展开。

表 4-1 Pandas 中的数据结构

| 数据结构 | 维度 | 轴标签 |
|---|---|---|
| Series | 一维 | index（唯一的轴） |
| DataFrame | 二维 | index（行）和 columns（列） |
| Panel | 三维 | items、major_axis 和 minor_axis |

### 2. Series 数据结构

Series是Pandas中最基本的对象，它定义了NumPy的ndarray对象的接口__arrat__()，因此可以利用NumPy的数组处理函数直接处理Series对象。它是由相同元素类型构成的一维数据结构，同时具有列表和字典的属性（字典的属性由索引赋予）。

Series的基本创建方式为：

```
pd.Series(data=None, index=None)
```

- data: 传入数据，可以传入多种类型。
- index: 索引，在不指定 index 的情况下，默认值索引 range(0,len(data))。

【例 4-3】创建 Series

输入：

```
data=[0,1,2]
index=["a","b","c"]
s=pd.Series(data=data,index=index)
print("s=\n{}".format(s))
print("s.index={}".format(s.index))
print("s.values={}".format(s.values))
print("s.dtype={}".format(s.dtype))
s1=pd.Series(data=data)
print("不指定 index 情况下的 Series,s1=\n{}".format(s1))
data1={"a":0,"b":1,"c":2}
s2=pd.Series(data=data,index=index)
print("传入 data 为字典, key 会被解析为 index, value 会被解析为 data, s2=\n{}".format(s2))
```

输出：

```
s=
a    0
b    1
c    2
dtype: int64
s.index=Index(['a', 'b', 'c'], dtype='object')
s.values=[0 1 2]
s.dtype=int64
不指定 index 情况下的 Series,s1=
0    0
1    1
2    2
dtype: int64
传入 data 为字典, key 会被解析为 index, value 会被解析为 data, s2=
a    0
b    1
c    2
dtype: int64
```

### 3. DataFrame 数据结构

DataFram 是表格型的数据结构，它含有有一组有序的列，每列可以是不同的值类型（数值、字符串、布尔值等）。DataFrame 既有行索引，也有列索引，它可以被看成由 Series 组成的字典（共用同一个索引）。

DataFrame 的基本创建方式为：

```
pd.DataFrame(data=None, index=None, columns=None)
```
