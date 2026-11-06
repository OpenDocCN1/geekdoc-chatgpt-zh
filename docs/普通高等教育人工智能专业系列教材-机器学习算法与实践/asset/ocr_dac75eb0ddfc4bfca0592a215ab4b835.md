回损失值的列表、模型参数和梯度值。接着，使用 plt. plot 函数可视化训练过程中的损失曲线。最后，调用 predict 函数对训练好的模型进行预测，得到预测结果 y_pred_results。通过计算准确率，判断预测结果的准确性，并将准确率打印出来。

# 代码 5-12 对模型进行训练和测试

```
```python
from sklearn.datasets import make_classification
import matplotlib.pyplot as plt

def generate_data():
    # 使用 sklearn 生成测试数据
    X, y = make_classification(n_samples=100, n_features=2, n_redundant=0, n_informative=2, random_state=1, n_clusters_per_class=1)
    return X, y

def visualize_data(X, y):
    # 可视化数据集
    plt.scatter(X[:, 0], X[:, 1], c=y, cmap='viridis')
    plt.xlabel('Feature 1')
    plt.ylabel('Feature 2')
    plt.title('Test Data')
    plt.show()

def test_logistic_regression():
    # 生成测试数据
    X, y = generate_data()

    # 可视化原始数据
    visualize_data(X, y)

    # 数据预处理
    X = X.T
    y = y.reshape(1, -1)

    # 模型训练
    loss_list, params, grads = logistic_train(X, y, learning_rate=0.01, epochs=1000)

    # 可视化损失曲线
    plt.plot(loss_list)
    plt.xlabel('Epochs ( per hundreds )')
    plt.ylabel('Loss')
    plt.title('Training Loss')
    plt.show()

    # 进行预测
    y_pred_results = predict(X, params)

    # 计算准确度
    accuracy = np.mean((y_pred_results > 0.5) == y)
```
```