一个数据副本。

为了表明视图没有维护一份自己的字典数据副本，首先将keys方法返回的视图保存到变量months_view中，然后遍历它：

```
In [4]: months_view = months.keys()

In [5]: for key in months_view:
   ...:     print(key, end=" ")
   ...:
January February March
```

接下来，在字典months中添加一个新的键-值对并显示更新后的字典：

```
In [6]: months['December'] = 12

In [7]: months
Out[7]: {'January': 1, 'February': 2, 'March': 3, 'December': 12}
```

现在，再遍历一遍months_view，可以看到上面添加的键确实显示出来了：

```
In [8]: for key in months_view:
   ...:     print(key, end=" ")
   ...:
January February March December
```

在遍历视图的时候，不要修改字典。参照Python标准库文档的4.10.1节，这可能会导致运行时错误（RuntimeError），或者可能无法处理视图中的所有值。

# 将字典的键、值和键-值对转换为列表

有时候需要列出一个字典的键、值和键-值对。要得到这样的一个列表，请将keys、values或items方法返回的视图传递给内置的list函数，修改这些列表不会造成对应字典的改变：

```
In [9]: list(months.keys())
Out[9]: ['January', 'February', 'March', 'December']

In [10]: list(months.values())
Out[10]: [1, 2, 3, 12]

In [11]: list(months.items())
Out[11]: [('January', 1), ('February', 2), ('March', 3), ('December', 12)]
```