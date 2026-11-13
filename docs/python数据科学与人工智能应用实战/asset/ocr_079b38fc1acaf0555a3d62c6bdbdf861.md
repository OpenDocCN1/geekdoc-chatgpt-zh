```
print(df["population"].head(3))
```

上述程序代码获取 population 单一字段，单一字段是 Series 对象。
也可以使用对象属性方式来选择相同的字段，如下所示：

```
print(df.population.head(3))
```

上述程序代码使用 df.population 选择此字段，然后调用 head(3) 函数输出前 3 项记录。
其运行结果如下所示：

```
first    159598
second   551452
third    441287
Name: population, dtype: int64
```

上述运行结果中，最后一行是字段名和数据类型。
也可以使用标签索引列表（字段名列表）来同时选择多个字段，如下所示：

```
print(df[["city", "name"]].head(3))
```

| | city | name |
|---|---|---|
| first | 台北市 | 中正区 |
| second | 新北市 | 板桥区 |
| third | 桃园市 | 桃园区 |

上述程序代码选择 city 和 name 两个字段的前 2 项记录，因为 DataFrame
对象支持 to_html() 函数（Series 对象不支持），所以可以产生 HTML 表格
的运行结果，如图 9-12 所示。

# 动手学——选择特定范围的多项记录：Ch9_3_1a.py

对于 DataFrame 对象每一行记录来说，可以使用从 0 开始的索引，或自定义索引的标签
名称来选择特定范围的记录。首先是数值索引范围，如下所示：

```
print(df[0:3])
```

上述索引值范围如同列表和 NumPy 分割运算符，可以选择第 1~3 项记录，但不含索引
值为 3 的第 4 项记录。其运行结果如图 9-13 所示。
如果是使用自定义索引的标签名称，那么此时的范围会包含最后一项记录，即 eleventh，
如下所示：

```
print(df["sixth":"eleventh"])
```

上述程序代码选择索引 sixth 到 eleventh，包含 eleventh。其运行结果如图 9-14 所示。

| | city | name | population |
|---|---|---|---|
| first | 台北市 | 中正区 | 159598 |
| second | 新北市 | 板桥区 | 551452 |
| third | 桃园市 | 桃园区 | 441287 |

| | city | name | population |
|---|---|---|---|
| sixth | 高雄市 | 三民区 | 343203 |
| seventh | 台北市 | 大安区 | 309835 |
| eighth | 新北市 | 永和区 | 222531 |
| ninth | 桃园市 | 八德区 | 198473 |
| tenth | 高雄市 | 前镇区 | 189623 |
| eleventh | 高雄市 | 凤山区 | 359125 |