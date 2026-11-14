# 键-值对：

```
In [45]: estimators = {
    ....:     'KneighborsClassifier': knn,
    ....:     'SVC': SVC(gamma='scale'),
    ....:     'GaussianNB': GaussianNB()}
    ....: 
```

# 现在，运行下面的模型：

```
In [46]: for estimator_name, estimator_object in estimators.items():
    ....:     kfold = KFold(n_splits=10, random_state=1, shuffle=True)
    ....:     scores = cross_val_score(estimator=estimator_object,
    ....:                          X=digits.data, y=digits.target, cv=kfold)
    ....:     print(f'{estimator_name:>20}: ' +
    ....:           f'mean accuracy={scores.mean():.2%})' +
    ....:           f'standard deviation={scores.std():.2%}')
    KNeighborsClassifier: mean accuracy=98.72%; standard deviation=0.75%
    SVC: mean accuracy=99.00%; standard deviation=0.85%
    GaussianNB: mean accuracy=84.48%; standard deviation=3.47%
```

此循环遍历了估计器字典中的元素，并且为每个键-值对执行以下任务：

+   - 将键解压缩到estimator_name，并将值解压缩到estimator_object。
+   - 创建一个KFold对象，该对象可以对数据进行混洗并将其分成10折。关键字参数random_state在这里特别重要，因为它确保每个估计器使用相同的训练集和测试集，使得每种算法有同样的对比基础。
+   - 使用cross_val_score评估当前的estimator_object。
+   - 打印估计器的名称，然后算出10折交叉验证的准确性得分的平均值和标准差。

根据结果可以看出，svc估计器获得了稍好的准确性—至少在使用估计器的默认设置情况下是这样的。通过调整估计器的某些设置，可能还可以获得更好的结果。KNeighborsClassier估计器和svc估计器的准确性几乎相同，因此我们可以对每个估计器进行进一步的超参数调整以确定哪个是最佳的。

# scikit-learn估计器图表