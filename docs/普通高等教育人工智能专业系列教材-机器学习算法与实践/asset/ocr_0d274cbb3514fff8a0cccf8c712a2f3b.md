```
for c in classes:
    mean[c] = np.mean(X_train[y_train == c], axis=0)
    variance[c] = np.var(X_train[y_train == c], axis=0)

# 预测测试样本的类别
predictions = []
for x in X_test:
    posteriors = []
    for c in classes:
        # 使用高斯分布的概率密度函数计算后验概率的对数似然
        log_likelihood = np.sum(-0.5 * np.log(2 * np.pi * variance[c])
            - 0.5 * ((x - mean[c]) ** 2) / variance[c])
        prior = np.log(class_prior[c])
        posterior = prior + log_likelihood
        posteriors.append(posterior)

    # 选择具有最大后验概率的类别作为预测结果
    predictions.append(classes[np.argmax(posteriors)])

return np.array(predictions)
```

2）根据给定的训练集使用高斯朴素贝叶斯算法对测试集样本的类别进行预测。

这里我们首先定义了训练集 X_train 和对应的标签 y_train，以及测试集 X_test。然后，我们调用 gaussian_naive_bayes 函数对测试集进行预测，并将结果存储在 predictions 变量中。最后，我们使用 print 函数打印出预测结果。在这个示例中，预测结果是[0,1,0]，表示测试数据集中的第一个样本和第三个样本被分类为类别 0，第二个样本被分类为类别 1。

# 代码 4-4 使用高斯朴素贝叶斯算法对测试集样本的类别进行预测

```
# 构造一些示例数据进行测试
X_train = np.array([[0.5, 1.2], [1.0, 1.8], [3.2, 4.5], [2.8, 3.9], [1.2, 1.0], [2.5, 3.7]])
y_train = np.array([0, 0, 1, 1, 0, 1])
X_test = np.array([[1.5, 2.0], [2.0, 3.5], [0.8, 0.6]])

# 调用高斯朴素贝叶斯算法进行预测
predictions = gaussian_naive_bayes(X_train, y_train, X_test)

# 打印预测结果
print(predictions)
```

# 4.2.2 多项式朴素贝叶斯算法

多项式朴素贝叶斯（multinomial naive bayes）算法是一种经典的朴素贝叶斯分类算法，与高斯朴素贝叶斯算法和伯努利朴素贝叶斯算法相比，多项式朴素贝叶斯算法引入了一个多项式模型，对应于离散特征的计数。

多项式朴素贝叶斯算法假设每个特征的出现次数是由多项分布生成的，即特征的计数符合多项分布。因此，该算法广泛应用于文本分类问题，其中特征是词汇表中的不同单词，并且特征的计数表示单词在文档中出现的次数。