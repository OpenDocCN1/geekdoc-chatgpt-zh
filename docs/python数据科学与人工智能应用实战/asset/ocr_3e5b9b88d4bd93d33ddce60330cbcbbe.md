是写入，False 是不写入；而 encoding 是编码。调用 to_json() 函数导出 JSON 格式文件，其参数字符串就是文件名。

在上述程序的运行结果中，可以在 Python 程序的相同目录看到 2 个文件：dists.csv 和 dists.json。

# 动手学——导入文件数据至 DataFrame 对象：Ch9_2_2a.py

在成功导出 dists.csv 和 dists.json 文件后，可以分别调用 read_csv() 函数和 read_json() 函数来导入文件数据，如下所示：

```
# 导入 CSV 格式的文件
df = pd.read_csv("dists.csv", encoding="utf-8")
print(df)
# 导入 JSON 格式的文件
df2 = pd.read_json("dists.json")
print(df2)
```

上述程序代码导入 dists.csv 和 dists.json 文件成为 DataFrame 对象，其对象内容和 Ch9_2_1.py 程序相同，故不再重复列出。

# 9.2.3 输出基本信息

当自行创建或导入文件成为 DataFrame 对象后，即可使用相关函数和属性来输出 DataFrame 对象的基本信息。

本书的 Python 范例程序都是导入 dists.csv 文件来创建 DataFrame 对象 df，如下所示：

```
df = pd.read_csv("dists.csv", encoding="utf-8")
```

# 动手学——输出前几项记录：Ch9_2_3.py

为了方便说明，笔者采用的是 SQL 数据库的术语，DataFrame 对象的每一行是一项记录，每一列是记录的字段。可以调用 head() 函数输出前几项记录，默认是 5 项，如下所示：

```
print(df.head())
print(df.head(3))
```

上述程序代码中，第 1 个 head() 函数没有参数，默认是 5 项 [图 9-7 (a)]；第 2 个 head() 函数指定参数值 3，表示输出前 3 项记录 [图 9-7 (b)]。

| | city | name | population |
|---|---|---|---|
| 0 | 台北市 | 中正区 | 159598 |
| 1 | 新北市 | 板桥区 | 551452 |
| 2 | 桃园市 | 桃园区 | 441287 |
| 3 | 台中市 | 北屯区 | 275207 |
| 4 | 台南市 | 安南区 | 192327 |

| | city | name | population |
|---|---|---|---|
| 0 | 台北市 | 中正区 | 159598 |
| 1 | 新北市 | 板桥区 | 551452 |
| 2 | 桃园市 | 桃园区 | 441287 |