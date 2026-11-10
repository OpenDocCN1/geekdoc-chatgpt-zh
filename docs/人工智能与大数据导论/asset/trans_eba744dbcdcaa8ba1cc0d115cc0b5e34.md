

函数`person(name, age, **kw)`中，`name`和`age`为普通参数，`kw`为关键字参数。执行`person('Jack', 25)`时，函数只传入两个参数`'Jack'`和`25`，而`kw`参数因为没有接收到数据，所以其为一个空字典`{}`。执行`person('Adam', 45, gender='M', job='Engineer')`时，函数传入四个参数`'Adam'`、`45`、`gender='M'`和`job='Engineer'`，其中有两个含名称的参数`gender='M'`和`job='Engineer'`，所以`kw`以字典`{'gender': 'M', 'job': 'Engineer'}`的形式存放这两个参数。

## 11.4 函数的返回值

函数体中`return`语句的结果就是返回值。如果一个函数没有`return`语句，其实它有一个隐含的`return`语句，返回值是`None`，类型也是`'None Type'`。

### 1. 例如函数定义内有return语句

![](images/txt013_25.jpg "pagenumber_ebook=159,pagenumber_book=151")

![](images/txt013_26.jpg "pagenumber_ebook=160,pagenumber_book=152")

输出结果：

![](images/txt013_27.jpg "pagenumber_ebook=160,pagenumber_book=152")

### 2. 例如函数定义内没有return语句

![](images/txt013_28.jpg "pagenumber_ebook=160,pagenumber_book=152")

输出结果：

![](images/txt013_29.jpg "pagenumber_ebook=160,pagenumber_book=152")

函数`show_plus(x)`中没有`return`语句，则`number`变量得到的返回值为`None`。

### 3. return语句位置与多条return语句

一个函数也可以存在多条`return`语句，但只有一条可以被执行，如果没有一条`return`语句被执行，同样会隐式调用`return None`作为返回值。

函数调用中执行了`return`语句，函数就会立刻结束调用，`return`之后的其他语句都不会被执行了。

### 4. 返回值类型

无论函数定义中`return`语句的返回值是什么类型，`return`只能返回一个值，但这个值可以存在多个元素。

#### (1) return返回一个值：

![](images/txt013_30.jpg "pagenumber_ebook=160,pagenumber_book=152")

输出结果：

![](images/txt013_31.jpg "pagenumber_ebook=160,pagenumber_book=152")

#### (2) return返回多个值：

![](images/txt013_32.jpg "pagenumber_ebook=160,pagenumber_book=152")

![](images/txt013_33.jpg "pagenumber_ebook=161,pagenumber_book=153")

输出结果：

![](images/txt013_34.jpg "pagenumber_ebook=161,pagenumber_book=153")

`return a, b, c`和`return (a, b, c)`看似返回多个值，隐式地被Python封装成了一个元组返回。例如`return [1, 3, 5]`是指返回一个列表对象，`1`、`3`、`5`分别是这个列表的元素。

## 11.5 函数的嵌套调用

在一个函数中调用了另一个函数，叫做函数的嵌套调用。

【例11-3】求3个数的平均值。

![](images/txt013_35.jpg "pagenumber_ebook=161,pagenumber_book=153")

如图11-8至图11-14所示该程序运行、函数嵌套调用的过程，浅色箭头代表刚执行过的语句，深色箭头代表即将执行的语句。

(1) 程序跳过`sum_3_number`和`average_3_number`两个函数的定义部分，从程序的第12行代码开始执行。

(2) 执行第12行，执行函数`average_3_number`调用语句，转去执行第6行，将实参值`5`、`6`、`7`分别传给形参`a`、`b`、`c`。

(3) 执行第7行，执行函数`sum_3_number`调用语句，转去执行第2行，将值`5`、`6`、`7`分别传给形参`a`、`b`、`c`。

![](images/txt013_36.jpg "pagenumber_ebook=162,pagenumber_book=154")

