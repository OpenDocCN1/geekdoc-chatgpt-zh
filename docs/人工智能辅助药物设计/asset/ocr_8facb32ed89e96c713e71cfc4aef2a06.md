```
converters={1:gender, 2:BP, 3:Cholesterol,
5:drug_type})
23.     x, y = np.split(data, (5,), axis=1)
24.     print(x,y)
```

我们会将整个数据集划分为训练集和测试集。这样做是为了测试算法的准确性。当然，也可以不对数据集进行划分——利用整个数据构建决策树。

# 清单4-2 划分数据集

```
1.     from sklearn.model_selection import train_test_split
2.     # 拆分训练数据与测试数据，以便进行交叉验证
3.     x_train, x_test, y_train, y_test = train_test_split(x,
 y, test_size=0.3)
```

接下来，可以对算法加以训练、测试，然后用其实施预测操作（见清单4-3～清单4-5）。

# 清单4-3 训练算法

```
1.     # 3、使用信息熵作为划分标准，对决策树进行训练
2.     clf = tree.DecisionTreeClassifier(criterion='entropy')
3.     print(clf)
4.     clf.fit(x_train, y_train)
```

# 清单4-4 测试算法

```
1.     # 4、使用训练数据预测，验证结果
2.     y_pred = clf.predict(x_train)
3.     y_train = y_train.reshape(-1)
4.     print(y_pred)
5.     print(y_train)
6.     print(np.mean(y_pred == y_train))
```

# 清单4-5 使用算法进行预测

```
1.     # 5、对测试数据进行预测
2.     y_pred = clf.predict(x_test)
3.     y_test = y_test.reshape(-1)
4.     print(" accuracy: ")
5.     print(metrics.accuracy_score(y_test, y_pred))
6.     print(y_test)
7.     print(np.mean(y_pred == y_test))
```

计算出的特征重要性如下所示:

```
feature_importances:
('Age', 0.643312969322477)
('Gender', 0.0)
('BP', 0.0)
('Cholesterol', 0.0)
('Na_to_K', 0.35668703067752305)
```