```
run fig05_01.py

1 # fig05_01.py
2 """Displaying a bar chart"""
3 numbers = [19, 3, 15, 7, 11]
4 print('\nCreating a bar chart from numbers:')
5 print(f"{'Index':<7}{'Value':>8} Bar")
6
7 for index, value in enumerate(numbers):
8     print(f"{index:<7}{value:>8}{'*' * value}")
```

```
Creating a bar chart from numbers:
Index   Value Bar
0          19 *******************
1           3 ***
2           15 ***************
3           7 *******
4          11 ***********
```

for语句使用enumerate获取每个元素的索引和值，然后格式化显示索引、元素值和相应的星号条。表达式

```
"{0:n} * value
```

创建一个由value个星号组成的字符串。

当与序列一起使用时，用乘法运算符（*）来重复序列—在本例中，字符"*"重复value次。在本章的后面部分，我们将使用开源Seaborn和Matplotlib库来显示出版物质量的柱状图。