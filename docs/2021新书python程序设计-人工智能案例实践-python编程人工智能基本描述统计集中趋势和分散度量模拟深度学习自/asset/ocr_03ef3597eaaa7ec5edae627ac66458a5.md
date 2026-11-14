# 2.6 从用户处获取输入

内置函数input请求并获取用户的输入：

```
In [1]: name = input("What's your name? ")
What's your name? Paul
In [2]: name
Out[2]: 'Paul'
In [3]: print(name)
Paul
```

该代码段的执行过程如下：

+   首先， input显示字符串参数作为提示，提示用户要键入的内容并等待用户响应。用户输入Paul并按Enter键。代码段中使用粗体表示用户的输入以与提示文本相区别。

+   然后， input函数将这些字符作为一个字符串返回。上面的代码段将返回的字符串赋值给了变量name。

代码段[2]显示name的值。评估name时显示的值'Paul'带有单引号，表示name是一个字符串。打印name时（在代码段[3]中）则显示不带引号的字符串。如果输入引号，那么引号将成为字符串的一部分，如下：

```
In [4]: name = input("What's your name? ")
What's your name? 'Paul'
In [5]: name
Out[5]: "'Paul'"
In [6]: print(name)
'Paul'
```

# input函数始终返回一个字符串

下面的代码段试图读取两个数字并求和：

```
In [7]: value1 = input('Enter first number: ')
Enter first number: 7
In [8]: value2 = input('Enter second number: ')
Enter second number: 3
In [9]: value1 + value2
Out[9]: '73'
```