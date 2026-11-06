最小二乘法的原理是：当预测值和实际值距离的平方和最小时，就选定模型中的两个参数（β₀,β₁）。

# 9.2.2 Python 求解回归模型案例

本节将通过 Python 完成计算，数据集见表 9-2，文件名为 data.xlsx。

表 9-2 回归分析数据集

| 工作年限 | 年收入 |
|---|---|
| 2 | 20 |
| 3 | 25 |
| 4 | 30 |
| 5 | 34 |
| 5 | 31 |
| 11 | 40 |

希望构建 ŷ = β̂₀ + β̂₁ x，其中 β̂₀ 是一个常数，β̂₁ 是回归系数。

# 【例 9-1】回归分析，程序运行结果如图 9-3 所示

输入：

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
#1.导入需要的包
import pandas as pd
from sklearn import linear_model
import matplotlib.pyplot as plt
#2.通过 pd.read_excel 获取数据，并分别存入变量值 X_parm, Y_parm
def get_data():
    data = pd.read_excel("data.xlsx")
    X_parm = []
    Y_parm = []
    for x ,y in zip(data['years'],data['income']):
        #存储在相应的 list 列表中
        X_parm.append([float(x)])
        Y_parm.append(float(y))
    return X_parm,Y_parmOutput
#3.构建线性回归并计算
def linear_model_main(X_ parm,Y_ parm,predict_value):
    # Create linear regression object
    regr = linear_model.LinearRegression()
    #train model
    regr.fit(X_parm,Y_parm)
    predict_outcome = regr.predict(predict_value)
    pred= {}
    pred['intercept'] = regr.intercept_
```