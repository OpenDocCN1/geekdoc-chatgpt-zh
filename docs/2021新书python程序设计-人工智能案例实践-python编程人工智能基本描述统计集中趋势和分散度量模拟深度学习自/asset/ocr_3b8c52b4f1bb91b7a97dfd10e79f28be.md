```
In [9]: contacts = [['Mike Green', 'demo1@delitel.com', '5555555555'].
....:       ['Sue Brown', 'demo2@delitel.com', '5555551234']]
In [10]: contactsdf = pd.DataFrame(contacts,
....:                               columns=['Name', 'Email', 'Phone'])
In [11]: contactsdf
Out[11]:
Name                   Email           Phone
0  Mike Green  demo1@delitel.com  5555555555
1   Sue Brown  demo2@delitel.com  5555551234
```

在这个DataFrame中，通过columns关键字参数指定了列索引，但是没有指定行索引，因此从0开始对行进行索引。此外，在默认情况下各列值右对齐输出。这与Python格式不同，在Python格式中，字段中的数字默认右对齐，而非数字值默认左对齐。

现在，让我们用稍微更函数式的编程来处理这些数据。通过调用在DataFrame对象的'phone'列上的Series方法map，可以将电话号码映射为适当的格式。map方法的参数是一个接收原始值并返回映射值的函数。函数get_formatted_phone将10个连续的数字映射为###-###-####格式：

```
In [12]: import re
In [13]: def get_formatted_phone(value):
    ....:     result = re.fullmatch(r'(\d{3})(\d{3})(\d{4})', value)
    ....:     return '-'.join(result.groups()) if result else value
    ....:
```

函数get_formatted_phone的第一条语句中的正则表达式只能与10个连续数字相匹配。它抓取包含前面三位、中间三位和最后四位为数的子字符串。return语句的操作如下：

+   - 如果结果为None，则返回未修改的值。
+   - 否则，调用result.groups()来获得一个包含所抓取到的子字符串的元组，并将该元组传递给字符串方法join来拼接元素，用'-'将每个元素与下一个元素分开，从而形成映射得到的电话号码。

Series方法map返回一个新的Series对象，其中包含对列中的每个值调用其函数参数的结果。代码段[15]展示了经map方法处理后的运行结果，包括列的名称和类型：