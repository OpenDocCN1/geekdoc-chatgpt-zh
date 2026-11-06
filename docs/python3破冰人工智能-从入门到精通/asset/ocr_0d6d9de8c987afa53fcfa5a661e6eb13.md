预计算的距离：取 False，不预计算的距离。

+   - **verbose**: 默认是 0，不输出日志信息，参数设定打印求解过程的程度，值越大，打印的细节越多。
+   - **random_state**: 随机生成簇的状态条件，一般默认即可（随机种子）。
+   - **copy_x**: 布尔值，标记是否修改数据，主要用于 precompute_distances= True 的情况。取 True：预计算距离时，并不修改原来的数据。取 False：预计算距离时，会修改原始数据用于节省内存，当算法完成后，会将原始数据返还。但是有可能因为浮点数的表示，在计算过程中由于有对数据均值的加减运算，所以数据返还后，原始数据和计算前可能会有细小差别。
+   - **n_jobs**: 指定计算所用的进程数。
+   - **algorithm**: 是 K-Means 的实现算法，有 auto、full 和 elkan 这 3 种状态。只要针对稀疏数据与稠密数据进行设置，通常设置 auto 即可。

# 小贴士：

针对 init 参数，K-Means 算法经过不断迭代，最终实现收敛目的，但是其收敛过程复杂程度依赖初始化的簇中心的选取，有时可能收敛到局部极小值。而 K-Means++选择策略就是针对算法的初始簇中心选择上这一缺点而改进的方法。

（1）基本属性说明。

+   - **cluster_centers_**: 每个簇中心的坐标 array, [n_clusters, n_features]。
+   - **labels_**: 给出每个样本所属的簇的标记。
+   - **inertia_**: 给出每个样本到它们各自所在的簇中心的距离之和。

（2）基本方法说明。

+   - **fit(X[, y])**: 训练 K-Means 聚类。
+   - **predict(x)**: 预测样本所属的簇。
+   - **fit_predict(X[, y])**: 训练模型并且预测每个样本对应的簇类别，相当于先调用 fit(X)再调用 predict(X)。
+   - **score(X[,y])**: 给出样本距离簇中心的偏移量的相反数。

# 【例 10-1】聚类案例，程序运行结果如图 10-5 所示

输入：

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
#导入所需要的模块
from sklearn.cluster import KMeans
import numpy as np
import matplotlib.pyplot as plt
#构建输入数据
X = np.array([[1, 2], [1, 4], [1, 0],
```