or) 时，其运算结果就是布尔值。首先介绍逻辑运算符（Python 程序：Ch2_2_3b.py），如下所示：

```
a = True
b = False
print(type(a))      # 输出"<class 'bool'>"
print(a and b)      # 逻辑 and: 输出"False"
print(a or b)       # 逻辑 or: 输出"True"
print(not a)        # 逻辑 not: 输出"False"
```

上述程序代码指定变量是布尔值后，依序执行 and、or 和 not 运算。

然后介绍关系运算符（Python 程序： Ch2_2_3c.py），如下所示：

```
a = 3
b = 4
print(a == b)   # 相等: 输出 "False"
print(a != b)   # 不等: 输出 "True"
print(a > b)    # 大于: 输出 "False"
print(a >= b)   # 大于等于: 输出 "False"
print(a < b)    # 小于: 输出 "True"
print(a <= b)   # 小于等于: 输出 "True"
```

# 4. 字符串

Python 字符串并不能更改字符串内容，所有字符串的变更都是建立一个全新字符串。Python 字符串是使用单引号 "" 或双引号 "" 括起的一系列 Unicode 字符，如下所示：

```
s1 = "学习 Python 语言程序设计"
s2 = 'Hello World!'
```

上述程序代码的变量是字符串数据类型。Python 语言并没有字符类型，当引号括起的字符串只有 1 个时，就是字符，如下所示：

```
ch1 = "A"
ch2 = 'b'
```

上述程序代码的变量是字符。当在 Python 程序创建字符串后，就可以输出字符串、计算字符串长度、连接 2 个字符串和格式化输出字符串内容（Python 程序： Ch2_2_3d.py），如下所示：

```
str1 = 'hello'      # 使用单引号创建字符串
str2 = "python"     # 使用双引号创建字符串
print(str1)         # 输出 "hello"
```