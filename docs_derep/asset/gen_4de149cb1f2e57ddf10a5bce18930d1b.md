### 2.3.2 map

map 是 Python 提供的函数式编程工具，调用形式为 map(func, *iterables)。其中第一个位置接收一个函数对象，第二位置接收一个或多个可迭代对象，比如列表。函数会被依次作用到序列的每个元素上。Python 2 时期 map 会直接给出列表结果，Python 3 改为返回惰性迭代器。

[PRE0]

例 2-21 用的是最基础的情形，定义 f(x)=x² 后让 map 对 [1, 2, 3, 4, 5, 6, 7, 8, 9] 进行处理，得到的仍是列表形式，运行机理可参考图 2-8。

### 【例 2-21】map 示例

输入：

[PRE1]

输出：

[PRE2]

map 也可以同时消费多个序列，序列长度不必相等。它会把 lambda 作用到各序列的对应项上，先取索引 0 的一组，再取索引 1 的一组，直到遍历结束，具体可看例 2-22。

### 【例 2-22】 map 应用于多个列表

输入：

[PRE3]

输出：

[PRE4]

当某一个序列的元素比其他序列少时，map 会在最短序列耗尽的瞬间停止，例 2-23 正是这种情形。

### 【例 2-23】 map 应用于多个列表不同元素情况

输入：

[PRE5]

输出：

[PRE6]

把例 2-22 与例 2-23 的结果对照，ac 只产出了 3 个元素，而 ab 产出了 4 个。这说明 map 的终止条件由最短的输入序列决定。

### 2.3.3 filter

filter 同样是 Python 的高阶函数，用于函数式编程。通用写法是 filter(function, iterable)，它会筛掉不满足条件的项，保留符合条件的子序列，是一种很优雅的过滤方式。

例 2-24 以前 11 个斐波那契数为例，演示如何分别取出奇数和偶数。

### 【例 2-24】 filter 示例

输入：

[PRE7]

输出：

[PRE8]

### 小贴士：

Python 3 把 reduce 从内置命名空间移除了，Python 2 里它是内置的高阶函数。Python 3 下要使用 reduce 需要先从 functools 导入，下面的代码展示了对序列元素做累积运算的用法。

[PRE9]

输出： 102

# 第 3 章 Python 科学计算库 NumPy

[PRE10]

为什么要先学习 NumPy？

NumPy 是开源的 Python 科学计算基石，Pandas、Scikit-learn 等众多科学计算库都直接依赖其功能。

## 3.1 NumPy 简介与安装

### 3.1.1 NumPy 简介

NumPy 专为快速处理大规模矩阵而设计，支持在 Python 中进行向量和矩阵运算，大量底层实现使用 C 语言编写，性能远超纯 Python。数组内元素类型统一，也省去了运行时的类型检查开销。本书示例基于 NumPy 1.13.1。

### 【例 3-1】查看 NumPy 版本

输入：

[PRE11]

输出：

[PRE12]

### 3.1.2 NumPy 安装

> 小贴士：
> 什么是科学计算？
> 科学计算聚焦于定量分析、数学建模以及借助计算机解决科学问题的研究。

NumPy 建立在 Python 之上，安装 NumPy 前必须先装 Python。当前推荐 Anaconda，Anaconda 是面向科学计算的 Python 发行版，覆盖 Linux、Mac、Windows，内置大量数据分析包。已装 Anaconda 即视为已装好 NumPy。

若直接从官网装 Python，Windows、各 Linux 发行版以及 Mac OS X 均提供 NumPy 二进制包，也可装源码版。系统要求 Python 2.4.x 及以上，通用安装命令为 pip install numpy。

### 小贴士：

Python 通过 import 关键字引入模块，可把某个包中的函数或子模块导入到当前代码，实现复用，例如 import numpy。

## 3.2 基本操作

NumPy 提供了大量函数，目的都是让用户更直观高效地使用，涵盖数组元素的选取、多项式运算等。

### 3.2.1 初识 NumPy

NumPy 核心是同质多维数组，即同一类型元素的表格，可用正整数元组索引。数组的维度叫做轴，轴的个数叫做秩。比如三维点 [1,2,1] 是秩为 1 的数组，轴长度为 3。

NumPy 数组类型为 ndarray，又称 array。与标准库 array.array 不同，后者仅支持一维且功能有限。下面是 ndarray 的常用属性，见表 3-1。

