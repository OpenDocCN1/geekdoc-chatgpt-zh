```
In [4]: country_codes.update(Australia='ar')
In [5]: country_codes
Out[5]: {'South Africa': 'za', 'Australia': 'ar'}
```

代码片段 [4] 提供了一个错误的澳大利亚国家代码。让我们使用另一个关键字参数来更新与 'Australia' 相关的值以纠正这个错误：

```
In [6]: country_codes.update(Australia='au')
In [7]: country_codes
Out[7]: {'South Africa': 'za', 'Australia': 'au'}
```

update方法还可以接收包含键-值对的迭代对象，例如二元元组列表。

# 6.2.9 字典推导式

字典推导式为快速生成字典提供了一种方便的表示方法，通常是将一个字典映射到另一个字典。例如，在具有不重复值的字典中，可以对键-值对进行反向操作：

```
In [1]: months = {'January': 1, 'February': 2, 'March': 3}
In [2]: months2 = {number: name for name, number in months.items()}
In [3]: months2
Out[3]: {1: 'January', 2: 'February', 3: 'March'}
```

字典推导式用花括号表示，for子句左边的表达式指定了key:value形式的键-值对。推导式遍历months.items()，将每个键-值对元组解包到变量name和number中。表达式number:name反转键和值，得到一个从月份数映射到月份名的新字典。

如果月份包含重复的值怎么办？当值成为months2中的键时，试图插入一个重复的键只会更新现有键的值。因此，如果'February'和'March'最初都映射到2，则前面的代码将生成：

```
{1: 'January', 2: 'March'}
```

字典推导式还可以将字典的值映射到新值。下面的推导式将一个关于名字和成绩列表的字典转换成一个关于名字和平均成绩的字典。通常用变量k和v表示键和值：