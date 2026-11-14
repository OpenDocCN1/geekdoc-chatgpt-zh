有时， if...else语句中的套件可以根据条件为变量赋不同的值，如下所示：

```
In [7]: grade = 87

In [8]: if grade >= 60:
    ...:     result = 'Passed'
    ...: else:
    ...:     result = 'Failed'
    ...:
```

然后我们可以评估这个变量或者用print输出这个变量，如下所示：

```
In [9]: result
Out[9]: 'Passed'
```

代码段[8]的语句可以改写为简洁的条件表达式，如下所示：

```
In [10]: result = ('Passed' if grade >= 60 else 'Failed')

In [11]: result
Out[11]: 'Passed'
```

上面的代码段中的括号不是必需的，但它们能清晰地表明该语句将条件表达式的值赋给了result。首先，Python评估条件grade >= 60：

+   - 如果条件为True，则代码段[10]将if左侧表达式的值，即'Passed'，赋值给result。else部分不执行。
+   - 如果条件为False，则代码段[10]将else右侧表达式的值，即'Failed'，赋值给result。

在交互模式下，还可以直接评估条件表达式，如下所示：

```
In [12]: 'Passed' if grade >= 60 else 'Failed'
Out[12]: 'Passed'
```

# 套件中的多条语句

下面的代码中， if...else语句的else套件包含两条语句：