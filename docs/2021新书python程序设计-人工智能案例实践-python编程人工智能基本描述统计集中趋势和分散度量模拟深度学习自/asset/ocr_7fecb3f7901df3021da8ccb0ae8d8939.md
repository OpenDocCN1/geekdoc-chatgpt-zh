# 3.5 while语句

while语句在循环测试条件保持为True时重复一个或多个操作。下面的代码使用while语句来查找第一个大于50的3的幂：

```
In [1]: product = 3

In [2]: while product <= 50:
    ...: product = product * 3
    ...:

In [3]: product
Out[3]: 81
```

代码段[3]评估result的值为81，这是第一个大于50的3的幂。

while语句套件中必须有某些语句可以更改result的值，使得循环测试条件最终变为False，否则，会进入无限循环。在Terminal、Anaconda命令提示符或shell中执行应用程序时，可以按组合键Ctrl + C或Control + C来终止无限循环。如果使用IDE编写和执行代码，则通常会有用于终止程序执行的工具栏按钮或菜单选项。