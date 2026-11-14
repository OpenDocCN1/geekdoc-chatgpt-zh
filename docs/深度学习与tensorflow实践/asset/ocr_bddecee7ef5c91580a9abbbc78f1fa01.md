```
19 #编译模型: 定义损失函数、优化函数和性能标准
20 model.compile(optimizer='adam',
21     loss='sparse_categorical_crossentropy', #稀疏分类交叉熵
22     metrics=['accuracy']))
```

# 代码分析

细究起来，针对不同的分类模式，交叉熵损失函数也可细分多类，有的放矢地使用这些损失函数，模型的性能会有显著的提升。具体说来，对于二分类，我们可以使用 binary_crossentropy；对于多分类（通常大于等于 3），使用 categorical_crossentropy；若是稀疏分类，如手写识别数字中，它的预测向量的独热编码中，10 个向量值中才有 1 位为 1，1 在众多 0 中，显得足够稀疏，则使用 sparse_categorical_crossentropy 比较好。交叉熵损失函数中凡是有 categorical 作为修饰的，表示使用了独热编码模式。在 TensorFlow 2 中，如果使用编译模式，将预测分类值转换为独热编码，那么这个工作已经由 tf.keras 内部实现了，其细节对读者是透明的。

上面代码可以简单运行，但依然意义不大。因为到目前为止，我们仅仅完成了模型的搭建和参数搭配。只有走完深度学习算法的余下流程：模型训练、模型预测和模型评估，工作才是善始善终，结果方能为我所用。

# 6.4.12 模型的训练与预测

到目前为止，我们已经为手写数字识别任务选择了 Softmax 回归模型，同时又为 Softmax 回归定义了损失函数——交叉熵。下面要做的工作就是定义一个优化算法来训练网络参数。最常见的优化算法包括随机梯度下降法（Stochastic Gradient Descent，SGD）、AdaGrad（自适应梯度）和 Adam（Kingma 和 Lei Ba 提出的一种结合 AdaGrad 和 RMSProp 优点的优化算法）。

一旦损失函数定义完毕，整个模型就可以根据优化算法的指引，每次微调网络参数（如 W 和 b），使其朝着最小化交叉熵的方向前进。如范例 6-6 所示。

# 范例 6-6 MNIST 模型的训练和预测（keras-mnist-train-predict.py）

```
01 import numpy as np
02 from tensorflow.keras.datasets.mnist import load_data
03 from tensorflow import keras
04 # 导入数据
05 (X_train, y_train), (X_test, y_test) = load_data()
06 # 获取图片的大小
```