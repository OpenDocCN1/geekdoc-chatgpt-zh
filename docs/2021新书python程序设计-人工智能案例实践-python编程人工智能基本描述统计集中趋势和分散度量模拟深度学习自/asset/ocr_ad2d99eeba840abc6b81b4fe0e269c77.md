![](bbox=[0.24, 0.09, 0.75, 0.4])

# 14.7.6 选择最佳聚类估计器

正如在分类和回归案例研究中所做的那样，下面来运行多个聚类算法，看看它们如何很好地聚类三种鸢尾花。在这里，我们将尝试使用之前创建的KMeans对象和scikit-learn的DBSCAN、MeanShift、SpectralClustering、AgglomerativeCluster估计器创建的对象来为鸢尾花数据集聚类。与KMeans一样，可以预先为SpectralClustering和AgglomerativeClustering估计器指定集群的数量：

```
In [39]: from sklearn.cluster import DBSCAN, MeanShift,\n    ...:     SpectralClustering, AgglomerativeClustering\n\nIn [40]: estimators = {\n    ...:     'KMeans': kmeans,\n    ...:     'DBSCAN': DBSCAN(),\n    ...:     'MeanShift': MeanShift(),\n    ...:     'SpectralClustering': SpectralClustering(n_clusters=3),\n    ...:     'AgglomerativeClustering':\n    ...:         AgglomerativeClustering(n_clusters=3)\n    ...: }
```

下列循环的每次迭代都会调用一种估计器的fit方法， iris.data 作为其中的参数，然后使用NumPy的unique函数来获取三组样本的集群标签和数量并显示结果。我们没有预先指定DBSCAN估计器和MeanShift 估计器的集群数量。有趣的是， DBSCAN估计器正确地预测了三个集群 （标记为-1、0和1），尽管它在同一集群中放置了100个维吉尼亚鸢尾 和变色鸢尾中的84个，但是MeanShift估计器只预测了两个集群 （标记