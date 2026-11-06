输出：

|   | one | two | three | four | five |
|---|---|---|---|---|---|
| a | NaN | -100.0 | 7.0 | bar | False |
| b | NaN | NaN | NaN | NaN | NaN |
| c | 8.0 | -99.0 | -99.0 | bar | True |
| d | NaN | NaN | NaN | NaN | NaN |
| e | 6.0 | 2.0 | 3.0 | bar | True |
| f | 3.0 | 6.0 | 5.0 | bar | True |
| g | NaN | NaN | NaN | NaN | NaN |
| h | 9.0 | 7.0 | 3.0 | bar | True |

# 【例 4-6】基于例 4-5 df2 进行缺失值处理

```
```python
#丢弃缺失值 dropna()
Input:
print("删除缺失值所在行(axis=0)或列(axis=1)，默认为 axis=0, df2.dropna(axis=0)=\n{}".format(df2.dropna(axis=0)))
```
```

输出：

|   | one | two | three | four | five |
|---|---|---|---|---|---|
| c | 8.0 | -99.0 | -99.0 | bar | True |
| e | 6.0 | 2.0 | 3.0 | bar | True |
| f | 3.0 | 6.0 | 5.0 | bar | True |
| h | 9.0 | 7.0 | 3.0 | bar | True |

输入：

```
```python
print("一行中全部为 NaN 的，才丢弃该行 df2.dropna(how='all')=\n{}".format(df2.dropna (how='all')))
```
```

输出：

|   | one | two | three | four | five |
|---|---|---|---|---|---|
| a | NaN | -100.0 | 7.0 | bar | False |
| c | 8.0 | -99.0 | -99.0 | bar | True |
| e | 6.0 | 2.0 | 3.0 | bar | True |
| f | 3.0 | 6.0 | 5.0 | bar | True |
| h | 9.0 | 7.0 | 3.0 | bar | True |

输入：

```
```python
print("移除所有字段中有值属性小于 4 的行, df2.dropna(thresh=4)=\n{}".format(df2.dropna (thresh=4)))
```
```