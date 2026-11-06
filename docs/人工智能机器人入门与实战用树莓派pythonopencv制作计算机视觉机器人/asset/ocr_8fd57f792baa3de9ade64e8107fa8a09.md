在设定返回值的情况下，调用函数将直接得到其返回值。例如使用 factorial(5) 将直接获得 5 的阶乘值，我们可以利用它对变量进行赋值，例如：

```
result = factorial(5)
```

这一语句可以将 5 的阶乘赋值给变量 result。

试一试
将斐波那契数列的运算封装为一个函数，通过调用这个函数获取数列中指定位置的值。

# 3.7.2 给 Python 程序加上注释

为了使 Python 程序变得更加易读，我们可以为程序中的语句、定义的函数、变量等进行注释来帮助阅读者理解程序的作用。在 Python 程序中，以 #（井号）开头的语句将被 Python 认为注释，这些内容将不会被执行。例如：

```
# calculate the factorial of a positive integer
def factorial(n):
    a, f = 1, 1
    while a <= n:
        f = f * a
        a = a + 1
    return f
# get the factorial of 5
result = factorial(5)
```

如果需要进行连续多行的注释，可以在需要注释的语句前后各使用 3 个单引号或 3 个双引号（前后必须一致）。例如：

```
'''
this is a program to calculate the factorial of a positive integer
we get factorial of 5
'''
def factorial(n):
    a , f = 1, 1
    while a <= n:
        f = f * a
        a = a + 1
    return f
result = factorial(5)
```

给程序加上清晰的注释是一个良好的编写习惯，使我们在程序内容复杂时只需阅读注释就可以大致掌握程序的结构和功能。

除了说明程序功能外，注释也可以用来跳过一些语句的执行，这是调试程序的一种常用技巧。