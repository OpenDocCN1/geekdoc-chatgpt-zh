

# 9.3 循环语句

## 9.3.2 for 语句与可迭代对象

可迭代对象可以是列表。

### 例9-12 用for语句遍历列表

![](images/txt011_54.jpg "pagenumber_ebook=125,pagenumber_book=117")

程序的运行结果如下：

![](images/txt011_55.jpg "pagenumber_ebook=125,pagenumber_book=117")

说明：

`num_list`是一个列表（“`list`”），列表是python的数据类型之一，在下一章会详细介绍。在例9-12中，列表中有6个元素，`for`语句的执行过程是：将列表中的元素6，3，4，5，1，9依次逐个赋值给变量`num`，每一次赋值都执行一遍语句`print（num）`。整个程序实现了遍历列表中的每个元素，并把元素打印输出。

可迭代对象可以是`range（）`函数返回的指定范围的数字序列。如果我们要用`for`循环输出1～100，在列表中写100个元素是比较不切实际的，python提供了一个很方便的函数`range（）`，它可以生成一段序列。`range（）`函数原型如下：

![](images/txt011_56.jpg "pagenumber_ebook=126,pagenumber_book=118")

函数说明：`range`生成的数字序列从`start`开始，默认是从0开始，到`end`结束（包含`start`，但不包含`end`）。参数`step`指定了可选的步长，序列按照步长`step`的增量改变，`step`可以为正数，可以为负数；`step`如果省略，则步长默认为1。

`range（5）`的序列为0　1　2　3　4　5；
`range（3，8）`的序列为3　4　5　6　7；
`range（0，10，3）`的序列为0　3　6　9；
`range（2，－20，－4）`的序列为2　－2　－6　－10　－14　－18。

### 例9-13 用for语句输出1～100

![](images/txt011_57.jpg "pagenumber_ebook=126,pagenumber_book=118")

程序运行的结果是：

![](images/txt011_58.jpg "pagenumber_ebook=126,pagenumber_book=118")

### 例9-14 用for语句输出1～100中的偶数

![](images/txt011_59.jpg "pagenumber_ebook=126,pagenumber_book=118")

程序运行的结果是：

![](images/txt011_60.jpg "pagenumber_ebook=126,pagenumber_book=118")

![](images/txt011_61.jpg "pagenumber_ebook=127,pagenumber_book=119")

### 例9-15 利用for语句求1＋2＋3＋…＋100的和

![](images/txt011_62.jpg "pagenumber_ebook=127,pagenumber_book=119")

程序运行的结果是：

![](images/txt011_63.jpg "pagenumber_ebook=127,pagenumber_book=119")

### 例9-16 利用for语句求10的阶乘

![](images/txt011_64.jpg "pagenumber_ebook=127,pagenumber_book=119")

程序运行的结果是：

![](images/txt011_65.jpg "pagenumber_ebook=127,pagenumber_book=119")

可迭代对象也可以是字符串。

### 例9-17 用for语句遍历字符串“python”中的每个字符

![](images/txt011_66.jpg "pagenumber_ebook=127,pagenumber_book=119")

程序运行的结果是：

![](images/txt011_67.jpg "pagenumber_ebook=127,pagenumber_book=119")

此外，可迭代对象还可以是元组（`tuple`）、字典（`dict`）、迭代器对象（`iterator`）、生成器函数（`generator`）等。具体的`for`语句使用方法将在后续章节结合相关概念讲解。

## 9.3.3 循环嵌套

在一个循环结构的循环体内包含另一个完整的循环结构，则称之为循环的嵌套。循环语句`while`和`for`可以互相嵌套。在使用循环嵌套时，应注意以下几个问题：

（1）外层循环和内层循环控制变量不能同名，以免造成混乱。
（2）循环嵌套不能交叉，即在一个循环体内必须完整地包含另一个循环。

### 例9-18 找出所有的三位数

找出所有的三位数，使得它满足如下条件：它的各位数字的立方和正好等于这个三位数。例如153＝1³＋5³＋3³。

分析：假设所求的三位数的百位数字为`a`，十位数字为`b`，个位数字为`c`，则

![](images/txt011_68.jpg "pagenumber_ebook=128,pagenumber_book=120")

程序运行结果如下：

![](images/txt011_69.jpg "pagenumber_ebook=128,pagenumber_book=120")

## 9.3.4 其他控制语句

有时候需要在循环体中提前跳出循环，或者在某种条件满足时，不执行循环体中的某些语句而从头开始新的一轮循环，此时可以用`break`、`continue`、`pass`、`else`等循环控制语句。下面分别进行介绍。

### 9.3.4.1 break语句和continue语句

`break`语句和`continue`语句可以用来辅助控制循环执行，更改语句执行的顺序。

`break`语句在`for`循环和`while`循环中都可以使用，一旦`break`被执行，将提前结束循环，继续执行循环结构后面的语句。如果`break`使用在嵌套循环中，则将停止执行所在层的循环，而外层循环将会继续进行。

`continue`语句用来跳过本轮循环剩余的语句，提前进行下一轮循环。

由例9-19和例9-20可以看出`break`语句和`continue`语句的区别。

