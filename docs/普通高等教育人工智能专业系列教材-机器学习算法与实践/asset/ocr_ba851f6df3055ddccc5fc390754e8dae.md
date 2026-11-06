```
# 计算并打印损失值
loss = compute_loss(X, y, weights, bias)
print("Iteration:", i+1, "Loss:", loss)

return weights, bias
```

4）定义对测试数据的预测函数。

代码 5-16 实现了 predict 函数，用于预测输入样本的类别标签。注释说明了参数和返回值的含义。它计算输入特征 X 的得分并使用 softmax 函数将得分转换为类别概率。然后，它找到概率最大的类别索引作为预测的标签，并返回预测的标签列表。

# 代码 5-16 predict() 函数

```
def predict(X, weights, bias):
    """
    预测输入样本的类别标签
    参数：
    X: 输入特征
    weights: 权重矩阵
    bias: 偏置向量
    返回：
    predicted_labels: 预测的类别标签
    """
    # 计算预测得分
    scores = np.dot(X, weights) + bias

    # 计算相应的概率
    probabilities = softmax(scores)

    # 找到概率最大的类别标签作为预测结果
    predicted_labels = np.argmax(probabilities, axis=1)

    return predicted_labels
```

5）生成模拟的三分类数据集进行模型训练和测试。

代码 5-17 首先使用 np.random.rand 生成了一个随机的特征矩阵 X 和标签数组 y。然后使用 StandardScaler 对特征进行归一化处理。接着使用 train_test_split 将数据划分为训练集和测试集。然后，我们创建了一个 Softmax_train 对象，并设置了特征数和类别数。接着调用方法对模型进行训练，使用学习率为 0.01，迭代次数为 1000。最后，调用 predict 方法在测试集上进行预测，计算准确率并输出。你可以根据需要调整数据生成的方式、数据预处理的方法以及模型的超参数来进行实验和测试。

# 代码 5-17 对模型进行训练和测试

```
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

# 生成模拟数据
```