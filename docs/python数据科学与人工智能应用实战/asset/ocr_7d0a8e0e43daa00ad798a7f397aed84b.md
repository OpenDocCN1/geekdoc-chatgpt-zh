| | city | name | population |
|---|---|---|---|
| second | 新北市 | 板桥区 | 551452 |
| third | 桃园市 | 桃园区 | 441287 |
| eleventh | 高雄市 | 凤山区 | 359125 |

图 9-20

| | city | name | population |
|---|---|---|---|
| first | 台北市 | 中正区 | 159598 |
| sixth | 高雄市 | 三民区 | 343203 |
| seventh | 台北市 | 大安区 | 309835 |
| tenth | 高雄市 | 前镇区 | 189623 |
| eleventh | 高雄市 | 凤山区 | 359125 |
| twelfth | 台北市 | 信义区 | 225561 |

图 9-21

# 动手学——使用多个条件和字符串函数过滤数据： Ch9_3_2a.py

在布尔索引可以使用多个条件。例如，人口大于 350000，且小于 500000，如下所示：

```
print(df[(df.population > 350000) & (df.population < 500000)])
print(df[df["city"].str.startswith("合")])
```

上述程序代码中，第 1 行的索引条件是“&”（且）[图 9-22（a）]，第 2 行是调用 str.startswith() 字符串函数搜索前缀是“合”的城市 [图 9-22（b）]。

| | city | name | population |
|---|---|---|---|
| third | 桃园市 | 桃园区 | 441287 |
| eleventh | 高雄市 | 凤山区 | 359125 |

(a)

| | city | name | population |
|---|---|---|---|
| first | 台北市 | 中正区 | 159598 |
| fourth | 台中市 | 北屯区 | 275207 |
| fifth | 台南市 | 安南区 | 192327 |
| seventh | 台北市 | 大安区 | 309835 |
| twelfth | 台北市 | 信义区 | 225561 |

(b)

图 9-22

# 9.3.3 排序数据

当 DataFrame 对象调用 set_index() 函数指定索引字段后，可以调用 sort_index() 函数指定索引字段的排序方式，或调用 sort_values() 函数指定特定字段值来进行排序。

# 动手学——指定索引字段排序： Ch9_3_3.py

将 DataFrame 对象改用 population 字段作为索引，然后指定从大到小排序，如下所示：

```
df2 = df.set_index("population")
print(df2.head())
df2.sort_index(ascending=False, inplace=True)
print(df2.head())
```

上述程序代码调用 set_index() 函数指定索引字段且创建新的 DataFrame 对象 df2，可以看到 DataFrame 对象改用 population 字段作为索引 [图 9-23（a）]。然后调用 sort_index() 函数指定 ascending 参数值为 False，从大到小排序；inplace 参数值为 True，直接取代原来 DataFrame 对象 df2 [图 9-23（b）]。

图 9-23（a）只指定了索引字段，并没有排序；图 9-23（b）则从大到小排序 population 字段。