#### 例9-19 break在循环体中的使用

![](images/txt011_70.jpg "pagenumber_ebook=128,pagenumber_book=120")

程序运行的结果如下：

![](images/txt011_71.jpg "pagenumber_ebook=128,pagenumber_book=120")

#### 例9-20 continue在循环体中的使用

![](images/txt011_72.jpg "pagenumber_ebook=128,pagenumber_book=120")

![](images/txt011_73.jpg "pagenumber_ebook=129,pagenumber_book=121")

程序运行的结果如下：

![](images/txt011_74.jpg "pagenumber_ebook=129,pagenumber_book=121")

### 9.3.4.2 pass语句

`pass`语句是一个空语句，它不做任何操作，代表一个空操作。`pass`语句用于在某些场合下语法上需要一个语句但实际上却什么都不做的情况，相当于一个占位符，有时候起到保证格式或者语义完整性的作用。

例如，在Python中，循环结构中的循环语句块，可以包含一条语句，也可以包含多条语句，但是却不可以没有任何语句。如果只是想让程序循环一定次数，但是循环过程中什么也不做，就可以使用`pass`语句。如下代码循环了100次，但是除了循环以外，它什么也没有做：

![](images/txt011_75.jpg "pagenumber_ebook=129,pagenumber_book=121")

### 9.3.4.3 else语句

在Python中，循环语句`for`语句和`while`语句都存在一种扩展用法，可以附带一个`else`子句（可选）。

`else`子句在`while`循环语句中的语法格式如下：

![](images/txt011_76.jpg "pagenumber_ebook=129,pagenumber_book=121")

`else`子句在`for`循环语句中的语法格式如下：

![](images/txt011_77.jpg "pagenumber_ebook=129,pagenumber_book=121")

不管条件表达式是真还是假，`else`中的子语句块会在循环正常执行完的情况下执行。但是如果循环是以非正常方式（如`break`语句、`return`语句等）退出，那么`else`子句将不会被执行。

#### 例9-21 循环正常执行下else用法

![](images/txt011_78.jpg "pagenumber_ebook=129,pagenumber_book=121")

程序第一次运行的结果如下：

![](images/txt011_79.jpg "pagenumber_ebook=130,pagenumber_book=122")

程序第二次运行的结果如下：

![](images/txt011_80.jpg "pagenumber_ebook=130,pagenumber_book=122")

#### 例9-22 循环非正常退出下else用法（猜数字游戏）

![](images/txt011_81.jpg "pagenumber_ebook=130,pagenumber_book=122")

## 9.4 综合实例

#### 例9-23 打印九九乘法表

![](images/txt011_82.jpg "pagenumber_ebook=130,pagenumber_book=122")

程序运行的结果如下：

![](images/txt011_83.jpg "pagenumber_ebook=131,pagenumber_book=123")

#### 例9-24 输出一个由∗组成的菱形

![](images/txt011_84.jpg "pagenumber_ebook=131,pagenumber_book=123")

程序运行的结果如下：

![](images/txt011_85.jpg "pagenumber_ebook=131,pagenumber_book=123")

#### 例9-25 打印输出不大于500的最大斐波那契数

分析：斐波那契数列，指的是这样一个数列：0、1、1、2、3、5、8、13、21、…在数学上，斐波那契数列以递归的方法定义：`F（0）＝0`，`F（1）＝1`，`F（n）＝F（n－1）＋F（n－2）`（`n＞＝2`，`n∈N∗`），

要求不大于500的最大斐波那契数，可根据公式一直循环求出，因不知道确切的循环次数，可采用`while`循环。具体代码如下：

![](images/txt011_86.jpg "pagenumber_ebook=131,pagenumber_book=123")

程序的运行结果如下：

![](images/txt011_87.jpg "pagenumber_ebook=131,pagenumber_book=123")

#### 例9-26 打印2～200的全部素数

![](images/txt011_88.jpg "pagenumber_ebook=132,pagenumber_book=124")

程序运行的结果如下：

![](images/txt011_89.jpg "pagenumber_ebook=132,pagenumber_book=124")

#### 例9-27 猴子吃桃问题

猴子第一天摘下若干个桃子，当即吃了一半，还不过瘾，又多吃了一个。第二天早上又将剩下的桃子吃掉一半，又多吃了一个。以后每天早上都吃了前一天剩下的一半零一个。到第10天早上想再吃时，见只剩下一个桃子了。求第一天共摘了多少。

![](images/txt011_90.jpg "pagenumber_ebook=132,pagenumber_book=124")

程序的运行结果如下：

![](images/txt011_91.jpg "pagenumber_ebook=132,pagenumber_book=124")

#### 例9-28 百鸡问题

假设公鸡每只5元，母鸡每只3元，小鸡1元3只。现有100元，要买100只鸡。试编程求出公鸡、母鸡和小鸡各买多少只。

![](images/txt011_92.jpg "pagenumber_ebook=132,pagenumber_book=124")

程序运行的结果是：

![](images/txt011_93.jpg "pagenumber_ebook=133,pagenumber_book=125")

## 9.5 课后练习

一、选择题

