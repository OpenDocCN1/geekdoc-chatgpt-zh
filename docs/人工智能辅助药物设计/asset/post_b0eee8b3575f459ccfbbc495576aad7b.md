
## 3.6.4 序贯最小优化算法

分解算法的基本思路就是在每一步迭代中把训练样本集分解为两个子集合 B 和 N，只对 B 中的样本进行迭代，而另一个集合 N 中样本所对应的拉格朗日乘子保持不变，然后将集合 N 中的一部分“情况最糟糕的样本点”与工作集 B 中的一部分样本点进行交换。序贯最小优化（Sequential Minimal Optimization, SMO）算法将分解算法的思路推广到极致，将工作集大小限定为 2，即每次迭代仅优化两个样本点的最小子集，进行多次优化，直到所有拉格朗日乘子都满足 KKT 条件，使目标函数达到最小。这时工作集的规模已经减至最小，因为原始问题的对偶问题有约束条件，只要变动一个乘子，就至少必须同时调整另一个乘子来保证不违反该约束。序贯最小优化算法的执行步骤如下：

- (1) 给定精度 $\mathcal{E}$，令 k=0，取初始值 $\alpha^0 = (\alpha_1^0, \dots, \alpha_l^0)^T = 0$；

- (2) 根据当前的近似解 $\alpha^k$ 选取集合 $\{1, 2, \dots, l\}$ 的一个由两个原始组成的子集 $\{i, j\}$ 作为工作集 B；

- (3) 求解与工作集 B 对应的最优化问题（式（3-41）），得解 $\alpha_B^* = (\alpha_i^{k+1}, \alpha_j^{k+1})^T$，据此更新 $\alpha^k$ 中的第 $i$ 个分量和第 $j$ 个分量，得到新的可行的近似解 $\alpha^{k+1}$；

- (4) 若 $\alpha^{k+1}$ 在精度 $\mathcal{E}$ 范围内满足 KKT 条件，即
  
  $$
  \sum_{i=1}^l y_i \alpha_i = 0, \quad 0 \leqslant \alpha_i \leqslant C
  $$
  
  $$
  y_j \left| \sum_{i=1}^l y_i \alpha_i K(x_i, x_j) + b \right| \begin{cases} > 1, & \alpha_j = 0 \\ = 1, & 0 < \alpha_j < C \\ < 1, & \alpha_j = C \end{cases}
  $$
  
  $i, j = 1, \dots, l$
  
  (3-41)
  
  则得到近似解 $\alpha^* = \alpha^{k+1}$，停止计算；否则，令 $k = k + 1$，转到步骤（2）；

- (5) 得到最优分类规则函数。

序贯最小优化算法存在两个具体问题：一是两个变量的最优化问题的解析求解；二是如何选取工作集 B 中的两个训练样本。求解两个变量的最优化问题的解析，要假设每次迭代过程中只调整相应两个样本点 $\alpha_1$ 和 $\alpha_2$，这两个乘子定义在 (0, C) 的二维正方形空间中，同时又在 $\alpha_1 + s\alpha_2 = \gamma$ 所确定的对角线上，其中 $s = y_1 y_2$，用于求解一个具有两个变量的最优化问题。

## 3.7 示例

为了帮助读者深入理解支持向量机的理论知识，我们给出基于支持向量机对激酶抑制剂的分类模型并提供相关源码。

激酶抑制剂是一种抑制激酶活性的化合物。蛋白激酶用于调节细胞的多个功能，自2001年以来已有76种激酶抑制剂获批上市，例如伊马替尼（Imatinib）已被成功应用于癌症治疗。这里使用的数据集为包含2013个激酶抑制剂的四分类数据，每个数据包含5个特征，为便于展示，我们进行名词替换，如表3-2所示，数据集展示如图3-6所示。

数据集在四分类上的分布情况如表3-3所示，标签分布存在一定的数据分布不均衡问题。针对数据分布不均衡有许多解决方法，为了简化实战代码，这里没有使用这些方法。

提示

解决数据分布不均衡的方法可以分为简单方法，以及数据层面和算法层面的方法。简单方法有收集数据、将分类任务转换成异常检测、调整权重和阈值等；数据层面的方法有欠采样、过采样和欠采样+过采样等；算法层面的方法有代价敏感和集成学习等。

