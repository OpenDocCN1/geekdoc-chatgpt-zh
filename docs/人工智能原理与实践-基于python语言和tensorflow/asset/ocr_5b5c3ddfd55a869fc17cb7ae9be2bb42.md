二元 ufunc 代码如下所示。

```
Print ('二元 ufunc 示例')
x = numpy.array([[1,4],[6,7]])
y = numpy.array([[2,3],[5,8]])
print (numpy.maximum(x,y)) #[[2,4],[6,8]]
print (numpy.minimum(x,y)) #[[1,3],[5,7]]
```

# 7. Ndarray 常用的统计方法

NumPy 库中还包括一些常用的统计方法，可以通过这些基本统计方法对整个数组/某个轴的数据进行统计计算。表 2-13 对这些统计方法进行了说明。

# 表 2-13

| 方法 | 说明 |
|---|---|
| sum | 对数组中全部或某轴向的元素求和。零长度数组的 sum 为 0 |
| mean | 算术平均数。零长度数组的 mean 为 NaN |
| std、var | 分别为标准差和方差，自由度可调（默认为 n ） |
| min、max | 最大值和最小值 |
| argmin、argmax | 分别为最大元素和最小元素的索引 |
| cumsum | 所有元素的累计和 |
| cunprod | 所有元素的累计积 |

代码如下所示。

```
Print ('numpy 的基本统计方法')
x = numpy.array([[1,2],[3,3],[1,2]]) #同 1 维度上的数组长度须一致
print (x.mean()) #2
print (x.mean(axis=1)) #对每一行的元素求平均
print (x.mean(axis=0)) #对每一列的元素求平均
print (x.sum()) #同理 12
print (x.sum(axis=1)) #[3 6 3]
print (x.max()) #3
print (x.max(axis=1)) #[2 3 2]
print (x.cumsum()) #[ 1 3 6 9 10 12]
print (x.cumprod()) #[ 1 2 6 18 18 36]
```

# 8. NumPy 中的线性代数

线性代数（如矩阵乘法、行列式等）是数学运算中的一个重要工具，在图像信号处理、音频信号处理中起到非常重要的作用。NumPy 与 Matlab 不同的是，通过 “**” 得到的是 2 维数组的元素级的积，而不是一个矩阵点积。

Numpy.linalg 中有一组标准的矩阵分解运算以及如求逆和行列式的操作。表 2-14 列出了 numpy.linalg 中的常用函数。

# 表 2-14

| 函数 | 说明 |
|---|---|
| diag | 以 1 维数组的形式返回方阵的对角线元素 |
| dot | 矩阵乘法 |
| trace | 计算对角线元素的和 |