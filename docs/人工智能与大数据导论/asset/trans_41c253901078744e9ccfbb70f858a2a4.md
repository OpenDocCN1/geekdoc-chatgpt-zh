
# 【例9-1】计算矩形的面积：由用户输入矩形的宽和长，计算矩形的面积并输出结果。程序运行的结果如下：

![](images/txt011_2.jpg "pagenumber_ebook=115,pagenumber_book=107")

请输入矩形的宽：7

请输入矩形的长：9

矩形的面积是　63

## 说明

### （1）`input（）`函数

Python提供了内置函数`input（）`从标准输入设备读入数据，默认的标准输入设备是键盘。`input（）`函数的基本格式为：

![](images/txt011_3.jpg "pagenumber_ebook=115,pagenumber_book=107")

说明：`input`函数里的提示字符串是可选项，如果有提示字符串，则运行时原样显示。该函数接收控制台输入，返回输入的字符串。如下代码：

![](images/txt011_4.jpg "pagenumber_ebook=115,pagenumber_book=107")

该行代码运行后会先显示“please input your name：”，然后等待用户输入，此时，若用户输入“Lily”，则会把“Lily”赋值给`name`。`name`的类型为字符串类型。

如果要得到数值型数据，可以使用类型转换函数将字符串转换为数值。比如例9-1的第一行代码：

![](images/txt011_5.jpg "pagenumber_ebook=115,pagenumber_book=107")

该行代码运行后会先显示“请输入矩形的宽：”，然后等待用户输入，此时，若用户输入“20”，则首先`input（）`函数接收到的是字符串“20”，接着`int（）`函数再将字符串“20”转换成整型数值20，最后再把整型数值20赋值给`width`。例9-1中在第二行代码以同样的方法得到矩形的长`length`的值之后，程序执行第三行代码求出计算矩形的面积`area`。

### （2）`print（）`函数

Python提供了内置函数`print（）`来实现标准输出。`print`函数的一般形式为：

![](images/txt011_6.jpg "pagenumber_ebook=115,pagenumber_book=107")

说明：输出项之间用逗号分隔，没有输出项时输出一个空行。`sep`表示输出时各输出项之间的分隔符（默认分隔符是空格），`end`表示输出时的结束符（默认以回车换行结束）。

![](images/txt011_7.jpg "pagenumber_ebook=115,pagenumber_book=107")

以上程序第一行代码实现了将3赋值给`x`，将5赋值给`y`，接着四条`print`语句实现了输出。具体输出结果如下：

![](images/txt011_8.jpg "pagenumber_ebook=115,pagenumber_book=107")

在实际应用中，经常需要将数据按照一定格式输出。在Python中格式化输出时，采用`%`分隔格式控制字符串与输出项，一般格式为：

格式控制字符串`%`（输出项1，输出项2，…，输出项n）

![](images/txt011_9.jpg "pagenumber_ebook=116,pagenumber_book=108")

以上代码的输出如下：

![](images/txt011_10.jpg "pagenumber_ebook=116,pagenumber_book=108")

其中，以上程序第三行代码中“my name is%s and age is%d”属于格式化字符串，该格式化字符串中包含一个“`%s`”占位符和一个“`%d`”占位符，它们依次会被后面圆括号中的第一个输出项`name`的值“Lily”和第二个输出项`age`的值“18”代替。`%s`表示以字符串形式输出，`%d`表示以十进制整数输出。对于不同类型数据的输出，Python采用不同的格式说明符描述。常见的格式说明见表9-1。

表9-1　`print（）`的格式说明符

![](images/txt011_11.jpg "pagenumber_ebook=116,pagenumber_book=108")

另外，在python中还有其他的格式化输出方式，比如也可以使用`str.format（）`函数来格式化输出值。因篇幅原因，在此不详细展开阐述。

## 9.2　选择语句

