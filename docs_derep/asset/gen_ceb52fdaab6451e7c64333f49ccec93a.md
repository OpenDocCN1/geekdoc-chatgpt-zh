线性拟合从解析解到迭代收敛的完整演示

先看最小二乘的闭式求解思路，用矩阵一次性把参数算出来。构造设计矩阵 A 与观测向量 C，先算 A 的转置乘 C 得到 B，再用 A 转置乘 A 的逆作为系数求解器 AA，最后得到最优系数 l 并生成预测值 P。配合一段等距采样 x 并投影到模型空间 xx，预留绘图对象 fig，思路一目了然。

[PRE0]
python

import numpy as np

from matplotlib import pyplot as plt

A = np.array([[5],[4]])

C = np.array([[4],[6]])

B = A.T.dot(C)

AA = np.linalg.inv(A.T.dot(A))

l=AA.dot(B)

P=A.dot(l)

x=np.linspace(-2,2,10)

x.shape=(1,10)

xx=A.dot(x)

fig = plt.figure()

拟合其实是数据与算法之间的往返。下面的流程图把关系画清楚了：原始数据集进入拟合算法，算法输出计算结果，同时会触发迭代计算，迭代结果再回到算法自身，形成闭环。

[PRE1]mermaid

graph TD

    A[数据集] --> B[拟合算法]

    B --> C[计算结果]

    B --> D[迭代计算]

    D --> B

换成梯度下降的视角，同样的问题可以用逐次修正来解。下面这段代码用三维特征做线性模型，学习率 alpha 设为 0.02，收敛阈值 epsilon 设为 0.002。每次遍历全部样本，按预测误差 diff 调整 theta0、theta1、theta2，随后计算半均方误差 error1，误差变化小于阈值就停止，最多允许 1000 轮。

[PRE2]python

x = [(2, 0, 3), (1, 0, 3), (1, 1, 3), (1, 4, 2), (1, 2, 4)]

y = [5, 6, 8, 10, 11]

epsilon = 0.002

alpha = 0.02

diff = [0, 0]

max_iter = 1000

error0 = 0

error1 = 0

cnt = 0

m = len(x)

theta0 = 0

theta1 = 0

theta2 = 0

while True:

    cnt += 1

    for i in range(m):

        diff[0] = (theta0 * x[i][0] + theta1 * x[i][1] + theta2 * x[i][2]) - y[i]

        theta0 -= alpha * diff[0] * x[i][0]

        theta1 -= alpha * diff[0] * x[i][1]

        theta2 -= alpha * diff[0] * x[i][2]

    error1 = 0

    for lp in range(len(x)):

        error1 += (y[lp] - (theta0 + theta1 * x[lp][1] + theta2 * x[lp][2])) ** 2 / 2

    if abs(error1 - error0) < epsilon:

        break

    else:

        error0 = error1

print('theta0 : %f, theta1 : %f, theta2 : %f, error1 : %f' % (theta0, theta1, theta2, error1))

print('Done: theta0 : %f, theta1 : %f, theta2 : %f' % (theta0, theta1, theta2))

print('迭代次数: %d' % cnt)

运行结果显示算法很快稳定下来。

[PRE3]

theta0 : 0.100684, theta1 : 1.564907, theta2 : 1.920652, error1 : 0.569459

Done: theta0 : 0.100684, theta1 : 1.564907, theta2 : 1.920652

迭代次数：24

24 次迭代后 theta0 接近 0.10，theta1 约 1.56，theta2 约 1.92，损失降到 0.57 左右，说明在当前学习率下收敛是有效的。

最后用一个极简的链式更新做直观验证。初始值 1，步长 gamma 0.1，每轮执行 x = x - gamma * 2 * x，即每次乘以 0.8，连续四次打印可以看出单调衰减的规律，正好对应梯度下降在凸二次上的行为。

[PRE4]python

import numpy as np

x = 1

def chain(x, gamma=0.1):

    x = x - gamma * 2 * x

    return x

for _ in range(4):

    x = chain(x)

    print(x)