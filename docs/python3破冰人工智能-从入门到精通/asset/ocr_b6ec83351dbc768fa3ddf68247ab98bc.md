输出：

Unnamed: 0  Column A  Column B
0  0  0.693188  -1.133000
1  1  0.222813  1.257744
2  2  -0.322323  -0.426994

read_table()的默认分隔符为"t", 所以需要制定 sep=","来进行操作, 否则将被当成一列来处理。

# 【例 7-5】read_csv 与 read_table 对比

输入：

```
df2=pd.read_csv("AB.csv")
print(df2)
```

输出：

Unnamed: 0  Column A  Column B
0  0  0.693188  -1.133000
1  1  0.222813  1.257744
2  2  -0.322323  -0.426994

例 7-4 和例 7-5 分别采用 read_table()和 read_csv()对 csv 文件进行读取, 均顺利获取了数据并打印出来。需要注意的是对于分隔符的指定。下面构建一个简单的案例完成数据的写入, 见例 7-6。

# 【例 7-6】Dataframe 保存为 csv 案例

输入：

```
import pandas as pd
from numpy.random.mtrand import randn

df = pd.DataFrame(randn(3, 2), columns=[' Column A ', ' Column B '], index = range(3))
print(df)
df.to_csv("AB.csv")
```

输出：

Column A  Column B
0  0.693188  -1.133000
1  0.222813  1.257744
7  -0.322323  -0.426994

# 7.2 数据分析案例

# 7.2.1 普查数据统计分析案例

本节数据来源于 UCI 机器学习库。UCI 数据库是一个常用的科学计算数据集库。adult.data 从美国 1994 年人口普查数据库中抽取而来, 因此也被称为人口普查收入数据集, 共包含 48842