表 3-1 ndarray 对象属性

| 属性 | 含义 |
|---|---|
| T | 转置，等价于 self.transpose()，低维返回自身 |
| size | 元素总数，等于 shape 各维乘积 |
| itemsize | 单个元素字节数，如 float64 为 8 |
| dtype | 元素数据类型对象，可用 Python 类型或 numpy 类型指定 |
| ndim | 轴的数量，亦称 rank |
| shape | 各维大小的整数元组，如 n 行 m 列为 (n, m) |
| data | 存放实际元素的缓冲区，通常直接用索引访问 |
| flat | 一维迭代器视图 |
| imag | 虚部 |
| real | 实部 |
| nbytes | 所有元素占用的总字节数 |

导入模块通常用 import，并可使用 as 起别名。例 3-2 演示 NumPy 基础使用。

### 【例 3-2】初识 NumPy

输入：

[PRE13]python

import numpy as np

a = np.random.random(4)

print(type(a))

[PRE14]

输出：

[PRE15]

<class 'numpy.ndarray'>

[PRE16]

输入：

[PRE17]python

print(a.shape)

[PRE18]

输出：

[PRE19]

(4,)

[PRE20]

输入：

[PRE21]python

print(a)

[PRE22]

输出：

[PRE23]

[ 0.24123598  0.04089253  0.94548641  0.46101693]

[PRE24]

例 3-2 说明 np.random.random(4) 生成一个浮点 ndarray，类型为 numpy.ndarray，shape 为 (4,) 表示长度 4，最终数值如上。

### 3.2.2 NumPy 数组类型

科学计算中 Python 自带的整型、浮点、复数往往不够用，NumPy 提供了丰富的类型以满足不同精度需求，内存占用也随之变化。类型名多以位数结尾，见表 3-2。

① 表格参考：NumPy 用户手册。

表 3-2 NumPy 数据类型

| 类型 | 描述规则 |
|---|---|
| bool | 1 位布尔 |
| int | 平台相关精度，一般 int32 或 int64 |
| int8 | -128 至 127 |
| int16 | -32768 至 32767 |
| int32 | -2^31 至 2^31-1 |
| int64 | -2^63 至 2^63 |
| uint8 | 0 至 255 |
| uint16 | 0 至 65535 |
| uint32 | 0 至 2^32-1 |
| uint64 | 0 至 2^64-1 |
| float16 | 半精度，1 位符号 5 位指数 10 位尾数 |
| float32 | 单精度，1 位符号 8 位指数 23 位尾数 |
| float64 或 float | 双精度，1 位符号 11 位指数 52 位尾数 |
| complex64 | 双 32 位浮点构成复数 |
| complex128 或 complex | 双 64 位浮点构成复数 |

使用时可通过 dtype 指定，也可后续用 astype() 转换，每种类型都有对应构造。NumPy 的类型操作可参考例 3-3 与例 3-4。

### 【例 3-3】NumPy 数据类型操作

输入：

[PRE25]

输出：

[PRE26]

输入：

[PRE27]

输出：

[PRE28]

输入：

[PRE29]

输出：

[PRE30]

输入：

[PRE31]

输出：

[PRE32]

输入：

[PRE33]

输出：

[PRE34]

查看所有类型也可用 set(np.typeDict.values())。

### 【例 3-4】查看 NumPy 数据类型

输入：

[PRE35]

输出：

[PRE36]

### 3.2.3 NumPy 创建数组

### 1. 通过列表或元组转化

Python 内建对象常用的有 list、tuple、dict。NumPy 用 numpy.array 把列表或元组转为 ndarray。

[PRE37]

主要参数：object 为输入对象，dtype 为目标类型，未指定时自动推断；copy 是否复制，默认 True；order 内存顺序；subok 是否保留子类。

### 【例 3-5】 numpy.array 创建数组

输入：

[PRE38]

输出：

[PRE39]

### 2. arange 函数创建数组

除了 numpy.array，NumPy 还提供多种生成规律数组的工具。arange 用于在区间内生成等差数列，用法频繁，常用于迭代。与 range 类似但返回的是数组而非列表。

range 语法为 range(start, stop[, step])，按步长生成半开区间序列，返回 range 对象。start 默认为 0，stop 不包含，step 默认为 1 且不支持小数。

### 【例 3-6】 range 案例

输入：

[PRE40]

输出：

[PRE41]