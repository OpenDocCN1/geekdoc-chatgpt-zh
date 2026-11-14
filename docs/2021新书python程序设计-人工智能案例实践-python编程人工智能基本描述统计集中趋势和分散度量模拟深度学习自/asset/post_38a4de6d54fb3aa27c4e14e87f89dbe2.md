
```
In [6]: numbers = [10, 3, 7, 1, 9, 4, 2, 8, 5, 6]

In [7]: ascending_numbers = sorted(numbers)

In [8]: ascending_numbers
Out[8]: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

In [9]: numbers
Out[9]: [10, 3, 7, 1, 9, 4, 2, 8, 5, 6]

In [10]: letters = 'fadgchjebi'

In [11]: ascending_letters = sorted(letters)

In [12]: ascending_letters
Out[12]: ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j']

In [13]: letters
Out[13]: 'fadgchjebi'

In [14]: colors = ('red', 'orange', 'yellow', 'green', 'blue')

In [15]: ascending_colors = sorted(colors)

In [16]: ascending_colors
Out[16]: ['blue', 'green', 'orange', 'red', 'yellow']

In [17]: colors
Out[17]: ('red', 'orange', 'yellow', 'green', 'blue')
```

将可选关键词参数中的reverse设置为True可按降序对元素进行排序。

## 5.9 序列搜索

经常需要确定序列（例如列表、元组或字符串）是否包含匹配于特定关键词值的元素，搜索就是查找关键词的过程。

### 列表的index方法

列表的index方法使用关键词（要搜索的值）作为参数，然后从索引0开始搜索列表，并返回与关键词匹配的第一个元素的索引：

```
In [1]: numbers = [3, 7, 1, 4, 2, 8, 5, 6]
In [2]: numbers.index(5)
Out[2]: 6
```

如果列表中不包含所搜索的值，则会出现值错误（ValueError）。

### 指定搜索的起始索引

使用index方法的可选参数，可以搜索列表元素的子集。使用*=，可以成倍地扩充序列，也就是说，一个序列追加到自身多次。执行下面的代码段，numbers变为包含原始列表内容的两份副本：

```
In [3]: numbers *= 2
In [4]: numbers
Out[4]: [3, 7, 1, 4, 2, 8, 5, 6, 3, 7, 1, 4, 2, 8, 5, 6]
```

以下代码在新列表numbers中，从索引7到列表末尾的所有元素中搜索5：

```
In [5]: numbers.index(5, 7)
Out[5]: 14
```

### 指定搜索的起始索引和结束索引

在index方法中指定起始索引和结束索引，会搜索从起始索引到结束索引（不包括结束索引）的匹配元素位置。在代码段[5]中调用 index方法：

```
numbers.index(5, 7)
```

可选的第三个参数的默认值为numbers的长度，相当于：

```
numbers.index(5, 7, len(numbers))
```

下面将在索引0到3的范围内查找值等于7的元素：

```
In [6]: numbers.index(7, 0, 4)
Out[6]: 1
```

### 成员运算符in和not in

运算符in的右边是可迭代的序列，左边是要查找的值。如果在指定的序列中找到值，则返回True，否则，返回False：

```
In [7]: 1000 in numbers
Out[7]: False
In [8]: 5 in numbers
Out[8]: True
```

类似地，运算符not in如果在指定的序列中没有找到值，则返回True，否则，返回False：

```
In [9]: 1000 not in numbers
Out[9]: True
In [10]: 5 not in numbers
Out[10]: False
```

### 使用运算符in来防止ValueError

搜索序列中不存在的关键词时，使用in运算符不像使用index方法，不会导致ValueError：

```
In [11]: key = 1000

In [12]: if key in numbers:
    ...:     print(f'found {key} at index {numbers.index(search_key)}!')
    ...: else:
    ...:     print(f'{key} not found')
    ...:
1000 not found
```

### 内置函数any和all

有时只需要知道可迭代对象中的任意项是True还是所有项都是True。如果可迭代对象中的任何项为True，则内置函数any返回True。如果可迭代对象中的所有项都为True，则内置函数all返回True。回想一下，非零值为True，而0为False。非空的可迭代对象的值为True，而空的可迭代对象的值都为False。函数any和all是函数式编程中内部迭代的附加示例。

## 5.10 列表的其他方法

列表还有添加和删除元素的方法。考虑列表`color_names`:

```
In [1]: color_names = ['orange', 'yellow', 'green']
```

### 在列表特定索引处插入元素

用`insert`方法在指定的索引处添加新项。下面的代码在索引0处插入`'red'`:

```
In [2]: color_names.insert(0, 'red')
In [3]: color_names
Out[3]: ['red', 'orange', 'yellow', 'green']
```

### 将元素添加到列表的末尾

使用`append`方法可以将新元素添加到列表的末尾:

```
In [4]: color_names.append('blue')
In [5]: color_names
Out[5]: ['red', 'orange', 'yellow', 'green', 'blue']
```

### 将序列的所有元素添加到列表的末尾

