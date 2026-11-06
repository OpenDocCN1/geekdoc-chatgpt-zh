```
num_features = X_train.shape[1]

# 创建特征存在计数数组和类别计数数组
feature_presence_counts = np.zeros((num_classes, num_features))
class_counts = np.zeros(num_classes)

# 遍历训练集, 统计特征存在计数和类别计数
for i in range(len(X_train)):
    sample = X_train[i]
    label = y_train[i]
    class_counts[label] += 1
    feature_presence_counts[label] += (sample > 0)

# 计算类别的先验概率
class_probabilities = class_counts / len(X_train)

# 计算特征存在的条件概率
# 使用拉普拉斯平滑: 将特征存在计数加 1, 类别计数加 2
feature_probabilities = (feature_presence_counts + 1) /
                        (class_counts.reshape(-1, 1) + 2)

predictions = []

# 对测试集中的每个样本进行预测
for sample in X_test:
    # 计算属于各个类别的后验概率
    log_probs = np.log(class_probabilities) + np.log(feature_probabilities
                                                     * sample + (1 - feature_probabilities) * (1 - sample))
    # 选择具有最大后验概率的类别作为预测结果
    predicted_class = np.argmax(np.sum(log_probs, axis=1))
    predictions.append(predicted_class)

# 返回预测结果
return predictions
```

2) 根据给定的训练集使用伯努利朴素贝叶斯算法对测试集样本的类别进行预测。
这里我们首先定义了训练集 X_train 和对应的标签 y_train，以及测试集 X_test。然后，我们调用 bernoulli_naive_bayes 函数对测试集进行预测，并将结果存储在 predictions 变量中。最后，我们使用 print 函数打印出预测结果。在这个示例中，预测结果是[0,1]，表示测试集中的第一个样本被分类为类别 0，第二个样本被分类为类别 1。

# 代码4-8 使用伯努利朴素贝叶斯算法对测试集样本的类别进行预测

```
X_train = np.array([[1, 1, 0, 0],
                    [0, 1, 1, 0],
                    [0, 0, 1, 1],
                    [1, 0, 1, 0]])

y_train = np.array([0, 0, 1, 1])
```