1.阅读以下程序：

![](images/txt011_94.jpg "pagenumber_ebook=133,pagenumber_book=125")

程序运行后，如果从键盘上输入5，则输出结果是（）。

A.－5
B.0
C.5
D.10

2.向横线处加入合适的语句，使程序正确运行（）。

![](images/txt011_95.jpg "pagenumber_ebook=133,pagenumber_book=125")

A.`x%2＝0`
B.`x%2＝＝0`
C.`x%2＝＝1`
D.`x%2`

3.请阅读下面一段程序：

![](images/txt011_96.jpg "pagenumber_ebook=133,pagenumber_book=125")

下列语句中，能够填写到程序中横线处的是（）。

A.`pass`
B.`else`
C.`elif`
D.`for`

4.下列代码段的输出为（）。

![](images/txt011_97.jpg "pagenumber_ebook=133,pagenumber_book=125")

A.3,
B.0,1,2,
C.0,1,2,3,
D.1,2,3,

5.以下代码的含义是（）。

![](images/txt011_98.jpg "pagenumber_ebook=134,pagenumber_book=126")

A.计算1＋10的和
B.计算1＋9的和
C.计算1～9的所有整数和
D.计算1～10的所有整数和

6.python中表示跳出循环的语句是（）。

A.`continue`
B.`break`
C.`close`
D.Esc

7.python中表示跳过本轮循环剩余的语句，提前进行下一轮循环的语句是（）。

A.`continue`
B.`break`
C.`close`
D.Esc

8.请阅读下面一段程序：

![](images/txt011_99.jpg "pagenumber_ebook=134,pagenumber_book=126")

运行程序后，最终执行的结果为（）。

A.0
B.10
C.11
D.15

9.下列Python语句正确的是（）。

A.`min＝x if x＜y else y`
B.`ax＝x＞y?x:y`
C.`if(x＞y)print x`
D.`while True:pass`

二、填空题

1.计算机程序一般分为三种基本结构：顺序结构、\_\_\_\_\_\_\_\_\_以及\_\_\_\_\_\_\_\_\_。
2.如果希望循环是无限的，我们可以通过设置条件表达式永远为\_\_\_\_\_\_\_\_\_来实现无限循环。
3.Python中的\_\_\_\_\_\_\_\_\_表示的是空语句。

三、编程题

1.请按照以下要求操作。

要求如下：
（1）通过变量`radius`接收用户输入的一个整数，表示圆的半径；
（2）计算该圆的周长，并输出；
（3）计算该圆的面积，并输出。

2.请按照以下要求操作。

要求如下：
（1）通过变量`num1`和`num2`接收用户输入的两个整数；
（2）比较这两个整数的大小。

3.请按照以下要求操作。

要求如下：
（1）通过变量`year`接收用户输入的一个数值，表示年份；
（2）判断该年是否是闰年。

4.请按照以下要求操作。

要求如下：
（1）通过变量`num1`和`num2`接收用户输入的两个数值；
（2）定义一个表示计算符号的变量`calculate`；
（3）如果`calculate`为“＋”，计算`num1`和`num2`的和；如果为“－”，计算它们的差；如果为“∗”，计算它们的乘积；
（4）如果`calculate`为“/”，必须保证`num2`的值不能为0。如果`num2`的值为0，则输出相应提示“除数不能为0”，如果`num2`的值不为0，则输出`num1`和`num2`相除的结果。

5.用`for`循环编程实现求2＋4＋6＋…＋100的和。
6.用`while`循环编程实现求1＋4＋9＋16＋25＋…＋100的和。
7.用`while`循环，输出如下九九乘法表。

![](images/txt011_100.jpg "pagenumber_ebook=135,pagenumber_book=127")

8.请按照以下要求操作。

（1）通过变量`n`接收用户输入的一个数值；
（2）求2至`n`之间有多少个素数。

# 第10章 列表、元组和字典

现需要统计某学校某专业的学生某门课程的成绩情况，例如计算课程的平均分、最高分、最低分等。假设该专业的学生有180人，用前面所学的知识，我们需要定义180个变量来存储每位学生的成绩，这样做会显得很繁琐。

在Python中，可以使用序列来解决上述问题。序列是Python中最基本的数据结构。序列中的每个数据被称作元素，每个元素都分配一个数字——它的位置或索引，第一个元素的索引是0，第二个元素的索引是1，以此类推。在Python序列的内置类型中，最常见的是列表和元组。除此之外，Python还提供了一种存储数据的容器——字典。接下来，本章将对列表、元组和字典等进行详细讲解。

## 10.1 列表

### 10.1.1 概述

列表`List`是Python中使用最频繁的数据类型。可以通过方括号来创建一个`list`结构，方括里可以放任何类型的元素，并支持嵌套，元素间用逗号分隔开。列表没有长度限制。列表的具体定义格式如下：

列表名＝\[元素1，元素2，元素3，…\]

其中，列表中的元素可以都是相同数据类型的数据，也可以是不同数据类型的数据。

#### 例10-1 定义列表

![](images/txt012_1.jpg "pagenumber_ebook=136,pagenumber_book=128")