图11-8 开始运行程序

![](images/txt013_37.jpg "pagenumber_ebook=162,pagenumber_book=154")

图11-9 调用`average_3_number`函数

![](images/txt013_38.jpg "pagenumber_ebook=162,pagenumber_book=154")

图11-10 调用`sum_3_number`函数

(4) 执行第3行，遇到`return`语句，返回`a`、`b`、`c`的和`18`。

![](images/txt013_39.jpg "pagenumber_ebook=163,pagenumber_book=155")

图11-11 结束`sum_3_number`函数调用，`return`返回值`18`

(5) 返回`average_3_number`函数，变量`sum_result`被赋值为`18`。

![](images/txt013_40.jpg "pagenumber_ebook=163,pagenumber_book=155")

图11-12 返回`average_3_number`函数

(6) 执行第8行，将表达式计算结果`6.0`赋值给变量`ave_result`，然后`return`语句，返回`ave_result`的值`6.0`。

![](images/txt013_41.jpg "pagenumber_ebook=164,pagenumber_book=156")

图11-13 结束`average_3_number`函数调用，`return`返回值`6.0`

(7) 返回到第12行，执行后续语句，直到程序结束。

![](images/txt013_42.jpg "pagenumber_ebook=164,pagenumber_book=156")

图11-14 继续执行主程序

## 11.6 函数中变量的作用域

### 11.6.1 LEGB规则

在Python程序中创建、改变、查找变量名时，都是在一个保存变量名的空间中进行，我们称之为命名空间，也被称为作用域。命名空间是对变量名的分组划分，不同作用域的相同名称的变量视为两个独立的变量，因此位于不同作用域（即命名空间）的变量名可以重复。

由于Python一切皆对象，所以在Python中变量名被视为字符串对象。

例如：

![](images/txt013_43.jpg "pagenumber_ebook=165,pagenumber_book=157")

表示建立字符串对象`a`与数值对象`10`之间的对应关系。由于这是一种映射关系，所以可以使用键-值的形式来表示，即`{name: object}`。

Python的作用域就是对多个键-值对的分组划分，即键值对的集合。因此Python的作用域就是一个字典，字典内保存了变量名称与对象之间的映射关系，因此，查找变量名就是在作用域字典中查找键-值对。

Python有多个作用域，`LEGB`就是用来规定在多个作用域中查找变量顺序的规则：

`L` - `Local(function)`：函数内部的作用域，包括局部变量和参数。

`E` - `Enclosing function locals`：外层嵌套函数的作用域，常见的是闭包函数的外层函数。

`G` - `Global(module)`：函数定义所在模块（文件）的全局作用域。

`B` - `Builtin(Python)`：Python内置模块的作用域。

`LEGB`规定了查找一个变量的顺序为：`L`→`E`→`G`→`B`，即Python检索变量的时候，会优先在函数内部的作用域中查找，如果没有找到，去外层嵌套函数的作用域中找，再找不到，就会去全局作用域中找，最后再去内置模块的作用域中找。

### 11.6.2 全局变量和局部变量

根据作用域的不同，变量分为局部变量和全局变量。在一个函数内或者语句块内定义的变量称为局部变量。局部变量的作用域仅限于定义它的函数体或语句块中，任意一个函数都不能访问其他函数中定义的局部变量。在所有函数之外定义的变量称为全局变量，它可以在多个函数中被引用。因此，在不同的函数之间可以定义同名的局部变量，虽然同名但却代表不同的变量，但不会发生命名冲突。

变量作用域测试如下：

![](images/txt013_44.jpg "pagenumber_ebook=165,pagenumber_book=157")

运行结果为：

![](images/txt013_45.jpg "pagenumber_ebook=165,pagenumber_book=157")

![](images/txt013_46.jpg "pagenumber_ebook=166,pagenumber_book=158")

![](images/txt013_47.jpg "pagenumber_ebook=166,pagenumber_book=158")

