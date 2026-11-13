[PRE0]

# 第一步：数据准备与可视化

```python
import numpy as np
import matplotlib.pyplot as plt

# 生成带噪声的线性数据
X = np.linspace(-10, 10, 1000)
features = X.reshape(-1, 1)
labels = X + 0.4 * np.random.rand(len(X))

# 绘图配置（支持中文显示）
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

plt.plot(features, labels, 'o', markersize=2, alpha=0.5)
plt.xlabel('自变量')
plt.ylabel('因变量')
plt.title('原始数据分布')
plt.show()
```

# 第二步：拆分训练集与测试集

```python
from sklearn.model_selection import train_test_split

# 98% 用于训练，2% 用于测试
X_train, X_test, y_train, y_test = train_test_split(
    features, labels, train_size=0.98, random_state=42
)
```

# 第三步：构建并训练三种回归模型

```python
from sklearn.tree import DecisionTreeRegressor
from sklearn.ensemble import AdaBoostRegressor
from sklearn.svm import SVR

# 1. 单棵决策树（限制深度防止过拟合）
model_dt = DecisionTreeRegressor(max_depth=20)

# 2. AdaBoost 集成 SVR（基学习器为 RBF 核 SVR）
model_ada_svr = AdaBoostRegressor(
    base_estimator=SVR(kernel='rbf'),
    n_estimators=50,
    random_state=42
)

# 3. 单独的 SVR（RBF 核）
model_svr = SVR(kernel='rbf')

# 统一训练
for model in (model_dt, model_ada_svr, model_svr):
    model.fit(X_train, y_train)
```

# 第四步：预测与误差评估

```python
from sklearn.metrics import mean_squared_error

# 获取预测值
pred_dt = model_dt.predict(X_test)
pred_ada_svr = model_ada_svr.predict(X_test)
pred_svr = model_svr.predict(X_test)

# 打印对比结果
print('=' * 60)
print('真实值 (测试集前6样本):')
print(y_test[:6])

print('\n决策树预测值:')
print(pred_dt)
print(f'MSE: {mean_squared_error(y_test, pred_dt):.6f}')

print('\nAdaBoost + SVR 预测值:')
print(pred_ada_svr)
print(f'MSE: {mean_squared_error(y_test, pred_ada_svr):.6f}')

print('\n单独 SVR 预测值:')
print(pred_svr)
print(f'MSE: {mean_squared_error(y_test, pred_svr):.6f}')
print('=' * 60)
```

[PRE1]

#### 实测对比数据 ####

**真实标签 (测试集片段):**
`[ 9.621  -4.897  -4.060   8.843  -1.259  -6.115]`

**决策树预测:**
`[ 9.841  -4.677  -4.141   9.047  -1.187  -6.473]`
**均方误差 (MSE):** `0.0464`

**AdaBoost + SVR 预测:**
`[ 9.590  -4.706  -4.169   9.003  -1.181  -6.264]`
**均方误差 (MSE):** `0.0172` ⭐ **表现最优**

**单独 SVR 预测:**
`[ 9.312  -4.709  -4.167   8.869  -1.181  -6.275]`
**均方误差 (MSE):** `0.0292`

[PRE2]