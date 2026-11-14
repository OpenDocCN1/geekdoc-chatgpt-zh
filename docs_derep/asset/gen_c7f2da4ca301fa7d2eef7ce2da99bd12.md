# NumPy 核心技法速通：运算、索引与广播机制实战

掌握 NumPy 的精髓，在于理解其向量化运算、灵活的索引切片以及强大的广播机制。本文通过一系列直观的代码示例，带你快速过一遍从基础向量点积到高级花式索引的核心用法。

---

## 向量点积：线性代数的基石

NumPy 中计算向量内积最直接的方式是 `dot` 方法。我们先定义两个一维数组 `a` 与 `s`，看看它们如何交互。

[PRE0]

a=[a1,a2,a3]

s=[s1,s2,s3]

c=a*s=a1*s1+a2*s2+a3*s3

[PRE1]python

a = np.array([1, 2, 3])

print("a=" + str(a))

s = np.array([4, 5, 6])

print("s=" + str(s))

b = a.dot(s)

print("a.dot(s)=" + str(b))

[PRE2]

1*4 + 2*5 + 3*6 = 32

[PRE3]

a=[1 2 3]

s=[4 5 6]

a.dot(s)=32

**解读**：`dot` 方法将对应位置元素相乘后求和，结果为标量 32。这是矩阵运算的基础，务必熟练掌握。

---

## 切片语法与数组基础操作

在深入复杂操作前，先回顾通用的切片语法 `array[start:end:step]`，它是数据提取的瑞士军刀。

[PRE4]

array[start:end:step]

接下来创建一个简单的一维数组演示基础打印。

[PRE5]python

a = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9])

[PRE6]python

print("a=" + str(a))

[PRE7]python

a = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9])

print("a=" + str(a))

---

## 布尔索引：数据筛选的利器

布尔索引（Boolean Indexing）允许我们用真假值掩码精准提取或修改数据。下面用一个包含 10 个元素的数组演示。

[PRE8]python

a = np.array([14, 8, 10, 11, 6, 3, 18, 13, 12, 9])

print("a=" + str(a))

mask = (a % 3 == 0)   # 建立布尔值数组

print("mask=" + str(mask))

利用生成的 `mask` 进行取值与赋值操作：

[PRE9]python

b = a[mask]    # 使用布尔值数组取出值

print("a[mask]=" + str(b))

a[a % 3 == 0] = -1    # 同时更改多个 True 索引

print("a[a%3==0]=-1->" + str(a))

[PRE10]

a=[14 8 10 11 6 3 18 13 12 9]

mask=[False False False False True True True False True True]

a[mask]=[ 6 3 18 12 9]

a[a%3==0]=-1->[14 8 10 11 -1 -1 -1 13 -1 -1]

**技巧**：`a[condition] = value` 这种写法极其高效，避免了显式循环，是数据清洗的常用手段。

---

## 广播机制：标量与数组的交互

NumPy 的广播机制让不同形状的数组运算成为可能。最简单的例子是数组与标量的四则运算。

[PRE11]python

a = np.array([[1, 2, 3], [4, 5, 6]])

print("a=")

print(a)

s = 5

print("s=" + str(s))

b = a + s

print("a+s=")

print(b)

b = a - s

print("a-s=")

print(b)

b = a * s

print("a*s=")

print(b)

b = a / s

print("a/s=")

print(b)

[PRE12]

a=

[[1 2 3]

 [4 5 6]]

s=5

a+s=

[[ 6  7  8]

 [ 9 10 11]]

a-s=

[[-4 -3 -2]

 [-1  0  1]]

a*s=

[[ 5 10 15]

 [20 25 30]]

a/s=

[[ 0.2  0.4  0.6]

 [ 0.8  1.   1.2]]

**注意**：标量 5 被“广播”到了矩阵 `a` 的每一个位置，逐元素完成运算，无需显式扩展维度。

---

## 同形数组的逐元素运算

当两个数组形状完全一致时，运算符直接作用于对应位置。

[PRE13]python

a = np.array([[1, 2], [3, 4]])

print("a=")

print(a)

s = np.array([[5, 6], [7, 8]])

print("s=")

print(s)

b = a + s

print("a+s=")

print(b)

b = a - s

print("a-s=")

print(b)

b = a * s

print("a*s=")

print(b)

