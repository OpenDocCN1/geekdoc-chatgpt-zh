```
b: 偏置项
"""
m = len(X)                                      # 样本数量
alpha = np.zeros(m)                             # 初始化拉格朗日乘子
b = 0                                           # 初始化偏置项
iter_count = 0                                  # 迭代计数器

while iter_count < max_iter:                    # 迭代停止条件
    alpha_changed = 0                           # 用于判断是否有乘子发生变化

    for i in range(m):                          # 遍历样本
        error_i = np.dot(alpha * y, kernel_function(X, X[i])) + b - y[i]  # 计算样本点的预测误差

        if np.any((y[i] * error_i < -tol) & (alpha[i] < C)) or np.any((y[i] * error_i > tol) & (alpha[i] > 0)):  # 判断是否需要优化乘子
            j = np.random.choice([x for x in range(m) if x != i])   # 随机选择第二个样本点
            error_j = np.dot(alpha * y, kernel_function(X, X[j])) + b - y[j]  # 计算第二个样本点的预测误差
            alpha_old_i = alpha[i]  # 保存旧的 alpha_i
            alpha_old_j = alpha[j]  # 保存旧的 alpha_j

            if y[i] != y[j]:        # 确定 alpha_j 的取值范围
                L = max(0, alpha[j] - alpha[i])
                H = min(C, C + alpha[j] - alpha[i])
            else:
                L = max(0, alpha[i] + alpha[j] - C)
                H = min(C, alpha[i] + alpha[j])

            if L == H:              # 如果取值范围相等则继续下一次循环
                continue

            eta = kernel_function(X[i], X[i]) + kernel_function(X[j], X[j]) - 2 * kernel_function(X[i], X[j])  # 计算核函数
            if eta <= 0:            # 如果 eta 小于等于 0 则继续下一次循环
                continue

            alpha[j] = alpha[j] + float(y[j] * (error_i - error_j) / eta)   # 更新 alpha_j
            alpha[j] = np.clip(alpha[j], L, H)      # 修剪 alpha_j

            if abs(alpha[j] - alpha_old_j) < 1e-5:  # 如果 alpha_j 变化很小则继续下一次循环
                continue

            alpha[i] += y[i] * y[j] * (alpha_old_j - alpha[j])      # 更新 alpha_i
```