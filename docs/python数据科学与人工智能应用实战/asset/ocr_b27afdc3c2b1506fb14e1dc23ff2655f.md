表 15-4 中的解释变量有 2 个，即分店面积和距地铁站距离。建立线性复回归的预测模型，只需输入新店的面积和距地铁站的距离，就可以预测新店的月营业额（ Python 程序：Ch15_3_1a.py），如下所示：

```
import numpy as np
import pandas as pd
from sklearn.linear_model import LinearRegression

area_dists = np.array([[10, 80], [8, 0], [8, 200],
    [5, 200], [7, 300], [8, 230],
    [7, 40], [9, 0], [6, 330],
    [9, 180]])

sales = np.array([46.9, 36.6, 37.1, 20.8,
    24.6, 29.7, 36.6, 43.6,
    19.8, 36.4])

X = pd.DataFrame(area_dists, columns=["Area", "Distance"])
target = pd.DataFrame(sales, columns=["Sales"])
y = target["Sales"]
```

上述程序代码同样是建立 1 个二维和 1 个一维 NumPy 数组，然后建立 2 个 DataFrame 对象，X 对象有 2 列，y 是字段 Sales，然后就可以训练线性复回归的预测模型，如下所示：

```
lm = LinearRegression()
lm.fit(X, y)
print("回归系数：", lm.coef_ )
print("截距：", lm.intercept_ )

# 预测店面积和距地铁站距离[10, 100]的营业额
new_area_dists = pd.DataFrame(np.array([[10, 100]]))
predicted_sales = lm.predict(new_area_dists)
print(predicted_sales)
```

上述程序代码在成功建立预测模型后，使用新的面积和距地铁站距离数据 [10, 100]，即可调用 predict() 函数预测新店的月营业额。其运行结果如下所示：

```
回归系数： [ 4.12351586 -0.03452946]
截距： 6.84555233843927275
[44.62773616]
```