在实际应用中，有很多情况需要根据给定的条件做出不同的选择，执行不同的操作。我们可以使用选择结构来处理此类问题。在Python中，选择结构根据程序分支的数目，分为单分支结构、双分支结构和多分支结构，分别用`if`语句、`if…else…`语句和`if…elif…else…`语句来实现。接下来将分别对它们进行详细讲解。

### 9.2.1　`if`语句

`if`语句是最简单的条件判断语句，表示选择结构中只有一个分支，只有满足给定的条件才能执行该分支的语句。`if`语句的语法格式如下：

![](images/txt011_12.jpg "pagenumber_ebook=117,pagenumber_book=109")

其中，首先计算“条件表达式”的值，如果值为True，则执行冒号下面的语句块；如果值为False，则不执行冒号下面的语句块。其流程图如图9-2所示。

![](images/txt011_13.jpg "pagenumber_ebook=117,pagenumber_book=109")

图9-2　单分支结构流程图

说明：

（1）`if`语句中，条件表达式后面要使用冒号“：”。

（2）语法格式中的条件表达式，可以为关系表达式、逻辑表达式或算术表达式。如果是算术表达式，则非0值表示True，0表示False。

（3）语法格式中的语句块，可以是一条或者多条语句。如果是多条语句，这些语句必须保持相同的缩进数；如果是一条语句，也可以将该语句与`if`写在同一行中。

【例9-2】编程实现：用户输入一个整数，如果该整数是7的倍数，则输出“该数能被7整除”。

![](images/txt011_14.jpg "pagenumber_ebook=117,pagenumber_book=109")

程序第一次运行的结果如下：

![](images/txt011_15.jpg "pagenumber_ebook=117,pagenumber_book=109")

程序第二次运行的结果如下：

![](images/txt011_16.jpg "pagenumber_ebook=117,pagenumber_book=109")

【例9-3】输入两个数`a`、`b`，如果`a`小于`b`，则交换两数。

![](images/txt011_17.jpg "pagenumber_ebook=117,pagenumber_book=109")

![](images/txt011_18.jpg "pagenumber_ebook=118,pagenumber_book=110")

程序的运行结果如下：

![](images/txt011_19.jpg "pagenumber_ebook=118,pagenumber_book=110")

### 9.2.2　`if…else…`语句

双分支结构表示选择结构中有两个分支，当满足给定的条件时，执行分支1，否则执行分支2。双分支结构`if…else…`语句的语法格式如下：

![](images/txt011_20.jpg "pagenumber_ebook=118,pagenumber_book=110")

其中，首先计算“条件表达式”的值，如果值为True，则执行语句块1；如果值为False，则执行语句块2。其流程图如图9-3所示。

![](images/txt011_21.jpg "pagenumber_ebook=118,pagenumber_book=110")

图9-3　双分支结构流程图

【例9-4】编写程序实现判断一个整数的奇偶性。

![](images/txt011_22.jpg "pagenumber_ebook=118,pagenumber_book=110")

程序第一次运行的结果：

![](images/txt011_23.jpg "pagenumber_ebook=118,pagenumber_book=110")

程序第二次运行的结果：

![](images/txt011_24.jpg "pagenumber_ebook=118,pagenumber_book=110")

【例9-5】输入两个数`a`、`b`，求两数中的最大值。

![](images/txt011_25.jpg "pagenumber_ebook=119,pagenumber_book=111")

程序运行的结果如下：

![](images/txt011_26.jpg "pagenumber_ebook=119,pagenumber_book=111")

### 9.2.3　`if…elif…else…`语句

在开发程序时，经常会遇到需要针对某一事件的多种情况进行处理，这时我们可以使用多分支结构`if…elif…else…`语句。`if…elif…else…`语句的语法格式如下：

![](images/txt011_27.jpg "pagenumber_ebook=119,pagenumber_book=111")

