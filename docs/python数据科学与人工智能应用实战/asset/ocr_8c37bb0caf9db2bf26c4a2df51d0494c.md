# 9.1.2 Series 对象

因为 Pandas 包中数据处理和分析的重点是 DataFrame 对象，所以笔者只简单说明 Series 对象的使用。在 Python 程序首先需要导入 Pandas 包，如下所示：

```
import pandas as pd
```

# 动手学——创建 Series 对象：Ch9_1_2.py

使用 Python 列表来创建 Series 对象，如下所示：

```
import pandas as pd
```

```
s = pd.Series([12, 29, 72, 4, 8, 10])
```

```
print(s)
```

上述程序代码导入 Pandas 包 pd 后，调用 Series() 函数创建 Series 对象，然后输出 Series 对象。其运行结果如下所示：

```
0    12
1    29
2    72
3     4
4     8
5    10
dtype: int64
```

上述运行结果的第 1 列是默认新增的索引（从 0 开始），如果在创建时没有指定索引，Pandas 会自行创建；最后一行是元素的数据类型。

# 动手学——创建自定义索引的 Series 对象：Ch9_1_2a.py

在第 9.1.1 节曾经说过过，Series 对象如同 2 个数组，一是索引的标签，一是数据。因此，可以使用 2 个 Python 列表来创建 Series 对象，如下所示：

```
fruits = ["苹果", "橘子", "梨子", "樱桃"]
quantities = [15, 33, 45, 55]
s = pd.Series(quantities, index=fruits)
print(s)
print(s.index)
print(s.values)
```

上述程序代码首先创建 2 个列表；然后创建 Series 对象，其中第 1 个参数是数据列表，第 2 个参数是使用 index 参数指定的索引列表；随后输出 Series 对象，使用 index 属性输出索引，使用 values 属性输出数据。其运行结果如下所示：