### 表3-2 特征情况

| 特征名 | 替换名 |
| :--- | :--- |
| r_desc_Average_connectivity_index_chi-2 | feature0 |
| r_desc_Maximal_electrotopological_negative_variation | feature1 |
| r_desc_Second_Mohar | feature2 |
| r_desc_Second_Zagreb_index_by_valence_vertex_degrees | feature3 |
| r_qp_ACxDN^.5/SA | feature4 |

|    | feature0   | feature1   | feature2   | feature3   | feature4   |
|---|---|---|---|---|---|
| 0  | 0.293245   | 1.902150   | 7.867279   | 611.000000 | 0.021358   |
| 1  | 0.300698   | 6.693446   | 5.678195   | 347.777778 | 0.058341   |
| 2  | 0.274048   | 0.987404   | 4.915686   | 401.000000 | 0.009612   |
| 3  | 0.280079   | 1.899112   | 7.295805   | 650.000000 | 0.019622   |
| 4  | 0.287003   | 2.405486   | 3.201728   | 481.000000 | 0.022678   |
| ...| ...        | ...        | ...        | ...        | ...        |
| 2008| 0.283208   | 5.174158   | 3.261525   | 356.000000 | 0.024340   |
| 2009| 0.279821   | 0.861883   | 3.539995   | 347.037037 | 0.008887   |
| 2010| 0.270120   | 2.089367   | 4.400193   | 509.000000 | 0.012749   |
| 2011| 0.269581   | 1.893247   | 6.029059   | 673.000000 | 0.019620   |
| 2012| 0.284379   | 1.861145   | 4.676438   | 314.000000 | 0.008033   |

2013 rows × 5 columns

### 图3-6 数据集展示

### 表3-3 标签分布情况

| 标签 | 数量 |
|---|---|
| I | 1399 |
| II/2 | 377 |
| II | 190 |
| A | 47 |

具体操作步骤如下。

(1) 导入相关的库和创建支持向量机实例，如清单3-1所示。

### 清单3-1 导入库和创建支持向量机实例

```
1. from sklearn.ensemble import AdaBoostRegressor,
   RandomForestRegressor,
   RandomForestClassifier
2. from sklearn.datasets import load_boston
3. from numpy import array
4. from pandas import read_csv
5. from sklearn.model_selection import train_test_split
6.  import pandas as pd
7.  import numpy as np
8.  from sklearn.preprocessing import OneHotEncoder, StandardScaler
9.  from sklearn.svm import SVC
10. #创建支持向量机实例
11. estimator = SVC()
```

(2) 在清单3-2所示的实际代码中，我们使用sklearn库直接调用各种机器学习模型并实例化，例如随机森林、决策树等。

### 清单3-2 实际代码

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
8.  from sklearn.preprocessing import OneHotEncoder, StandardScaler
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
30. # 'r_desc_Second_Mohar'
31. # 'r_desc_Second_Zagreb_index_by_valence_vertex_degrees'
32. #'r_qp_ACxDN^.5/SA']
33. #length of features 5
34. #读取label文件
35. label = pd.read_csv('label_kinase_selected.csv',
    sep=',', )
36.
37. from sklearn import preprocessing
38. from sklearn.preprocessing import OneHotEncoder, StandardScaler
39.
40. le = preprocessing.LabelEncoder()
41.
42. y=label.iloc[:,-1]
43. print(y.shape)
44. #输出为
45. #(2013,)
46. # label encoder
47. Y=le.fit_transform(y)
48. print(Y)
49. #输出为
50. #[3 2 1 ... 2 2 1]
51. print(label['Binding_Modes'].value_counts())
52. #输出为
53. #I      1399
54. #II/2   377
55. #II     190
56. #A      47
57. # split into input (X) and output (Y) variables
58. from sklearn.model_selection import train_test_split
59.
60. X_train, X_test, Y_train, Y_test =
    train_test_split(X_scaled, Y, test_size
    =0.2, random_state=42,
    stratify=Y)
