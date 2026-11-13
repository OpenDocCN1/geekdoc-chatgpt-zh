```
6.  import pandas as pd
7.  import numpy as np
8.  from sklearn.preprocessing import OneHotEncoder,
StandardScaler
9.  from sklearn.svm import SVC
10. #创建支持向量机实例
11. estimator = SVC()
```

(2) 在清单3-2所示的实际代码中，我们使用sklearn库直接调用各种机器学习模型并实例化，例如随机森林、决策树等。

# 清单3-2 实际代码

```
1.  from sklearn.ensemble import AdaBoostRegressor,
RandomForestRegressor,
RandomForestClassifier
2.  from sklearn.datasets import load_boston
3.  from numpy import array
4.  from pandas import read_csv
5.  from sklearn.model_selection import train_test_split
6.  import pandas as pd
7.  import numpy as np
8.  from sklearn.preprocessing import OneHotEncoder,
StandardScaler
9.  #读取data文件
10. dataframe = read_csv( " kinase_selected.csv ", delimiter = ',')
11.
12. dataset = dataframe.values
13. X = dataset[:,1:]
14.
15. scaler = StandardScaler()
16. X_scaled = scaler.fit_transform(X)
17. print(X_scaled.shape)
18. #输出为
19. # (2013, 5)
20. #feature_names = dataframe_cat.columns.values[2:]
21. feature_names = dataframe.columns.values[1:]
22. features = array(feature_names)
23. print("All features: " )
24. print(features)
25. print( " length of features ", len(features))
26. #输出为
27. #All features:
28. #[ 'r_desc_Average_connectivity_index_chi-2'
29. # 'r_desc_Maximal_electrotopological_negative_variation'
30. # ' 'r_desc_Second_Mohar'
31. # 'r_desc_Second_Zagreb_index_by_valence_vertex_degrees'
```