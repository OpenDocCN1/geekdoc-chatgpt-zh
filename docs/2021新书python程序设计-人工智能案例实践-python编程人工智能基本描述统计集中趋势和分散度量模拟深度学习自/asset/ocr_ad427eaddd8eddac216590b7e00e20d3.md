```
In [1]: import pandas as pd

In [2]: grades_dict = {'Wally': [87, 96, 70], 'Eva': [100, 87, 90],
   ...:
   ...:                 'Sam': [94, 77, 90], 'Katie': [100, 81, 82],
   ...:
   ...:                 'Bob': [83, 65, 85]}

In [3]: grades = pd.DataFrame(grades_dict)

In [4]: grades
Out[4]:
     Wally  Eva  Sam  Katie  Bob
0     87  100   94    100   83
1     96   87   77     81   65
2     70   90   90     82   85
```

pandas以表格格式显示DataFrame，其中索引在索引列中左对齐，
其余列的值右对齐。字典的键变成列名，而与每个键相关联的值成为
对应列中的元素值。稍后，我们将展示如何“翻转”行和列。默认情
况下，行索引是从0开始自动生成的整数。

# 使用index属性自定义DataFrame的索引

在创建DataFrame时，可以使用index关键字参数指定自定义索
引，如下所示：

```
pd.DataFrame(grades_dict, index=['Test1', 'Test2', 'Test3'])
```

# 使用index属性将DataFrame的索引从连续整数更改为标签：

```
In [5]: grades.index = ['Test1', 'Test2', 'Test3']

In [6]: grades
Out[6]:
          Wally  Eva  Sam  Katie  Bob
Test1     87  100   94    100   83
Test2     96   87   77     81   65
Test3     70   90   90     82   85
```

在指定索引时，必须提供元素数等于DataFrame中的行数的一维合
集；否则，将引发ValueError。Series还提供了一个index属性，用于
更改现有Series的索引。

# 访问DataFrame元素的列