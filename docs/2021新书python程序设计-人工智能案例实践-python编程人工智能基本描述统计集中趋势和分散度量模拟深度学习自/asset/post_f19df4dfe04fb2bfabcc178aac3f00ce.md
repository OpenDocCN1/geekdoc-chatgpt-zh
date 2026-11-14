
```python
In [1]: min(36, 27, 12)
Out[1]: 12

In [2]: max(36, 27, 12)
Out[2]: 36
```

函数min和max可以接收任意数量的参数。

## 确定合集中值的范围

值的range指的是从最小值到最大值。在上面的例子中，range是从12到36。许多数据科学致力于了解数据的性质，描述性统计是其中的关键部分，因此，我们需要知道这些统计数据的含义。例如，如果有100个数字，范围为12到36，那么这些数字可以均匀地分布在这个范围内。在极端情况下，这100个数字也可能会包含99个12和1个36，或1个12和99个36。

## 函数式编程：约简

本书将介绍多种函数式编程的技巧，这些技巧可以使我们能够编写出更简洁、更清晰、更易于调试（即查找和纠正错误）的代码。min和max函数是称为约简的函数式编程概念的示例，min和max会将一个合集的值约简为一个值。书中还会用到许多其他的约简，例如合集中的值的总和、平均值、方差和标准偏差等，并且还会介绍如何自定义约简。

## 数据科学部分简介

在接下来的两章中，我们将继续讨论采用集中趋势度量的基础描述性统计，包括均值、中值和众数，以及离中趋势度量，包括方差和标准偏差等。

## 2.10 小结

本章继续讨论了算术运算以及如何使用变量来存储数据，介绍了Python的算术运算符，说明必须以单行形式编写所有表达式，并使用内置函数print来显示数据。我们创建了单引号、双引号和三引号字符串，并使用三引号字符串来创建多行字符串和在字符串中嵌入单引号或双引号。

然后，我们演示了如何使用input函数显示提示信息，并获取用户的输入，如有必要，可以使用函数int或float将字符串转换为数值。接着，我们介绍了Python的比较运算符，并在脚本中使用了它们，这些脚本会从用户的输入中读取两个整数，再使用一系列if语句比较这些整数的值。

我们还讨论了Python的动态类型，并使用内置函数type来显示对象的类型。最后，介绍了基础的描述性统计中的最小值和最大值，并使用它们来计算值合集的范围。在下一章中，我们将介绍Python的控制语句。

## 第3章

### 控制语句

### 目标

- 使用if、if...else和if...elif...else语句进行决策。
- 使用while和for重复执行语句。
- 使用增强赋值运算符缩短赋值表达式。
- 使用for语句和内置的range函数重复一系列针对值的操作。
- 使用while执行边界值控制的迭代。
- 使用布尔运算符and、or和not创建复合条件。
- 使用break停止循环。
- 使用continue强制执行循环的下一次迭代。
- 利用函数式编程的特点编写更简洁、更清晰、更易于调试和更易于并行化的脚本。

### 3.1 简介

本章将会介绍Python的控制语句if、if...else、if...elif...else、while、for、break和continue。我们将使用for语句执行序列控制的迭代。在这种迭代中，序列包含的数据项的个数决定了for语句的迭代次数。可以使用Python的内置函数range生成整数序列。

我们还将使用while语句执行边界值控制的迭代，使用Python标准库的Decimal类型进行精确的货币计算，使用各种格式说明符格式化f字符串（即格式字符串）中的数据。我们会演示如何使用布尔运算符and、or和not创建复合条件。在“数据科学入门”部分中，我们将使用Python标准库的statistics模块实现集中趋势度量，包括均值、中值和众数。

### 3.2 控制语句概述

Python提供了三种选择语句，它们根据条件的取值为True或False来执行代码：

- if语句在条件为True时执行操作，如果条件为False，则跳过操作。
- if...else语句在条件为True时执行一种操作；如果条件为False，则执行另外一种操作。
- if...elif...else语句根据多个条件为True或False执行多个不同操作之一。

以上三种选择语句中的操作，既可以是由一条语句完成的单一操作，也可以是由多条语句构成的一组操作。

Python提供了两种迭代语句—while和for，规则如下：

- 只要条件为True，while语句就会重复同一个操作（或一组操作）；
- for语句针对序列中的每一项重复同一个操作（或一组操作）。

### 关键字

if、elif、else、while、for、True和False是Python的关键字。使用关键字作为标识符（如变量名）是语法错误。下表列出了Python的关键字。

