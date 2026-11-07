# Python 深度学习从零开始学

替换空单元格的常用方法是计算列的均值（所有值加起来的平均值）、中位数值（排序后排在中间的数）或众数（出现频率最高的数）。Pandas 分别使用 mean()、median() 和 mode() 方法计算列的均值、中位数值和众数。如图 3-24 所示，使用 mean() 方法计算列的均值并替换空单元格。

```
```python
import pandas as pd
df = pd.read_csv('clean-data.csv')
x = df['ST_NUM'].mean()
df['ST_NUM'].fillna(x, inplace=True)
print(df.to_string())
```
PID  ST_NUM  ST_NAME  OWN_OCCUPIED  NUM_BEDROOMS  NUM_BATH  SQ_FT
0 100001000.0 104.000000 PUTNAM Y 3 1 1000
1 100002000.0 197.000000 LEXINGTON N 3 1.5 —
2 100003000.0 191.428571 LEXINGTON N NaN 1 850
3 100004000.0 201.000000 BERKELEY 12 1 NaN 700
4 NaN 203.000000 BERKELEY Y 3 2 1600
5 100006000.0 207.000000 BERKELEY Y NaN 1 800
6 100007000.0 191.428571 WASHINGTON NaN 2 HURLEY 950
7 100008000.0 213.000000 TREMONT Y 1 1 NaN
8 100009000.0 215.000000 TREMONT Y na 2 1800
```

图 3-24

数据格式错误的单元格会使数据分析变得困难，比如日期格式错误，我们可以将列中的所有单元格转换为相同格式的数据；数据错误也是很常见的情况，比如年龄超过 100 岁，我们可以对错误的数据进行替换或移除。如图 3-25 所示。

如果我们要清洗重复数据，可以使用 duplicated() 和 drop_duplicates() 方法。如果对应的数据是重复的，duplicated() 会返回 True，否则返回 False。删除重复数据，可以直接使用 drop_duplicates() 方法，如图 3-26 所示。

```
```python
import pandas as pd
# 第三日日期格式错误
data = {
  'Date': ['2020/12/01', '2020/12/02', '20201226'],
  'duration': [50, 40, 45]
}
person = [
  'name': ['Google', 'Runoob', 'Taobao'],
  'age': [50, 200, 12345]
]
df1 = pd.DataFrame(data, index = ['day1', 'day2', 'day3'])
df1['Date'] = pd.to_datetime(df1['Date'])
print(df1.to_string())
df2 = pd.DataFrame(person)
for x in df2.index:
  if df2.loc[x, 'age'] > 100:
    df2.loc[x, 'age'] = 100
print(df2.to_string())
```
Date duration
day1 2020-12-01 50
day2 2020-12-02 40
day3 2020-12-26 45
name age
0 Google 50
1 Runoob 100
2 Taobao 100
```

图 3-25

```
```python
import pandas as pd
persons = {
  'name': ['Google', 'Runoob', 'Runoob', 'Taobao'],
  'age': [50, 40, 40, 23]
}
df = pd.DataFrame(persons)
df.drop_duplicates(inplace = True)
print(df)
```
name age
0 Google 50
1 Runoob 40
3 Taobao 23
```

图 3-26