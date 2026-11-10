# BP 反向传播训练循环详解

```python
for epoch in range(epochs):  # 训练轮次遍历

    total_loss = 0

    for data in D:  # 对训练集逐条处理

        x = data[:-output_units]  # 取输入特征

        y = data[-output_units:]  # 取期望输出

        # 正向计算
        alpha = np.dot(x, v)  # 隐层净输入
        b = sigmoid(alpha - gamma)  # 隐层激活输出
        beta = np.dot(b, w)  # 输出层净输入
        y_pred = sigmoid(beta - theta)  # 网络预测值

        # 误差统计
        loss = 0.5 * np.sum((y_pred - y) ** 2)  # 均方误差
        total_loss += loss  # 累计损失

        # 误差回传
        rho = y_pred * (1 - y_pred) * (y_pred - y)  # 输出层误差项 公式12-15
        pi = b * (1 - b) * np.dot(rho, w.T)  # 隐层误差项 公式12-17

        # 参数修正
        v -= learning_rate * np.outer(np.array(x), pi)  # 修正输入到隐层的权重
        w -= learning_rate * np.outer(b, rho)  # 修正隐层到输出的权重
        theta += learning_rate * rho  # 修正输出层阈值
        gamma += learning_rate * pi  # 修正隐层阈值

    avg_loss = total_loss / len(D)  # 轮均损失
    print(f"Epoch {epoch+1}/{epochs}, Loss: {avg_loss:.4f}")

return v, w, theta, gamma  # 返回训练后的权重与阈值
```

[PRE0]
# 样本数据与超参数

D = np.array([

[0, 0, 0],

[0, 1, 1],

[1, 0, 1],

[1, 1, 0]

])

# 超参数初始化

hidden_units = 2      # 隐层节点数
learning_rate = 0.1   # 步长
epochs = 2000         # 总迭代轮数

# 启动标准 BP 训练
v, w, theta, gamma = standard_bp_algorithm(D, hidden_units, learning_rate, epochs)

# 展示训练结果
print("v:")
print(v)
print("w:")
print(w)
print("theta:")
print(theta)
print("gamma:")
print(gamma)