| Python关键字 |
|---|
| and |
| as |
| assert |
| async |
| await |
| break |
| class |
| continue |
| def |
| del |
| elif |
| else |
| except |
| False |
| finally |
| for |
| from |
| global |
| if |
| import |
| in |
| is |
| lambda |
| None |
| nonlocal |
| not |
| or |
| pass |
| raise |
| return |
| True |
| try |
| while |
| with |
| yield |

### 3.3 if语句

下面的代码段执行了一次if语句：

```
In [1]: grade = 85
In [2]: if grade >= 60:
    ...:     print('Passed')
    ...:
Passed
```

其中，条件grade >= 60为True，因此if套件中被缩进的print语句显示'Passed'。

### 套件缩进

套件需要有相应的缩进，否则，会引发IndentationError，如下：

```
In [3]: if grade >= 60:
    ...: print('Passed') # statement is not indented properly
File "<ipython-input-3-f42783904220>", line 2
print('Passed') # statement is not indented properly
^
IndentationError: expected an indented block
```

如果套件中包含多条语句，但这些语句没有相同的缩进，也会引发IndentationError，例如：

```
In [4]: if grade >= 60:
    ...:     print('Passed') # indented 4 spaces
    ...:     print('Good job') # incorrectly indented only two spaces
File "<ipython-input-4-8c0d75c127bf>", line 3
print('Good job') # incorrectly indented only two spaces
^
IndentationError: unindent does not match any outer indentation level
```

有些时候，Python给出的错误信息可能不够准确，但它提示需要注意的那一行通常可以让我们弄清楚代码发出了什么错误。不均匀的缩进会降低程序的可读性，因此，在整个代码中应该使用统一的缩进规则。

### 每个表达式都可以被解释为True或False

任何表达式的值都可作为决策依据，非零值为True，零为False，如下所示：

```
In [5]: if 1:
    ...:     print('Nonzero values are true, so this will print')
    ...
Nonzero values are true, so this will print
In [6]: if 0:
    ...:     print('Zero is false, so this will not print')
    ...
In [7]:
```

非空的字符串为True，空字符串（''、'\n'、""）为False。

### 容易混淆的==和=

在赋值语句中使用等于运算符“==”而不是“=”可能会导致不易发现的问题。例如，在下面的代码段中，代码段[1]使用赋值定义了变量grade：

```
grade = 85
```

如果将其写成下面这样：

```
grade == 85
```

那么grade是未定义的，会显示一个NameError。如果在上面的语句之前定义了grade，则grade==85就是一个逻辑错误，这是一个取值为True或False的关系表达式，而不是赋值语句，不会将85赋值给grade。

### 3.4 if...else和if...elif...else语句

if...else语句根据条件是True还是False执行不同的套件：

```
In [1]: grade = 85

In [2]: if grade >= 60:
    ...:     print('Passed')
    ... else:
    ...:     print('Failed')
...
Passed
```

上面的代码段中的条件为True，因此if套件显示'Passed'。需要注意的是，当键入print('Passed') 后按Enter键时，IPython会将下一行缩进四个空格。必须删除这四个空格，以便“else:”套件可以在if的字母i下正确对齐。

下面的代码将57赋值给变量grade，然后再次执行if...else语句，执行结果说明，条件为False时只执行else的套件：

```
In [3]: grade = 57

In [4]: if grade >= 60:
    ...:     print('Passed')
    ... else:
    ...:     print('Failed')
...
Failed
```

使用向上和向下箭头键可以在交互式会话的当前代码段中向后或向前导航，按Enter键可以重新执行选中的代码段。现在，我们将grade赋值为99，按两次向上箭头键导航到代码段[4]，然后按Enter键重新执行该代码段。因为每个被执行的代码段都会获得一个新的ID，因此，该代码段将显示为[6]，如下所示：

```
In [5]: grade = 99

In [6]: if grade >= 60:
    ...:     print('Passed')
    ... else:
    ...:     print('Failed')
...
Passed
```

### 条件表达式

有时，if...else语句中的套件可以根据条件为变量赋不同的值，如下所示：

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

- 如果条件为True，则代码段[10]将if左侧表达式的值，即'Passed'，赋值给result。else部分不执行。
- 如果条件为False，则代码段[10]将else右侧表达式的值，即'Failed'，赋值给result。

在交互模式下，还可以直接评估条件表达式，如下所示：

```
In [12]: 'Passed' if grade >= 60 else 'Failed'
Out[12]: 'Passed'
```

### 套件中的多条语句

下面的代码中，if...else语句的else套件包含两条语句：

```
In [13]: grade = 49

In [14]: if grade >= 60:
    ...:     print('Passed')
    ... else:
    ...:     print('Failed')
    ...:     print('You must take this course again')
    ...:

Failed
You must take this course again
```

