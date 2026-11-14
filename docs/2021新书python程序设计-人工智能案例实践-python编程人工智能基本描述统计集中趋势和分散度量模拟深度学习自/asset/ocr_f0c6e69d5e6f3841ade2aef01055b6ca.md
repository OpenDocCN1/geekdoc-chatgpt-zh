# 将平均高温数据加载到一个DataFrame中

下面从ave_hi_nyc_jan_1895-2018.csv文件加载并显示纽约市数据：

```
In [7]: nyc = pd.read_csv('ave_hi_nyc_jan_1895-2018.csv')
```

通过查看DataFrame的头部和尾部可以了解数据的情况：

```
In [8]: nyc.head()
Out[8]:
    Date  Value  Anomaly
0  189501   34.2    -3.2
1  189601   34.7    -2.7
2  189701   35.5    -1.9
3  189801   39.6     2.2
4  189901   36.4    -1.0

In [9]: nyc.tail()
Out[9]:
      Date  Value  Anomaly
119  201401   35.5    -1.9
120  201501   36.1    -1.3
121  201601   40.8     3.4
122  201701   42.8     5.4
123  201801   38.7     1.3
```

# 清理数据

后面会使用Seaborn来绘制Date-Value对和回归线。在绘制DataFrame数据时，Seaborn使用DataFrame的列名称作为轴标签。为了便于阅读，这里将'Value'列重命名为'Temperature'：

```
In [10]: nyc.columns = ['Date', 'Temperature', 'Anomaly']

In [11]: nyc.head(3)
Out[11]:
    Date  Temperature  Anomaly
0  189501        34.2     -3.2
1  189601        34.7     -2.7
2  189701        35.5     -1.9
```

Seaborn用Date值标记x轴上的刻度线。本示例仅处理1月份的温度，如果x轴的标签不包含01（1月份），那么会有更好的可读性，因此这里将每个Date数据最后的01删除。首先，检查Date列的数据类型：

```
In [12]: nyc.Date.dtype
Out[12]: dtype('int64')
```