b = a / s

print("a/s=")

print(b)

[PRE14]

a=

[[1 2]

 [3 4]]

s=

[[5 6]

 [7 8]]

a+s=

[[ 6 8]

 [10 12]]

a-s=

[[-4 -4]

 [-4 -4]]

a*s=

[[ 5 12]

 [21 32]]

a/s=

[[ 0.2 0.33333333]

 [ 0.42857143 0.5 ]]

**区别**：这里的 `*` 是**逐元素乘法**（Hadamard 积），而非矩阵乘法，这是新手极易混淆的点。

---

## 矩阵乘法：`dot` 的真正威力

对于二维数组，`dot` 执行标准的线性代数矩阵乘法（行乘列求和）。

[PRE15]python

a = np.array([[1, 2], [3, 4]])

print("a=")

print(a)

s = np.array([[5, 6], [7, 8]])

print("s=")

print(s)

b = a.dot(s)

print("a.dot(s)=")

print(b)

[PRE16]

a=

[[1 2]

 [3 4]]

s=

[[5 6]

 [7 8]]

a.dot(s)=

[[19 22]

 [43 50]]

**验证**：结果 `[[19, 22], [43, 50]]` 来源于 `[1*5+2*7, 1*6+2*8]` 等计算，这是深度学习中全连接层前向传播的核心数学操作。

---

## 重塑形状与二维数组构建

`arange` 配合 `reshape` 能快速生成连续整数矩阵。

[PRE17]python

a = np.arange(11, 36)

a = a.reshape(5, 5)

print("a=")

print(a)

也可以直接嵌套列表定义非方阵：

[PRE18]python

a = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]])

print ("a=")

print(a)

---

## 花式索引：想取哪行哪列就取哪行哪列

花式索引（Fancy Indexing）允许用整数数组作为索引，实现任意顺序、重复选取。

### 基础用法：坐标配对取值

传入行索引数组和列索引数组，NumPy 会按配对位置取值。

[PRE19]python

b = a[[0, 1, 2], [0, 1, 0]]           # 索引 [0, 0][1, 1][2, 0]

print ("a[[0, 1, 2], [0, 1, 0]]=")

print(b)

这等价于手动打包元组：

[PRE20]python

b = np.array((a[0, 0], a[1, 1], a[2, 0])) # 索引 [0, 0][1, 1][2, 0]

print ("np.array((a[0, 0], a[1, 1], a[2, 0]))")

print(b)

### 进阶：配合 `arange` 实现行固定、列变化

若想选取第 0 行第 0 列、第 1 行第 2 列、第 2 行第 0 列、第 3 行第 1 列，可以构造列索引配合 `arange` 生成的行索引。

[PRE21]python

idx = np.array([0, 2, 0, 1])

print ("idx=" + str(idx))

b = a[np.arange(4), idx]                # 索引 [0, 0][1, 2][2, 0][3, 1]

print ("a[np.arange(4), idx]=")

print(b)

这种技巧常用于提取每行特定位置的元素（如分类任务中取正确类别的 logits）。

### 就地修改：批量更新特定坐标

花式索引不仅能取值，还能直接赋值修改原数组。

[PRE22]python

a[np.arange(4), idx] += 10

print("a[np.arange(4), idx] += 10->")

print(a)

[PRE23]

a[np.arange(4), idx] += 10->

[[11 2 3]

 [ 4 5 16]

 [17 8 9]

 [10 21 12]]

**效果**：指定坐标处的值瞬间加 10，无需循环，性能极佳。

---

## 二维布尔掩码：条件筛选的矩阵版

布尔索引在二维数组上同样强大，生成同形掩码矩阵。

[PRE24]python

a = np.array([[1, 2], [3, 4], [5, 6]])

print("a=")

print(a)

[PRE25]

a=

[[1 2]

 [3 4]

 [5 6]]

构建条件 `a > 2`，得到布尔矩阵：

[PRE26]python

mask = (a > 2)

print("mask=")

print(mask)

[PRE27]

mask=

[[False False]

---

### 小结

从向量点积到矩阵乘法，从基础切片到花式索引与布尔掩码，NumPy 提供了一套表达力极强的数组编程范式。**熟练运用广播机制避免显式循环，善用花式索引处理非连续数据提取，是写出高性能、可读性强的科学计算代码的关键。**