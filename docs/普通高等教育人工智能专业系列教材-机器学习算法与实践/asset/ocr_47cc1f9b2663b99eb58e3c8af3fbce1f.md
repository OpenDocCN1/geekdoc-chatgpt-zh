```
return 1 / (1 + np.exp(-x))

def derivative_sigmoid(x: float) -> float;
    """
    Sigmoid 函数的导数实现。

    参数：
        x: 输入值。

    返回：
        float: Sigmoid 函数在输入值处的导数结果。
    """
    return sigmoid(x) * (1 - sigmoid(x))
```

代码 12-12 实现了标准的 BP 算法，用于训练神经网络模型。首先，根据输入数据集的形状确定输入和输出单元的数量，并初始化权重矩阵 v 和 w，以及阈值向量 theta 和 gamma。在每个迭代的 epoch 中，遍历数据集中每一个样本，将输入数据 x 和目标输出 y 提取出来。然后进行前向传播计算，通过矩阵乘法和激活函数 sigmoid 函数得到隐层输出 b 和输出层预测结果 y_pred。接下来计算误差，使用均方误差损失函数计算预测结果 y_pred 与目标输出 y 之间的误差。然后进行反向传播，计算输出层误差项 rho 和隐层误差项 pi，并根据权重矩阵进行传播。最后，根据学习率和误差项的乘积更新权重矩阵 v、w 和阈值向量 theta、gamma。每个 epoch 结束后，计算平均损失并输出结果。最终返回训练得到的参数 v、w、theta、gamma，可以用于进行预测任务。

# 代码 12-12 定义 BP 算法框架

```
def standard_bp_algorithm(D, hidden_units, learning_rate, epochs):
    """
    标准反向传播算法的实现。

    参数：
        D: 输入数据集，形状为（样本数，特征数）。
        hidden_units: 隐层单元数。
        learning_rate: 学习率。
        epochs: 迭代次数。

    返回：
        tuple: 训练得到的参数 v, w, theta, gamma。
    """
    np.random.seed(0)

    # 初始化参数
    input_units = D.shape[1] - 1        # 输入单元的数量
    output_units = D.shape[1] - input_units # 输出单元的数量
    v = np.random.randn(input_units, hidden_units)   # 输入层到隐层之间的权重矩阵
```