其中，首先计算“条件表达式1”的值，如果值为True，则执行语句块1，且执行完后结束整个多分支语句结构；如果值为False，则接着计算“条件表达式2”的值；如果“条件表达式2”的值为True，则执行语句块2，且执行完后结束整个多分支语句结构；如果“条件表达式2”的值为False，则接着计算“条件表达式3”的值……；依次类推，如果“条件表达式n”的值为True，则执行语句块n，且执行完后结束整个多分支语句结构；如果“条件表达式n”的值为False，则执行`else`下面的语句块n＋1。其流程图如图9-4所示。

![](images/txt011_28.jpg "pagenumber_ebook=120,pagenumber_book=112")

图9-4　多分支结构流程图

说明：

（1）无论有多少个分支，程序一旦执行了某一个分支后则退出整个`if…elif…else…`语句，其余分支不再执行。

（2）`elif`子句的数量没有限制，可以根据需要使用任意多个`elif`子句。

（3）在某些情况下，可能有多个条件表达式的值都为True，但只执行第一个条件表达式为True所对应的语句块，执行后就退出整个`if…elif…else…`语句。

【例9-6】输入学生的百分比成绩，判断其等级。等级判定规则：90分～100分为“优秀”，80分～89分为“良好”，70分～79分为“中等”，60分～69分为“及格”，0分～59分为“不及格”。

![](images/txt011_29.jpg "pagenumber_ebook=120,pagenumber_book=112")

程序运行时，根据用户输入的分数不同，将得到不同的结果。比如用户输入78，则运行结果如下：

![](images/txt011_30.jpg "pagenumber_ebook=120,pagenumber_book=112")

说明：程序中设置条件表达式“80分～89分为良好”时，只限定了“`score`＞＝80”，而没有限定“`score`＜90”，主要是因为如果程序执行到该处，则说明第一个条件表达式“`score`＞＝90”的值为False。如果“`score`＞＝90”表达式的值为False，则`score`值必然是小于90分，所以此处不需要写成“80＜＝`score`＜90”。

### 9.2.4　选择嵌套

在一个选择结构的语句块中包含另外一个选择结构，称为选择结构的嵌套。选择结构的嵌套可以是同一种结构的嵌套，也可以是不同结构之间的嵌套。比如双分支`if…else…`语句中的`if`分支和`else`分支中可以完整地嵌套另一个单分支`if`语句、双分支`if…else…`语句或者多分支语句`if…elif…else…`语句，同样嵌套的语句里又可以再嵌套这些分支语句。这里举一种常见的嵌套格式为例，其语句形式如下：

![](images/txt011_31.jpg "pagenumber_ebook=121,pagenumber_book=113")

其中，首先计算“条件表达式1”的值，如果“条件表达式1”的值为True，则继续判断“条件表达式2”的值；如果“条件表达式1”的值为False，则继续判断“条件表达式3”的值。

说明：

（1）在使用嵌套的选择结构时，内层的选择结构只能处于外层选择结构某个“语句块”的位置，内外层的选择结构不允许交叉。

（2）同一层的选择结构具有相同的缩进数。

【例9-7】输入一个整数，判断该整数能否同时被5和7整除，还是只能被5或者只能被7整除，还是既不能被5也不能被7整除。

![](images/txt011_32.jpg "pagenumber_ebook=121,pagenumber_book=113")

程序的运行结果一：

![](images/txt011_33.jpg "pagenumber_ebook=122,pagenumber_book=114")

程序的运行结果二：

![](images/txt011_34.jpg "pagenumber_ebook=122,pagenumber_book=114")

![](images/txt011_35.jpg "pagenumber_ebook=122,pagenumber_book=114")

程序的运行结果三：

![](images/txt011_36.jpg "pagenumber_ebook=122,pagenumber_book=114")

程序的运行结果四：

![](images/txt011_37.jpg "pagenumber_ebook=122,pagenumber_book=114")

【例9-8】输入3条线段长度，判断这3条线段能否构成三角形，如果可以构成三角形，进一步判断是否是直角三角形。

