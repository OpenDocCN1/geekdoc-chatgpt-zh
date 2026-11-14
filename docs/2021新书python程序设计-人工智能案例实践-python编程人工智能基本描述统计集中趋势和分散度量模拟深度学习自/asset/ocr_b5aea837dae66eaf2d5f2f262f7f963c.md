一旦知道整个条件是否为False，Python就会停止计算and表达式。类似地，一旦知道整个条件是否为True，Python就会停止计算or表达式。这种机制称为短路。因此在条件

```
gender == 'Female' and age >= 65
```

中，如果gender不等于'Female'，立即停止计算，因为整个表达式的值一定为False；否则继续执行，如果age大于或等于65，整个表达式的值为True。

同样，在条件

```
semester_average >= 90 or final_exam >= 90
```

中，如果semester_average大于或等于90，立即停止计算，因为整个表达式的值一定为True；否则继续执行，因为如果final_exam大于或等于90，整个表达式的值仍然可以是True。

在使用and表示条件时，应该将其值更有可能为False的条件放在最左边。同样，在使用运算符or的表达式中，应该将其值更有可能为True的条件放在最左边。这些技巧可以减少程序的执行时间。

# 布尔运算符not

布尔运算符not表示“取反”，即True变为False，False变为True。not只有一个操作数，是一元运算符。如果原始条件（没有not运算符）为False，可以在条件之前放置not运算符来选择执行该路径，例如：

```
In [7]: grade = 87

In [8]: if not grade == -1:
     ...:     print('The next grade is', grade)
     ...
The next grade is 87
```

通常，我们会避免使用not，而是以更“自然”或方便的方式表达条件。例如，上面代码中的if语句也可以写成如下形式：