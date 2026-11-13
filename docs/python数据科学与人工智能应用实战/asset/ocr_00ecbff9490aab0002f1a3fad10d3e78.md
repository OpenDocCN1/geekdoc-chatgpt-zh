的平均值是 0，标准差是 1。实际上，如果机器学习算法是根据数据分配，就可以使用数据标准化。

实际上，可以自行使用第 12.2.2 节的公式进行数据标准化，另一种方式是使用第 15 章 Scikit-learn 包的 preprocessing 模块进行数据标准化。例如，标准化某社交软件朋友的追踪数和快乐程度的调查数据（Python 程序：Ch13_2_1.py），如下所示：

```
import pandas as pd
from sklearn import preprocessing
```

```
f_tracking = [110, 1018, 1130, 417, 626,
957, 90, 951, 946, 797,
981, 125, 456, 731, 1640,
486, 1309, 472, 1133, 1773,
906, 532, 742, 621, 855]
```

```
happiness = [0.3, 0.8, 0.5, 0.4, 0.6,
0.4, 0.7, 0.5, 0.4, 0.3,
0.3, 0.6, 0.2, 0.8, 1,
0.6, 0.2, 0.7, 0.5, 0.7,
0.1, 0.4, 0.3, 0.6, 0.3]
```

上述程序代码导入 Scikit-learn 包的 preprocessing 模块后，建立数据的 2 个 Python 列表。然后建立 DataFrame 对象，如下所示：

```
df = pd.DataFrame({"f_tracking" : f_tracking,
"happiness" : happiness})
```

```
print(df.head())
```

上述程序代码建立 DataFrame 对象 df 后，输出前 5 项数据，如图 13-6 所示。

从图 13-6 中可以看出，2 个变量的单位差异很大。所以，需要使用 Z 分数来进行数据标准化，如下所示：

```
df_scaled = pd.DataFrame(preprocessing.scale(df),
columns=["f_tracking_s", "happiness_s"])
```

```
print(df_scaled.head())
```

上述程序代码调用 preprocessing.scale() 函数进行数据标准化，并且建立新的 DataFrame 对象 df_scaled 后，输出前 5 项，如图 13-7 所示。