图11-15 全局变量和局部变量

如图11-15所示，全局变量`a`和变量`b`，与`fun`函数内部的局部变量`a`和`b`，在不同的作用域。程序执行到第8行，`LEGB`查到全局作用域的变量`a`和变量`b`，输出结果为：`a=10`，`b=20`。当执行`fun`函数调用语句后，根据`LEGB`原则，先查找到`fun`函数内部的作用域的变量`a`和变量`b`，输出结果为：`a=1`，`b=2`。当程序返回主程序后，`LEGB`查到全局作用域的变量`a`和变量`b`，输出结果为：`a=10`，`b=20`。

使用全局变量可以减少变量的个数，减少由于实际参数和形式参数的数据传递带来的时间消耗，但同时全局变量的值可能随时发生变化，使函数的代码可读性降低，也影响了函数的独立性。因此全局变量的使用需要慎重考虑。

### 11.6.3 global关键字和nonlocal关键字

如果需要在函数内部修改全局作用域或外层嵌套作用域的变量时，可以使用`global`和`nonlocal`关键字。

#### 1. global关键字

如果想在函数内部修改全局变量，需要在函数内部使用`global`关键字进行声明，下面我们把程序进行修改，示例代码如下：

![](images/txt013_48.jpg "pagenumber_ebook=166,pagenumber_book=158")

![](images/txt013_49.jpg "pagenumber_ebook=167,pagenumber_book=159")

运行结果为：

![](images/txt013_50.jpg "pagenumber_ebook=167,pagenumber_book=159")

![](images/txt013_51.jpg "pagenumber_ebook=167,pagenumber_book=159")

图11-16 `global`关键字

如图11-16所示，全局变量`a`和变量`b`，与`fun`函数内部的局部变量`b`，在不同的作用域。程序执行到第9行，`LEGB`查到全局作用域的变量`a`和变量`b`，输出结果为：`a=10`，`b=20`。由于在函数内部使用关键字`global`对全局变量`a`进行声明，在`fun`函数内部，根据`LEGB`原则，分别查到全局作用域的变量`a`和`fun`函数内部的作用域的变量`b`，输出结果为：`a=1`，`b=2`。当程序返回主程序后，`LEGB`查到全局作用域的变量`a`和变量`b`，输出结果为：`a=1`，`b=20`。

#### 2. nonlocal关键字

使用`nonlocal`关键字可以在函数内部的作用域中修改嵌套作用域中的变量。

![](images/txt013_52.jpg "pagenumber_ebook=167,pagenumber_book=159")

运行结果为：

![](images/txt013_53.jpg "pagenumber_ebook=167,pagenumber_book=159")

上述代码是一个函数的嵌套定义，其中`fun`为外层函数，`fun_in`为内层函数。`fun`函数中定义了一个变量`a`，在函数`fun_in`内部重新定义了变量`a`，两个变量的作用域不同，因此在`fun`函数中输出变量`a`的值为`1`。

若要修改外层函数`fun`中的变量`a`，则需要在`fun_in`函数内部使用`nonlocal`关键字对变量`a`声明，修改后的代码如下：

![](images/txt013_54.jpg "pagenumber_ebook=168,pagenumber_book=160")

运行结果为：

![](images/txt013_55.jpg "pagenumber_ebook=168,pagenumber_book=160")

由运行结果可以看到，内层函数`fun_in`中查找到的是外层函数`fun`的变量`a`，成功修改了变量`a`的值，因此在`fun`函数中输出变量`a`的值为`10`。

## 11.7 函数的递归

递归（`recursion`）是一种直接或者间接调用函数自身的算法，其本质是把问题分解成规模缩小的同类子问题，然后递归调用表示问题的解。

能够设计成递归算法的问题必须满足两个条件：能找到反复执行的过程（调用自身）和能找到跳出反复执行过程的条件（递归出口）。

【例11-4】计算`n!`的值（`n! = 1*2*3*……*n`）。

