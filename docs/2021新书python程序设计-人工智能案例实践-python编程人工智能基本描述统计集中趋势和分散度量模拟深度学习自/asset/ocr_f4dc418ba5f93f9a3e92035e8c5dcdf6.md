```
In [10]: pd.read_sql("""SELECT title, edition, copyright
    ...:     FROM titles
    ...:     WHERE copyright > '2016'""", connection)
Out[10]:
         title  edition copyright
0  Intro to Python for CS and DS        1     2020
1       Java How to Program       11     2018
2   Visual C# How to Program        6     2017
3        C++ How to Program       10     2017
4   Android How to Program        3     2017
```

# 模式匹配：零个或多个字符

WHERE子句可以包含运算符<、>、<=、>=、=、<>（不等于）和 LIKE。运算符LIKE用于模式匹配，搜索与给定模式匹配的字符串。包含百分号（%）通配符的模式搜索表示在模式中，%字符位置处有零个或多个字符的字符串。例如，找到姓氏以字母D开头的所有作者：

```
In [11]: pd.read_sql("""SELECT id, first, last
    ...:     FROM authors
    ...:     WHERE last LIKE 'D%'"",
    ...:     connection, index_col='id'])
Out[11]:
             first    last
id
1      Paul  Deitel
2    Harvey  Deitel
3     Abbey  Deitel
```

# 模式匹配：任意字符

模式字符串中的下划线（_）表示该位置处的单个通配符。例如，在表authors中选择以任何字符开头的姓氏、后跟字母b、再后跟任意数量的附加字符（由%指定）的authors的行：

```
In [12]: pd.read_sql("""SELECT id, first, last
    ...:     FROM authors
    ...:     WHERE first LIKE '_b%'"",
    ...:     connection, index_col='id'])
Out[12]:
             first    last
id
3     Abbey  Deitel
```