![](images/txt011_38.jpg "pagenumber_ebook=122,pagenumber_book=114")

程序的运行结果一：

![](images/txt011_39.jpg "pagenumber_ebook=122,pagenumber_book=114")

程序的运行结果二：

![](images/txt011_40.jpg "pagenumber_ebook=122,pagenumber_book=114")

程序的运行结果三：

![](images/txt011_41.jpg "pagenumber_ebook=122,pagenumber_book=114")

![](images/txt011_42.jpg "pagenumber_ebook=123,pagenumber_book=115")

## 9.3　循环语句

在实际应用中，我们也经常遇到需要重复处理的问题，如果仅仅使用之前介绍的顺序结构或者选择结构，则会使程序显得冗长、繁琐、有时候甚至难以实现。循环结构用来重复执行一条或多条语句，使用循环结构可以减少源程序重复书写的工作量，简化程序结构。在Python中，循环结构的流程控制语句主要有`while`语句和`for`语句，下面将对这两者进行详细的讲解。

### 9.3.1　`while`循环

`while`循环语句在条件表达式的值为真时，重复执行一段代码。`while`语句的一般语法格式如下：

![](images/txt011_43.jpg "pagenumber_ebook=123,pagenumber_book=115")

`while`语句的执行步骤：

（1）计算条件表达式的值；

（2）当条件表达式的值为真时，循环条件成立，执行循环语句块，然后继续步骤（1）；

（3）当条件表达式的值为假时，循环条件不成立，不执行循环语句块，执行`while`语句的后续代码。

`while`语句的循环流程图如图9-5所示。

![](images/txt011_44.jpg "pagenumber_ebook=123,pagenumber_book=115")

图9-5　`while`循环流程图

说明：

（1）当循环语句块中只有一条语句时，与`if`语句的语法类似，可以将该语句与`while`写在同一行中。

（2）当循环语句块由多条语句构成时，必须用缩进对齐的方式组成一个语句块来分隔子句，否则会产生错误。

（3）如果条件表达式的值永远为真，则循环将会无限循环下去。

【例9-9】输出1～100这100个整数。

![](images/txt011_45.jpg "pagenumber_ebook=124,pagenumber_book=116")

程序的运行结果如下：

![](images/txt011_46.jpg "pagenumber_ebook=124,pagenumber_book=116")

【例9-10】利用`while`语句求1＋2＋3＋…＋100的和。

![](images/txt011_47.jpg "pagenumber_ebook=124,pagenumber_book=116")

程序的运行结果如下：

![](images/txt011_48.jpg "pagenumber_ebook=124,pagenumber_book=116")

【例9-11】编程实现拉兹猜想。拉兹猜想，又称“3n＋1猜想”、“奇偶归一猜想”，是指对于每一个正整数，如果它是奇数，则对它乘3再加1，如果它是偶数，则对它除以2，如此循环，最终都能够得到1。

![](images/txt011_49.jpg "pagenumber_ebook=124,pagenumber_book=116")

程序运行的结果如下：

![](images/txt011_50.jpg "pagenumber_ebook=124,pagenumber_book=116")

![](images/txt011_51.jpg "pagenumber_ebook=125,pagenumber_book=117")

### 9.3.2　`for`循环

`for`语句是使用较广泛的一种循环控制结构，可用于遍历可迭代对象中的元素。`for`语句的一般格式为：

![](images/txt011_52.jpg "pagenumber_ebook=125,pagenumber_book=117")

`for`语句的执行过程是：将可迭代对象中的元素逐个赋值给变量，对每一次赋值都执行一遍循环语句块。当可迭代对象中的每个元素都被遍历过，则结束循环。`for`语句的执行流程图如图9-6所示。

![](images/txt011_53.jpg "pagenumber_ebook=125,pagenumber_book=117")

图9-6　`for`语句循环流程图
