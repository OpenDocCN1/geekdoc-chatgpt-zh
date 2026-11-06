# 附录 B

# 本书用到的 Python 包简介

# 1. numpy

numpy 的主要对象是同类型元素的多维数组，这是一个所有的元素都是一种类型、通过一个正整数元组索引的元素表格（通常是元素是数字）。在 numpy 中维度（dimensions）叫作轴（axes），轴的个数叫作秩（rank）。

如下示例中，我们用 arr 表示 numpy 数组对象，同时需要做如下引入。

```
import numpy as np
```

+   - 1) 数据的输入和输出
+   - np.loadtxt('file.txt')：从文件“file.txt”中导入数据并返回 ndarray 对象
+   - np.genfromtxt('file.csv', delimiter=',')：从文件“file.csv”中导入数据并返回 ndarray 对象
+   - np.savetxt('file.txt', arr, delimiter=' ')：将数组保存为“file.txt”文件
+   - np.savetxt('file.csv', arr, delimiter=',')：将数组保存为“file.csv”文件

+   - 2) 创建数组
+   - np.array([1,2,3,4,5,6])：创建一维数组并返回 ndarray 对象
+   - np.array([(1.2,3),(4.5,6)])：创建二维数组并返回 ndarray 对象
+   - np.zeros(3)：创建长度为 3 的一维数组且数组元素均为 0,返回 ndarray 对象
+   - np.ones((3,4))：创建 3×4 的二维数组且数组元素均为 1,返回 ndarray 对象
+   - np.eye(5)：创建一个 5×5 的二维数组且对角线元素均为 1,其他元素均为 0,返回 ndarray 对象
+   - np.linspace(0,100,6)：创建一维数组且其元素为 0 到 100 之间的 6 等分数字,返回 ndarray 对象
+   - np.arange(0,10,3)：创建一维数组且其元素为以 0 为起点,步长为 3,直到小于 10 的所有数值,返回 ndarray 对象
+   - np.full((2,3),8)：创建 2×3 二维数组且其所有元素均为 8,返回 ndarray 对象