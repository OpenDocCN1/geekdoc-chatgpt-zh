```
plt.scatter(X["petal_length"], X["petal_width"],
color=colmap[pred_y])
plt.xlabel("Petal Length")
plt.ylabel("Petal Width")
plt.title("K-means Classification")
plt.show()
```

上述程序代码和 Ch16_3_2.py 的最后只差第 2 个子图的 color 属性是使用 pred_y，而不是 kmeans.labels_，从其运行结果中可以看出色彩分类十分相似，如图 16-19 所示。

![](bbox=[0.15, 0.27, 0.8, 0.55])

# 3. K-means 模型的绩效测量

在完成分群标签的修正后，就可以计算 K-means 模型的绩效。使用 Scikit-learn 包的 metrics 对象运行模型绩效测量，如下所示：

```
import sklearn.metrics as sm
```

上述程序代码导入 metrics 对象后，可以使用准确度（Accuracy）和混淆矩阵（Confusion Matrix）来进行模型的绩效测量。首先计算模型的准确度（Python 程序：Ch16_3_2b.py），如下所示：

...

```
kmeans = cluster.KMeans(n_clusters=k, random_state=12)
kmeans.fit(X)
# 修正标签错误
pred_y = np.choose(kmeans.labels_, [1, 0, 2]).astype(np.int64)
# 绩效矩阵
print(sm.accuracy_score(y, pred_y))
```