格式字符串可以通过format方法的参数列表中的索引位置引用特定的参数，索引从位置0开始：

```
In [3]: '{0} {0} {1}'.format('Happy', 'Birthday')
Out[3]: 'Happy Happy Birthday'
```

注意到我们使用了两次索引位置0的参数'Happy'一可以随意引用任意顺序的任意参数。

# 引用关键字参数

可以通过占位符中的关键字来引用关键字参数：

```
In [4]: '{first} {last}'.format(first='Amanda', last='Gray')
Out[4]: 'Amanda Gray'
In [5]: '{last} {first}'.format(first='Amanda', last='Gray')
Out[5]: 'Gray Amanda'
```