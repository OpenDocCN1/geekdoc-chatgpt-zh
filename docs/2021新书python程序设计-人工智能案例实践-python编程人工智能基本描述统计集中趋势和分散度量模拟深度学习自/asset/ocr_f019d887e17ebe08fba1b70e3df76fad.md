```
In [4]: print("Display the name O'Brien")
Display the name O'Brien
```

但不能包含双引号，如需包含双引号，要使用转义字符 “\” :

```
In [5]: print("Display \"hi\" in quotes")
Display "hi" in quotes
```

为了避免在字符串中使用 “\” 和 “\” , 可以将这些字符串括在三引号中，例如：

```
In [6]: print("""Display "hi" and 'bye' in quotes""")
Display "hi" and 'bye' in quotes
```

# 多行字符串

下面的代码段将三引号括起来的多行字符串赋值给变量 triple_quoted_string:

```
In [7]: triple_quoted_string = """This is a triple-quoted
... string that spans two lines"""
```

因为在按下Enter键之前没有输入结束符 ( “ “ ” )，因此IPython知道字符串是不完整的，会显示一个延续提示符 “...” 。可在其后面输入多行字符串的下一行，直到输入结束符 ( “ “ ” ) 并按Enter键。下面的代码段显示triple_quoted_string的值：

```
In [8]: print(triple_quoted_string)
This is a triple-quoted
string that spans two lines
```

Python通过嵌入换行符来存储多行字符串。例如，当评估triple_quoted_string而不是打印它时，IPython会将它显示在单引号中，并在代码段[7]中按Enter键的位置显示一个转义字符 “\n”。IPython显示的引号不是字符串的一部分，而是表示triple_quoted_string的类型为字符串，显示如下：

```
In [9]: triple_quoted_string
Out[9]: 'This is a triple-quoted\nstring that spans two lines'
```