分析：`n! = (n-1)! * n`，由此可设计函数`func(n)`用递归表达：`func(n-1) * n`

代码如下：

![](images/txt013_56.jpg "pagenumber_ebook=168,pagenumber_book=160")

运行结果为：

![](images/txt013_57.jpg "pagenumber_ebook=169,pagenumber_book=161")

阶乘`5!`的算法执行原理如图11-17所示：

![](images/txt013_58.jpg "pagenumber_ebook=169,pagenumber_book=161")

图11-17 计算阶乘`5!`的执行过程

## 11.8 Lambda函数

`lambda`关键字用来在同一行内创建函数，这个函数称为匿名函数，也称`lambda`函数，就是没有实际名称的函数。`lambda`表达式主体仅是一个表达式而不需要使用代码块。

`lambda`函数适合于处理不再需要在其他位置复用代码的函数逻辑，可以省去函数的定义过程和考虑函数的命名，让代码更加简洁，可读性更好。其通式为：

![](images/txt013_59.jpg "pagenumber_ebook=169,pagenumber_book=161")

例如：输入两个数，输出它们的积`x*y`，自定义函数如下：

![](images/txt013_60.jpg "pagenumber_ebook=169,pagenumber_book=161")

用`lambda`来实现：

![](images/txt013_61.jpg "pagenumber_ebook=169,pagenumber_book=161")

函数对象名可以作为函数直接调用，如：

![](images/txt013_62.jpg "pagenumber_ebook=169,pagenumber_book=161")

对一个整数列表，按照元素的绝对值大小升序排列。用`lambda`函数实现代码如下：

![](images/txt013_63.jpg "pagenumber_ebook=169,pagenumber_book=161")

字典是无序的，不能直接对字典排序，如果需要对字典按照键降序排序，可以采取间接的办法，先把字典的键-值对转换为列表，即`d.items()`，再对列表嵌套的元组按第一项（键）排序，用`lambda`函数可以方便地获取元组的第一项。代码如下：

![](images/txt013_64.jpg "pagenumber_ebook=170,pagenumber_book=162")

程序运行结果：

![](images/txt013_65.jpg "pagenumber_ebook=170,pagenumber_book=162")

如果需要对字典按照值升序排序，则修改`lambda`函数即可：

![](images/txt013_66.jpg "pagenumber_ebook=170,pagenumber_book=162")

运行结果：

![](images/txt013_67.jpg "pagenumber_ebook=170,pagenumber_book=162")

## 11.9 程序实例

【例11-5】编写一个函数，功能是计算传入列表的最大值、最小值和平均值，然后调用该函数。

程序如下：

![](images/txt013_68.jpg "pagenumber_ebook=170,pagenumber_book=162")

程序运行结果：

![](images/txt013_69.jpg "pagenumber_ebook=170,pagenumber_book=162")

最大值是`96`，最小值是`12`，平均值是`44.9`。

![](images/txt013_70.jpg "pagenumber_ebook=170,pagenumber_book=162")

图11-18 程序执行过程

分析：

(1) 程序中引用了`random`模块。Python中的`random`模块用于生成随机数，它提供了很多函数，其中`random.randint(a, b)`返回一个随机整数`N`，`N`的取值`[a, b]`。需要注意的是`a`和`b`的取值必须为整数，并且`a`的值一定要小于`b`的值。

(2) `fun`函数里的`return`返回一个元组类型来间接达到返回多个值。返回的元组可以省略括号，而多个变量可以同时接收一个元组，按位置赋给对应的值，所以，函数返回多值其实就是返回一个元组，这样写起来更方便。

【例11-6】求`1! + 2! + 3! + …… + 10!`的值。程序如下：

![](images/txt013_71.jpg "pagenumber_ebook=171,pagenumber_book=163")

运行结果：

![](images/txt013_72.jpg "pagenumber_ebook=171,pagenumber_book=163")