上面的代码段中的grade小于60，因此else套件中的两条语句都会被执行。

如果第二个print没有进行缩进，那么它将不被包含在else的套件中。因此，无论if语句的条件是True还是False，该条语句都会被执行，这可能会导致错误的输出：

```
In [15]: grade = 100

In [16]: if grade >= 60:
    ...:     print('Passed')
    ... else:
    ...:     print('Failed')
    ...:     print('You must take this course again')
    ...:

Passed
You must take this course again
```

### if...elif...else语句

可以使用if...elif...else语句测试多种情况。下面的代码段中，等级A表示grade大于等于90，等级B表示grade为80～89，等级C表示grade为70～79，等级D表示grade为60～69，而等级F表示其他成绩。在这几个条件中，仅执行第一个值为True的条件控制的操作。代码段[18]显示结果为C，因为grade的值是77：

```
In [17]: grade = 77

In [18]: if grade >= 90:
    ...:     print('A')
    ... elif grade >= 80:
    ...:     print('B')
    ... elif grade >= 70:
    ...:     print('C')
    ... elif grade > 60:
    ...:     print('D')
    ... else:
    ...:     print('F')
    ...:

C
```

第一个条件grade>=90为False，因此跳过语句print('A')。第二个条件grade>=80也是False，语句print('B')也被跳过。第三个条件grade>=70为True，因此执行语句print('C')。然后跳过if...elif...else语句中的所有剩余代码。if...elif...else语句的执行速度比单独执行多个if语句的速度更快，因为当条件为True时，会立即终止测试。

### else是可选项

if...elif...else语句中的else是可选项。包含else可以使代码处理不满足任何条件的值。当没有else的if...elif语句中的条件全部都为False时，程序不会执行任何语句套件，而是直接执行if...elif语句后的下一条语句。如果在if...elif语句中包含else，则必须把它放在最后一个elif之后；否则，会引发SyntaxError。

### 逻辑错误

代码段[16]中错误地使用缩进是一个非致命逻辑错误的示例。这个代码段可以执行，但会产生错误的结果。如果脚本中存在致命的逻辑错误，会引发异常（例如尝试除以0会引发ZeroDivisionError），Python会显示回溯，然后终止脚本。交互模式中的致命错误仅终止当前代码段，然后IPython会等待下一个输入。

### 3.5 while语句

while语句在循环测试条件保持为True时重复一个或多个操作。下面的代码使用while语句来查找第一个大于50的3的幂：

```
In [1]: product = 3

In [2]: while product <= 50:
    ...: product = product * 3
    ...:

In [3]: product
Out[3]: 81
```

代码段[3]评估product的值为81，这是第一个大于50的3的幂。

while语句套件中必须有某些语句可以更改product的值，使得循环测试条件最终变为False，否则，会进入无限循环。在Terminal、Anaconda命令提示符或shell中执行应用程序时，可以按组合键Ctrl + C或Control + C来终止无限循环。如果使用IDE编写和执行代码，则通常会有用于终止程序执行的工具栏按钮或菜单选项。

### 3.6 for语句

for语句可以为一个序列中的每个项重复一个或多个操作。例如，字符串是由单个字符组成的序列，下面的代码段可以将字符串'Programming'中的每个字符用两个空格分隔开并显示出来：

```
In [1]: for character in 'Programming':
    ...:     print(character, end=' ')
    ...:
    P r o g r a m m i n g
```

for语句按照如下步骤执行：

- 在进入循环语句时，它会将"Programming"中的字母P分配给关键字for和in之间的目标变量，在本例中是character。
- 接下来执行套件中的语句，显示字符的值，后跟两个空格。
- 执行套件后，Python为character分配序列中的下一个项（即"Programming"中的字母r），然后再次执行套件。
- 当序列中还有未处理的项时，循环将会继续，直到所有的项都被处理，循环终止。在本例中，循环在显示最后一个字母g以及其后的两个空格后终止。

在套件中使用目标变量的情况很常见，例如在本例中显示character的值，但也可以不使用。

### print函数的关键字参数end

内置函数print的功能是显示它的参数，然后将光标移到下一行。可以通过设置参数end来修改输出的效果，例如语句

```
print(character, end=' ')
```

会在显示character的值后继续显示两个空格，而不是换行。因此，光标不会移动到下一行。在Python中将end称为关键字参数（也称为命名参数），但end本身不是Python关键字。关键字参数end是可选项，如果参数中不包含end，会使用默认值换行符（'\n'）。Style Guide for Python Code建议在关键字参数的“=”两侧不加空格。
