# 3.4 if...else和if...elif...else语句

if...else语句根据条件是True还是False执行不同的套件：

```
In [1]: grade = 85

In [2]: if grade >= 60:
...: print('Passed')
... else:
...: print('Failed')
...
Passed
```

上面的代码段中的条件为True，因此if套件显示'Passed'。需要注意的是，当键入print ('Passed') 后按Enter键时， IPython会将下一行缩进四个空格。必须删除这四个空格，以便“else:”套件可以在if的字母i下正确对齐。

下面的代码将57赋值给变量grade，然后再次执行if...else语句，执行结果说明，条件为False时只执行else的套件：

```
In [3]: grade = 57

In [4]: if grade >= 60:
...: print('Passed')
... else:
...: print('Failed')
...
Failed
```

使用向上和向下箭头键可以在交互式会话的当前代码段中向后或向前导航，按Enter键可以重新执行选中的代码段。现在，我们将grade赋值为99，按两次向上箭头键导航到代码段[4]，然后按Enter键重新执行该代码段。因为每个被执行的代码段都会获得一个新的ID，因此，该代码段将显示为[6]，如下所示：

```
In [5]: grade = 99

In [6]: if grade >= 60:
...: print('Passed')
... else:
...: print('Failed')
...
Passed
```

# 条件表达式