使用列表的`extend`方法将另一个序列的所有元素添加到列表的末尾:

```
In [6]: color_names.extend(['indigo', 'violet'])
In [7]: color_names
Out[7]: ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet']
```

列表的`extend`方法等效于`+=`。下面的代码先将字符串的所有字符附加到列表中，然后将元组的所有元素附加到列表中:

```
In [8]: sample_list = []
In [9]: s = 'abc'
In [10]: sample_list.extend(s)
In [11]: sample_list
Out[11]: ['a', 'b', 'c']
In [12]: t = (1, 2, 3)
In [13]: sample_list.extend(t)
In [14]: sample_list
Out[14]: ['a', 'b', 'c', 1, 2, 3]
```

可能希望将元组直接作为参数传递给列表的extend方法，而不是先创建一个临时变量t来存储元组。因为extend方法的参数必须是可迭代的对象，这个时候可以直接用在参数部分显示的括号构造一个元组（元组的括号不可以省略）：

```
In [15]: sample_list.extend((4, 5, 6)) # note the extra parentheses
In [16]: sample_list
Out[16]: ['a', 'b', 'c', 1, 2, 3, 4, 5, 6]
```

如果省略元组的括号，则会引发TypeError。

### 删除列表中某个元素的第一个匹配项

remove方法用于移除列表中某个值的第一个匹配项—如果remove的参数不在列表中，则会引发ValueError：

```
In [17]: color_names.remove('green')
In [18]: color_names
Out[18]: ['red', 'orange', 'yellow', 'blue', 'indigo', 'violet']
```

### 清空列表

要删除列表中的所有元素，请使用方法clear：

```
In [19]: color_names.clear()
In [20]: color_names
Out[20]: []
```

这相当于前面出现的给切片分配空数组：

```
color_names[:] = []
```

### 计算元素出现的次数

列表的count方法用于统计某个元素在列表中出现的次数：

```
In [21]: responses = [1, 2, 5, 4, 3, 5, 2, 1, 3,  
...:     1, 4, 3, 3, 3, 2, 3, 3, 2, 2]

In [22]: for i in range(1, 6):
...:     print(f'{i} appears {responses.count(i)} times in responses')
1 appears 3 times in responses
2 appears 5 times in responses
3 appears 8 times in responses
4 appears 2 times in responses
5 appears 2 times in responses
```

### 反转列表的元素

列表的reverse方法反转列表中的元素，注意区别于前面使用切片创建列表的反向副本：

```
In [23]: color_names = ['red', 'orange', 'yellow', 'green', 'blue']

In [24]: color_names.reverse()

In [25]: color_names
Out[25]: ['blue', 'green', 'yellow', 'orange', 'red']
```

### 复制列表

列表的copy方法返回一个包含原始对象浅拷贝的新列表：

```
In [26]: copied_list = color_names.copy()

In [27]: copied_list
Out[27]: ['blue', 'green', 'yellow', 'orange', 'red']
```

这相当于前面使用切片创建副本的操作：

```
copied_list = color_names[:]
```

## 5.11 使用列表模拟堆栈

上一章介绍了函数调用堆栈。Python没有内置堆栈类型，但可以将堆栈视为约束列表。使用列表的append方法来实现入栈，该方法将新元素添加到列表的末尾。使用不带参数的pop方法来实现出栈，该方法移除列表中的最后一个元素，并且返回该元素的值。

让我们创建一个名为stack的空列表，用append方法令两个字符串入栈，然后用pop方法使其出栈，来确认一下它们是否保持后进先出（Last-In, First-Out, LIFO）的顺序：

![](img/bf43b6caad271ba92b343a48c79c2155_224_0.png)

对于每个出栈的代码段，pop方法移除并且返回该元素的值。对空堆栈做出栈操作会引发IndexError，就像用[]访问列表中某个不存在的元素一样。要避免出现IndexError，就需要在每次做出栈之前，确保堆栈的项目数大于0。如果入栈的速度大于出栈的速度，可能会耗尽内存。

还可以使用列表来模拟另一个常用的合集—队列，队列好比排队，依次鱼贯而行，以先进先出（First-In, First-Out, FIFO）顺序来处理项目。

## 5.12 列表推导式

列表推导式（List Comprehension）是将一个列表转换成另一个列表的工具。在转换过程中，可以指定元素必须符合一定的条件，才能添加至新的列表中，这样每个元素都可以按需要进行转换。对于熟悉函数式编程（functional programming）的读者，可以把列表推导式看作结合了filter函数与map函数功能的语法糖。如果不熟悉函数式编程，也不用担心。这里先给出一个for循环的示例：

```
In [1]: list1 = []

In [2]: for item in range(1, 6):
    ...:     list1.append(item)
    ...: 

In [3]: list1
Out[3]: [1, 2, 3, 4, 5]
```

### 使用列表推导式来创建整数列表

可以使用列表推导式在一行代码中完成相同的任务：

