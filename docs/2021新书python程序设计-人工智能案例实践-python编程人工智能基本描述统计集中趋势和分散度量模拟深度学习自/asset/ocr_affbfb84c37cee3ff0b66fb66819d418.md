算。此外，Decimal类型也提供一些在银行的日常事务中必须用到的其他功能，例如，在计算账户的每日利息时使用的公平的舍入算法。

# 从decimal模块导入Decimal类型

之前，我们使用过几个Python内置的类型，如int（用于整数，如10）、float（用于浮点数，如7.5）、str（用于字符串，例如'Python'）等。Decimal类型不是Python的内置类型，而是来自Python标准库。Python标准库依据相关的功能被分成不同的模块，decimal是其中的一个模块，定义了Decimal类型及其功能。

要使用Decimal类型，必须首先导入decimal模块，可以导入整个模块，如下：

```
```python
import decimal
```
```

然后使用decimal.Decimal引用Decimal类型，也可以使用from...import指定要导入的特定功能，如下所示：

```
```python
In [4]: from decimal import Decimal
```
```

这种形式的import语句只导入decimal模块中的Decimal类型，因此可以在代码中直接使用它。我们会在下一章讨论其他形式的import。

# 创建Decimal

通常会使用字符串来创建一个Decimal：

```
```python
In [5]: principal = Decimal('1000.00')

In [6]: principal
Out[6]: Decimal('1000.00')

In [7]: rate = Decimal('0.05')

In [8]: rate
Out[8]: Decimal('0.05')
```
```

在随后的复利计算示例中会使用到变量principal和rate。