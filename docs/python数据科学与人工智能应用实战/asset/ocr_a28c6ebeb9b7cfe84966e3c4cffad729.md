![](bbox=[0.3, 0.06, 0.76, 0.3])

图 16-12

# 2. 交叉验证的 K 值优化

在了解 K-fold 交叉验证后，可以调用 K-fold 交叉验证的 cross_val_score() 函数来找出最佳 K 值（Python 程序：Ch16_2_3.py），如下所示：

```
import pandas as pd
import numpy as np
from sklearn import datasets
from sklearn import neighbors
from sklearn.model_selection import cross_val_score
import matplotlib.pyplot as plt

iris = datasets.load_iris()

X = pd.DataFrame(iris.data, columns=iris.feature_names)
X.columns = ["sepal_length", "sepal_width", "petal_length", "petal_width"]
target = pd.DataFrame(iris.target, columns=["target"])
y = target["target"]

Ks = np.arange(1, round(0.2*len(X) + 1))
accuracies=[]
for k in Ks:
    knn = neighbors.KNeighborsClassifier(n_neighbors=k)
    scores = cross_val_score(knn, X, y, scoring="accuracy",
        cv=10)
    accuracies.append(scores.mean())
```