```
In [4]: list2 = [item for item in range(1, 6)]

In [5]: list2
Out[5]: [1, 2, 3, 4, 5]
```

列表推导式的for子句与代码段[2]的for循环语句一样：

```
for item in range(1, 6)
```

对于由range(1, 6)迭代生成的序列中的每个元素，列表推导式计算for子句左侧的表达式，并将表达式的值放在新列表（在本例中为item自身）中。使用下面的list函数可以更简洁地表达代码段[4]的特殊推导式：

```
list2 = list(range(1, 6))
```

### 映射：在列表推导式的表达式中执行操作

列表推导式的表达式可以执行将元素映射到新值（可能是不同类型）的任务，例如某种运算。映射是一种常见的函数式编程操作，它生成与原始数据元素数量相等的映射结果。下面的推导式使用表达式 `item*3` 将每个值的立方映射到新列表：

```
In [6]: list3 = [item ** 3 for item in range(1, 6)]

In [7]: list3
Out[7]: [1, 8, 27, 64, 125]
```

### 过滤：在列表推导式中使用if子句

另一种常见的函数式编程操作是过滤，只选择满足条件的元素，满足条件的元素的数量通常比原始元素的数量要少，我们使用if子句在列表推导式中执行此操作。下面的代码让列表`list4`中仅包括由`for`子句产生的偶数值：

```
In [8]: list4 = [item for item in range(1, 11) if item % 2 == 0]

In [9]: list4
Out[9]: [2, 4, 6, 8, 10]
```

### 用列表推导式处理另一个列表的元素

`for`子句可以处理任何可迭代的序列。让我们创建一个小写字符串的列表，然后用列表推导式来创建一个包含其大写版本的新列表：

```
In [10]: colors = ['red', 'orange', 'yellow', 'green', 'blue']

In [11]: colors2 = [item.upper() for item in colors]

In [12]: colors2
Out[12]: ['RED', 'ORANGE', 'YELLOW', 'GREEN', 'BLUE']

In [13]: colors
Out[13]: ['red', 'orange', 'yellow', 'green', 'blue']
```

## 5.13 生成器表达式

生成器表达式类似于列表推导式。当序列过长，而每次只需要获取一个元素时，应当考虑使用生成器表达式，这称为惰性计算（lazy evaluation）。列表推导式使用贪婪计算（greedy evaluation）——执行时一次性创建包含了所有值的列表。对于大序列，创建列表需要大量的内存和时间。如果不需要整个列表，生成器表达式可以大大减少程序消耗的内存数量，并提高性能。

生成器表达式的语法和列表解析一样，只不过生成器表达式是被（）括起来的，而不是[]。代码段[2]中的生成器表达式仅返回列表numbers中的奇数的平方值：

```
In [1]: numbers = [10, 3, 7, 1, 9, 4, 2, 8, 5, 6]
In [2]: for value in (x ** 2 for x in numbers if x % 2 != 0):
    ...:     print(value, end=' ')
    ...:
9 49 1 81 25
```

为了证明生成器表达式不创建列表，让我们将上面代码段的生成器表达式赋给变量并查看该变量：

```
In [3]: squares_of_odds = (x ** 2 for x in numbers if x % 2 != 0)
In [3]: squares_of_odds
Out[3]: <generator object <genexpr> at 0x1085e84c0>
```

"generator object <genexpr>"说明square_of_odds是一个从生成器表达式（genexpr）创建的生成器对象。

## 5.14 过滤、映射和归约

这一节介绍几个函数式功能：过滤、映射和归约。这里先分别演示内置函数filter和map的过滤和映射的功能。然后继续讨论归约，其将元素合集处理为单个值，例如计数、求和、乘积、平均、最小值或最大值。

### 使用内置函数filter过滤序列的值

使用内置函数filter来得到列表numbers中的奇数元素：

```
In [1]: numbers = [10, 3, 7, 1, 9, 4, 2, 8, 5, 6]
In [2]: def is_odd(x):
    ....:     """Returns True only if x is odd."""
    ....:     return x % 2 != 0
    ....:
In [3]: list(filter(is_odd, numbers))
Out[3]: [3, 7, 1, 9, 5]
```

与数据一样，Python函数可以当作对象赋值给变量，传递给其他函数以及从函数返回对象。能接收其他函数作为参数的函数是一种编程范式，称为高阶函数。上面的例子中， filter函数的第一个参数is_odd必须是一个函数，该函数接收一个参数，如果该参数是奇数，则返回True。 filter函数对第二个参数的可迭代对象（numbers）中的每个值调用is_odd一次。高阶函数也可能返回一个函数。

filter函数返回的是迭代器，因此filter在迭代它们之前不会产生结果，这是另一个惰性计算的例子。在代码段[3]中，list函数创建包含遍历结果的列表。通过使用带有if子句的列表推导式，可以获得与上面相同的结果：

```
In [4]: [item for item in numbers if is_odd(item)]
Out[4]: [3, 7, 1, 9, 5]
```

### 使用lambda表达式代替函数
