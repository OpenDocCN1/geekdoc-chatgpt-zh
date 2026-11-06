```
train = pd.read_csv('adult.data.txt', sep="\s", header=None, names = column_names, engine = 'python')
test = pd.read_csv('adult.test.txt', sep="\s", header=None, names = column_names, engine = 'python')
test['income'].replace(regex=True,inplace=True,to_replace=r'\.',value=r'')
adult = pd.concat([test,train])
adult.reset_index(inplace = True, drop = True)  #查看数据
print(adult.count())
```

输出：

|  |  |
|---|---|
| age | 48842 |
| workclass | 48842 |
| fnlwgt | 48842 |
| education | 48842 |
| educational-num | 48842 |
| marital-status | 48842 |
| occupation | 48842 |
| relationship | 48842 |
| race | 48842 |
| gender | 48842 |
| capital-gain | 48842 |
| capital-loss | 48842 |
| hours-per-week | 48842 |
| native-country | 48842 |
| income | 48842 |

数据维度较多，可选择自己想要观察的数据进行查看，这里选择'age','educational-num'两个字段进行查看，采用 tail()可以查看最后 5 行数据，具体形式如例 7-7.2 所示。

# 【例 7-7.2】观察数据

输入：

```
#选择'age','educational-num'两个字段进行查看
print(adult[['age','educational-num']].tail())
```

输出：

|  | age | educational-num |
|---|---|---|
| 48838 | 27 | 12.0 |
| 48839 | 40 | 9.0 |
| 48840 | 58 | 9.0 |
| 48841 | 22 | 9.0 |
| 48842 | 52 | 9.0 |

下面通过例 7-7.3 对数据进行一个整体的观察，针对已有的 column 绘制数据条形图，从整体上观察数据的分布形态，以便进行更加详细的分析。