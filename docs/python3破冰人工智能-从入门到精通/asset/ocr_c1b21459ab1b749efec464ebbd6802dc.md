输出：

移除所有行字段中有值属性小于 4 的行, df2.dropna(thresh=4)=

|   | one | two | three | four | five |
|---|---|---|---|---|---|
| a | NaN | -100.0 | 7.0 | bar | False |
| c | 8.0 | -99.0 | -99.0 | bar | True |
| e | 6.0 | 2.0 | 3.0 | bar | True |
| f | 3.0 | 6.0 | 5.0 | bar | True |
| h | 9.0 | 7.0 | 3.0 | bar | True |

输入：

```
print("参数 subset 移除指定列为空的所在行据,df2.dropna(subset=['one',
'five'])]=\n{}".format (df2.dropna(subset=['one', 'five'])))
```

输出：

参数 subset 移除指定列为空的所在行数据,df2.dropna(subset=['one', 'five'])=

|   | one | two | three | four | five |
|---|---|---|---|---|---|
| c | 8.0 | -99.0 | -99.0 | bar | True |
| e | 6.0 | 2.0 | 3.0 | bar | True |
| f | 3.0 | 6.0 | 5.0 | bar | True |
| h | 9.0 | 7.0 | 3.0 | bar | True |

# #缺失值填充 fillna()

输入：

```
print("缺失值以0填充, df2.fillna(0)=\n{}".format(df2.fillna(0)))
```

输出：

缺失值以0填充, df2.fillna(0)=

|   | one | two | three | four | five |
|---|---|---|---|---|---|
| a | 0.0 | -100.0 | 7.0 | bar | False |
| b | 0.0 | 0.0 | 0.0 | 0 | 0 |
| c | 8.0 | -99.0 | -99.0 | bar | True |
| d | 0.0 | 0.0 | 0.0 | 0 | 0 |
| e | 6.0 | 2.0 | 3.0 | bar | True |
| f | 3.0 | 6.0 | 5.0 | bar | True |
| g | 0.0 | 0.0 | 0.0 | 0 | 0 |
| h | 9.0 | 7.0 | 3.0 | bar | True |

输入：

```
print("指定列空值赋值为\n{}".format(df2.fillna({"one":0,"two":0.5,
"five":10})))
```