+   - 首先，假设`value1`包含最大值，因此将其赋值给局部变量
+     `max_value`。当然，`value2`或`value3`可能包含实际的最大值，因此
+     必须将它们与`max_value`进行比较。
+   - 然后，第一个`if`语句测试`value2>max_value`，如果此条件为
+     `True`，则将`value2`赋值给`max_value`。
+   - 最后，第二个`if`语句测试`value3>max_value`，如果此条件为
+     `True`，则将`value3`赋值给`max_value`。

现在，`max_value`中包含三个值中的最大值，因此将它返回。当控
制权返回给调用者时，参数`value1`、`value2`和`value3`以及函数语句块
中的变量`max_value`（都是局部变量）都将被回收。

# Python内置函数max和min

Python中包含许多用于完成常见任务的函数。例如，内置的`max`和
`min`函数分别可用于求出各自参数中的最大和最小值，它们的参数可以
是两个，也可以是多个：

![](bbox=[0.205, 0.496, 0.798, 0.554])

以上两个函数都可以接收可迭代的参数，例如列表或字符串。使
用Python内置函数或标准库模块中的函数而不是编写自定义函数可以
缩短开发时间并提高程序的可靠性、可移植性和性能。有关Python内
置函数和模块的列表，可参阅
https://docs.python.org/3/library/index.html。