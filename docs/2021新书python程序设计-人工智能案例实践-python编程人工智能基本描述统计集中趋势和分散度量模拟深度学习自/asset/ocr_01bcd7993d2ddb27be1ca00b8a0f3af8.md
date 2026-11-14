# 10.2 自定义Account类

我们以银行的Account（账户）类开始本节内容，这里考虑的Account类仅包括持有者姓名和余额这两个信息。实际上，银行账户类通常还会包含许多其他信息，如地址、出生日期、电话号码、账户编号等，这里做了问题的简化处理以方便读者通过简短的代码理解自定义类的方法。Account类具有使余额增加的存款操作和使余额减少的取款操作。

# 10.2.1 试用Account类

我们创建的每个新类都是一种新的数据类型，可以用于创建对象，这是Python被称为是可扩展语言的一个原因。在开始查看Account类的定义之前，先演示一下Account类的功能。

# 导入Account类和Decimal类

为了使用Account类，从ch10文件夹启动IPython会话，然后导入Account类：

```
In [1]: from account import Account
```

Account类以Decimal类型保存和操作账户余额，因此我们也导入Decimal类：

```
In [2]: from decimal import Decimal
```

# 使用构造器表达式创建Account对象

为了创建Decimal对象，可以写：

```
value = Decimal('12.34')
```