61.
62. print("Feature data dimension: ", X_train.shape)
63. print("Num of classes: ", Y_train.shape)
64. #输出为
65. #Name: Binding_Modes, dtype: int64
66. #Feature data dimension: (1610, 5)
67. #Num of classes: (1610,)
68.
69. from sklearn.svm import SVC
70. estimator = SVC()
71.
72. estimator.fit(X_train, Y_train)
73. print("train score: ", estimator.score(X_train,
    Y_train))
74. print("test score: ", estimator.score(X_test,
    Y_test))
75. #输出为
76. #train score: 0.7521739130434782
77. #test score: 0.7344913151364765
```

（3）结果分析。通过训练之后，得到表 3-4 所示的最终得分。这里使用的评估函数为 accuracy_score，其计算公式如式（3-42）所示。通过比较训练集和测试集的结果发现，得分差距很小，说明支持向量机算法可以有效地进行分类任务，且不存在过拟合和欠拟合的情况。最终结果是：在20%样本的测试集中，有73.449%的样本被正确分类。

```
accuracy_score=\frac{TP}{ALL}
```

(3-42)

其中，TP代表分类正确的样本个数，ALL代表所有的样本数。

### 表3-4 得分情况

| 评估函数 | train | test |
|---|---|---|
| accuracy_score | 0.75217 | 0.73449 |

## 3.8 参考资料

- [1] 杨志民, 刘广利.不确定性支持向量机: 算法及应用[M].北京: 科学出版社, 2012.
- [2] 赛英, 张凤延, 张涛.基于支持向量机的中国股指期货回归预测研究[J].中国管理科学, 2013, 21(3): 35-39.
- [3] 危傲. 基于SVM算法的分类器设计[J]. 电子科技, 2015, 28(4): 23.
- [4] 张铃. 基于核函数的SVM机与三层前向神经网络的关系[J]. 计算机学报, 2002, 25(7): 5.
- [5] 李凯, 卢霄震. 一种基于粗糙间隔的模糊支持向量机[J]. 电子学报, 2013, 41(006): 1183-1187.
- [6] 梁礼明, 钟震, 陈召阳. 支持向量机核函数选择研究与仿真[J]. 计算机工程与科学, 2015, 37(6): 1135-1141.
- [7] 奉国和. SVM分类核函数及参数选择比较[J]. 计算机工程与应用, 2011, 47(3): 3.
- [8] BOSER B E, GUYON I M, VAPNIK V N. A Training Algorithm for Optimal Margin Classifiers[C]//Proceedings of The Fifth Annual Workshop on Computational Learning Theory. New York: ACM Press, 1992: 144-152.
- [9] OSUNA E, FRENUD R, GIROSI F. An improved Training Algorithm for Support Vector Machines[C]//Proceedings of IEEE Workshop on Neural Networks for Signal Processing. New York, USA:[s.n.], 1997: 276-285.
- [10] WANG X H, LIU A, ZHANG S Q. New facial expression recognition based on FSVM and KNN [J]. Optik-International Journal for Light and Electron Optics, 2015, 126(21): 3132-3134.
- [11] 邬啸, 魏延, 吴瑕. 改进的双隶属度模糊支持向量机[J]. 重庆师范大学学报: 自然科学版, 2011, (5): 194-196.
- [12] 刘三阳, 杜喆. 一种改进的模糊支持向量机算法[J]. 智能系统学报, 2007, 2(3):30-33.
- [13] 刘开旻, 吴小俊. 一种基于新隶属度函数的模糊支持向量机[J]. 计算机工程, 2016, 42(4): 155-159.
- [14] 汪海燕, 黎建辉, 杨凤雷. 支持向量机理论及算法研究综述[J]. 计算机应用研究, 2014, 31(5): 1281-1286.

## 第4章 决策树

决策树的基本原理类似于人类在面临抉择时的处理机制，在机器学习和深度学习中都有其应用场景。其中，得到广泛使用的随机森林就是以决策树为基础构建的一种集成学习算法。本章将先以二分类任务为例，阐释决策树的基本原理，然后介绍与决策树划分选择相关的几个核心概念，并在最后给出代码示例，以期帮助读者更好地了解这一知识点。
