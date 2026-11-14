使用非整数索引值会引发类型错误（TypeError）。

# 列表是可变的

列表是可变的——它们的元素可以修改：

```
```
In [11]: c[4] = 17

In [12]: c
Out[12]: [-45, 6, 0, 72, 17]
```
```

很快就会看到插入和删除列表元素，列表的长度会同步更改。

# 不可变的序列

Python的字符串和元组序列是不可变的，即它们的元素不能被修改。可以在字符串中获取单个字符，但尝试为其中一个字符赋新值会引发TypeError：

```
```
In [13]: s = 'hello'

In [14]: s[0]
Out[14]: 'h'

In [15]: s[0] = 'H'
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)

<ipython-input-15-812ef2514689> in <module>()
----> 1 s[0] = 'H'

TypeError: 'str' object does not support item assignment
```
```

# 试图访问不存在的元素

索引超出列表、元组或字符串的范围会引发索引错误（IndexError）：

```
```
In [16]: c[100]
---------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)

<ipython-input-16-9a31ealeala13> in <module>()
----> 1 c[100]

IndexError: list index out of range.
```
```