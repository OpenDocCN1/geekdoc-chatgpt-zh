![](bbox=[0.125, 0.085, 0.475, 0.655])

# 图6-4 手写数字数据集的例子

为了使用前面两个例子的分类器，我们必须将图像格式化处理为一个向量。我们首先编写一段函数img2vector()，将图像转换为向量。该函数创建1×1024的NumPy数组，然后打开给定的文件，循环读出文件的前32行，并将每行的前32个字符值存储在NumPy数组中，最后返回数组，如清单6-9所示。

# 清单6-9 将图像转换为向量

```
1. def img2vector(filename):
2.     returnVect = zeros((1,1024))
3.     fr = open(filename)
4.     for i in range(32):
5.         lineStr = fr.readline()
6.         for j in range(32):
```