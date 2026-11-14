# 按列索引排序

现在根据列名按升序（从左到右）排序。传递`axis=1`关键字参数表示我们希望依据列索引进行排序，而不是行索引—`axis=0`（默认值）表示按行索引排序：

```
```
In [33]: grades.sort_index(axis=1)
Out[33]:
    Bob   Eva  Katie   Sam  Wally
Test1  83  100   100   94    87
Test2  65   87    81   77    96
Test3  85   90    82   90    70
```
```

# 按列元素值排序

假定想按降序查看Test1的成绩，以便可以按分数从高到低的顺序查看学生的姓名。对此，可以按以下方式调用`sort_values`方法：

```
```
In [34]: grades.sort_values(by='Test1', axis=1, ascending=False)
Out[34]:
    Eva  Katie  Sam  Wally  Bob
Test1  100   100   94    87   83
Test2   87    81   77    96   65
Test3   90    82   90    70   85
```
```

`by`和`axis`关键字参数共同决定哪些值将被排序。在本例中，我们根据Test1的列值（`axis=1`）进行排序。

当然，如果成绩和名称位于一列中，则可能更易于阅读，因此我们可以对转置的DataFrame进行排序。在这里，我们不需要指定`axis`关键字参数，因为默认情况下`sort_values`对指定列中的数据进行排序：

```
```
In [35]: grades.T.sort_values(by='Test1', ascending=False)
Out[35]:
    Test1 Test2 Test3
Eva   100   87   90
Katie 100   81   82
Sam   94   77   90
Wally 87   96   70
Bob   83   65   85
```
```

最后，由于只需要对Test1的成绩排序，所以可能根本不想看到其他测试。为此，让我们把选择和排序结合起来：