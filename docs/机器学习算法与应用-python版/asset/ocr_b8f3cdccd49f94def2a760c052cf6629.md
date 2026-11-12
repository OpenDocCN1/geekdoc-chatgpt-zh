# 步骤 2 使用 SVM 分类器实现鸢尾花分类

使用 SVM 分类器进行分类，具体实现代码如下。

[ 代码 10-2 ]

```
from sklearn import datasets
iris = datasets.load_iris()
X = iris.data
y = iris.target
# 数据集划分
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3)
# 建立 SVM 分类模型，训练模型
from sklearn.svm import SVC
from sklearn.ensemble import AdaBoostClassifier
svc=SVC(probability=True, kernel='linear')
Boosting=AdaBoostClassifier(n_estimators=50, base_estimator=svc, learning_rate=1)
model = Boosting.fit(X_train, y_train)
Y_pred = model.predict(X_test)
# 输出分类结果
from sklearn import metrics
print("准确率为：", metrics.accuracy_score(y_test, y_pred))
print(metrics.classification_report(y_test, y_pred))
print(metrics.confusion_matrix(y_test, y_pred))
```

输出结果如下。

```
准确率为：0.97777777777778
         precision    recall  f1-score   support
0       1.00      1.00      1.00        16
1       0.93      1.00      0.96        13
2       1.00      0.94      0.97        16
accuracy                           0.98        45
macro avg      0.98      0.98      0.98        45
weighted avg   0.98      0.98      0.98        45
[[16  0  0]
 [ 0 13  0]
 [ 0  1 15]]
```

由如上输出结果可以看出，SVC 分类器的准确率为 0.98。显然，选取不同的弱分类器，会对输出结果造成一定的影响，并且 SVC 分类器比决策树分类器的准确率更高。