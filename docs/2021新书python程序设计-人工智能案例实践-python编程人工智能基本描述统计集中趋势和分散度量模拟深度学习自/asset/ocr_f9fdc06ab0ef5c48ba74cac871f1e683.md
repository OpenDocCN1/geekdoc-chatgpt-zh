# 2.5 三引号字符串

可以使用一对单引号（'）或一对双引号（"）来表示字符串，也可以用一对三引号（三个双引号" " "或三个单引号' ' '）来表示字符串。Style Guide for Python Code推荐使用三个双引号（" " "）。使用三引号可以创建以下三类字符串：

+   - 多行字符串；
+   - 包含单引号或双引号的字符串；
+   - 文档字符串，是用来详细记录某些程序组件用途的一种推荐方法。

# 字符串中包含引号

在由单引号界定的字符串中可以包含双引号：

```
In [1]: print("Display "hi" in quotes")
Display "hi" in quotes
```

但不能包含单引号：

```
In [2]: print("Display 'hi' in quotes")
File "<ipython-input-2-19bf596ccf72>", line 1
print("Display 'hi' in quotes")
^ SyntaxError: invalid syntax
```

如果需要包含单引号，需要使用转义字符 “\,”

```
In [3]: print("Display \"hi\" in quotes")
Display "hi" in quotes
```

由于单引号字符串中含有单引号，代码段[2]显示语法错误。IPython显示引起语法错误的代码行的信息，并使用“^”符号指向发生错误的位置。还会显示更详细的信息 “SyntaxError: invalid syntax”。

由双引号分隔的字符串中可以含有单引号：