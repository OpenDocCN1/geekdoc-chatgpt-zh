```
min_samples_split=6, min_samples_leaf=5,
max_features='sqrt',
oob_score=True)
2.  history = estimators.fit(X_train, Y_train )
3.  print('training accuracy:', history.oob_score_)
```

在清单15-9中，使用训练好的模型对测试集进行预测，prediction以一维数组的形式保存着测试集经过模型预测后的结果。绘制模拟坐标分别为预测值和真实值的散点图，我们能直观地观察到模型对测试集数据的整体预测效果，如图15-6所示。散点离直线 y=x 越近，表明预测越准确。

# 清单15-9 使用模型对测试集进行预测

```
1.  prediction = estimators.predict(X_test)
2.  plt.scatter (prediction,Y_test, color='red',s=2)
3.  x = np.linspace(-2, 5, 10000)
4.  plt.plot(x,x,color=" blue ")
5.  plt.xlabel( " prediction " )
6.  plt.ylabel( " Y_test " )
7.  plt.show()
```

![](bbox=[0.12, 0.39, 0.88, 0.75])

图15-6 模型对测试集整体预测效果的展示