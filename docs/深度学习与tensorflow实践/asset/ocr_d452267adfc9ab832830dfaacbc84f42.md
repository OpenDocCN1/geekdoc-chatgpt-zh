神经网络而言，我们可能需要同时求解成千上万个的梯度，因此，求解多元张量的梯度可谓是一个“标配”操作。

改造范例 5-2，我们得到范例 5-4，它演示如图 5-2 所示的三元函数的梯度求解方法。更高元的梯度求解过程与其类似。

# 范例 5-4 用 TensorFlow 求三元变量函数 f = x² + 3xy + y² + z² 在 x=1，y=2 和 z=3 处的梯度（poly-variable-gradient.py）。

```
import tensorflow as tf
def func(x):
    #注意：此处 x 为一个多元张量
    return x[0] ** 2 + 3 * x[0] * x[1] + x[1] **2 + x[2] ** 3
def gradient_test():    #求 n（3）元函数的梯度
    x = tf.constant(value = [1.0, 2.0, 3.0])
    with tf.GradientTape() as tape:
    tape.watch(x)
        y = func(x)
    #一阶导数
    order_1 = tape.gradient(target = y, sources = x)
    print("多元函数 x = [1.0, 2.0, 3.0]处的梯度为：", order_1.numpy())
if __name__=="__main__":
    gradient_test()
```

# 运行结果

多元函数 x = [1.0, 2.0, 3.0]处的梯度为： [ 8. 7. 27.]

# 代码解析

从运行结果可以看出，TensorFlow 的求解结果与如图 5-2 所示的结果是等同的。需要注意的是，对于多元变量，通常用张量的形式表示，如第 04 行和第 07 行。

从前面的范例可以看出，TensorFlow 提供自动微分（Automatic Differentiation）支持，即只要我们给出目标函数的表达形式（哪怕这个目标函数非常复杂），且无须提供它的一阶导数或更高阶导数的显式形式，TensorFlow 就能自动完成求导工作。自动微分是优化机器学习模型的关键技巧之一。

# 5.2.3 梯度递减

梯度最明显的价值在于，指导我们如何快速找到损失函数的极大值。