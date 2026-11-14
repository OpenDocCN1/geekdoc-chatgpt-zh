# 3.3 if语句

下面的代码段执行了一次if语句：

```
In [1]: grade = 85
In [2]: if grade >= 60:
    ...:     print('Passed')
    ...:
Passed
```

其中，条件grade >= 60为True，因此if套件中被缩进的print语句显示'passed'。

# 套件缩进

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
print('good job') # incorrectly indented only two spaces
^
IndentationError: unindent does not match any outer indentation level
```

有些时候，Python给出的错误信息可能不够准确，但它提示需要注意的那一行通常可以让我们弄清楚代码发出了什么错误。不均匀的缩进会降低程序的可读性，因此，在整个代码中应该使用统一的缩进规则。

# 每个表达式都可以被解释为True或False