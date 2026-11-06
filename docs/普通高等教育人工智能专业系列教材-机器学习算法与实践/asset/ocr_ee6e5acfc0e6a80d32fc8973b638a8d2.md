解决多项式回归问题。需要指出的是，在多项式回归分析中，检验回归系数b是否显著，实质上就是判断自变量x的i次方项对因变量y的影响是否显著。

对于二元二次多项式回归方程，令z₁=x₁，z₂=x₂，z₃=x₁²，z₄=x₂²，z₅=x₁x₂，则该二元二次多项式函数就转化为五元线性回归方程ŷ=b₀+b₁z₁+b₂z₂+…+b₅z₅。但随着自变量个数的增加，多元多项式回归分析的计算量急剧增加。

下面我们使用 Python 语言对多项式回归进行实现。

+   1）定义多项式回归函数。

代码 5-5 实现了多项式回归算法的过程。首先，根据指定的多项式阶数 degree，调用 create_polynomial_matrix 函数创建多项式特征矩阵 X_poly。多项式特征矩阵包含原始特征的各种幂次组合。然后，使用最小二乘法求解多项式系数，通过对特征矩阵 X_poly 进行矩阵计算得到。最后，返回拟合得到的多项式系数。create_polynomial_matrix 函数负责创建多项式特征矩阵，通过遍历从 1 到指定阶数 degree，使用了 np.column_stack 函数将特征向量 X 的幂次逐个添加到特征矩阵中。

# 代码 5-5  polynomial_regression 函数

```
import numpy as np

def polynomial_regression(X, y, degree):
    """
    使用多项式回归拟合数据。
    参数:
        X (numpy. array) : 输入特征向量。
        y (numpy. array) : 目标变量向量。
        degree (int) : 多项式的阶数。
    返回:
        numpy. array : 返回拟合得到的多项式系数。
    """
    # 创建多项式特征矩阵
    X_poly = create_polynomial_matrix(X, degree)

    # 使用最小二乘法求解多项式系数
    coefficients = np.linalg.inv(X_poly.T.dot(X_poly)).dot(X_poly.T).dot(y)

    return coefficients

def create_polynomial_matrix(X, degree):
    """
    创建多项式特征矩阵。
    参数:
        X (numpy. array) : 输入特征向量。
        degree (int) : 多项式的阶数。
    返回:
        numpy. array : 返回多项式特征矩阵。
    """
```