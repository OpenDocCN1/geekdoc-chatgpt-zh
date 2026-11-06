机抽取两个数作为特征 x1 和 x2。然后，使用条件语句判断点(x1，x2)是否位于圆形区域内。如果满足条件 x1 ** 2 + x2 ** 2 < 10，则将特征点[x1，x2]添加到特征列表 X 中，并将标签 1 添加到标签列表 y 中；否则，将特征点[x1，x2]添加到特征列表 X 中，并将标签-1 添加到标签列表 y 中。最后，将特征列表 X 和标签列表 y 转化为特征矩阵 X 和标签向量 y，并将其作为函数的输出返回。示例中调用 generate_data 函数生成指定数量的训练集和测试集的数据。

# 代码 6-12 随机生成满足特定规律训练集、测试集

```
def generate_data(n_samples):
    """
    生成随机数据函数

    参数：
        n_samples (int): 样本数量

    返回：
        X (ndarray): 特征矩阵，形状为 (n_samples, 2)
        y (ndarray): 标签向量，形状为 (n_samples,)
    """
    np.random.seed(0)            # 设置随机种子，保证可重复性

    X = []                       # 存储特征的列表
    y = []                       # 存储标签的列表

    for i in range(n_samples):
        # 从均匀分布[-5, 5)中随机抽取两个数作为特征 x1 和 x2
        x1 = np.random.uniform(-5, 5)
        x2 = np.random.uniform(-5, 5)

        if x1 ** 2 + x2 ** 2 < 10:  # 判断点(x1, x2)是否位于圆形区域内
            X.append([x1, x2])     # 将特征点添加到特征列表 X 中
            y.append(1)            # 将标签 1 添加到标签列表 y 中
        else:
            X.append([x1, x2])     # 将特征点添加到特征列表 X 中
            y.append(-1)           # 将标签-1 添加到标签列表 y 中

    return np.array(X), np.array(y)  # 将特征列表 X 和标签列表 y 转化为特征矩阵 X 和标签向量 y, 然后返回

# 生成训练集数据
X_train, Y_train = generate_data(30)

# 生成测试集数据
X_test, Y_test = generate_data(10)
```