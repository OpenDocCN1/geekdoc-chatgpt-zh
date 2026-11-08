retain_grad 不是想开就开的功能。频繁使用它会让每个非叶子节点的梯度张量都被额外保留，显存很快就会被吃满。所以原则很简单：确实用到时才打开，用完即关。

## 3.7 torch.nn：搭建网络的积木箱
torch.nn 是 PyTorch 建模的主力模块，里面提供了大量现成的网络组件，用来拼装复杂的深度学习模型。核心内容大致可以分为五类：

- （1）Layers：基础层集合，相当于神经网络的砖块。比如 nn.Linear、nn.Conv2d、nn.MaxPool2d、nn.ReLU 等。实例化时传入特定参数，前向时接收输入数据，每层内部都有可学习的权重和偏置。
- （2）Loss Functions：常用损失函数库。nn.MSELoss 多用于回归任务，nn.CrossEntropyLoss 用于分类任务，nn.NLLLoss 也是负对数似然损失。作用是衡量预测值与真实值的差距，为参数更新提供信号。
- （3）Utilities：模型构建与训练的辅助工具。nn.Sequential 可以把多个层按顺序串起来，nn.Module 是所有网络模块的基类，继承它就能自定义层或模型。
- （4）Normalization and Regularization Layers：正则化和归一化层。nn.Dropout 用来抑制过拟合，nn.BatchNorm1d、nn.BatchNorm2d 能加速收敛并提升性能。
- （5）Activation Functions：激活函数集合。nn.Sigmoid、nn.ReLU、nn.Tanh、nn.Softmax 等，通常接在层后引入非线性，让模型学会更复杂的模式。

### 3.7.1 nn.Linear 全连接层的核心用法
nn.Linear 是 torch.nn 里实现线性变换的类，也常叫做全连接层或线性层，可用来搭建模型中的线性部分。

构造时需要两个参数：in_features 输入特征数，out_features 输出特征数。该层把输入特征映射到输出特征，并对每个输出应用可学习的权重和可选的偏置。

计算公式为

[PRE0]

其中：
x 是输入张量，形状为（batch_size, input_features）；
A 是权重矩阵，形状为（input_features, output_features）；
b 是偏置向量，形状为（output_features，）；
y 是输出张量，形状为 (batch_size, output_features)。

一个简单示例：

[PRE1]

输出：

[PRE2]

这里创建了一个输入维度 3、输出维度 2 的线性层。把形状为 (2, 3) 的输入送入后，得到形状为 (2, 2) 的输出，表示两个样本在两个输出特征上的结果。注意 nn.Linear 的参数是可学习的，训练时会自动更新以适应任务。

### 3.7.2 nn.Sigmoid 把输出压缩到 0-1 的激活
nn.Sigmoid 会把任意实数映射到 0 到 1 之间。数学表达为 sigmoid(x) = 1 / (1 + exp(-x))，exp 为指数函数。

在实践中它常作为隐藏层的激活，也常出现在二分类输出层，把模型预测归一化为概率值。

示例，假设输入张量为 x：

[PRE3]

输出：tensor([0.8808, 0.2689, 0.6225])

先导入库、定义输入，再实例化 Sigmoid 对象，把 x 送入后得到同形状的输出。实际项目里 Sigmoid 通常内嵌在模型结构中，由前向传播自动调用。

### 3.7.3 nn.BCELoss 二分类损失怎么算
nn.BCELoss 用于计算二元交叉熵损失，主要服务于二分类问题。

它根据模型输出的概率和真实标签计算差距，适用于判断正类负类的场景。

一般调用方式：

[PRE4]

输出：tensor(0.9854)

其中 output 表示样本属于正类的概率，target 是真实标签。损失会根据两者差异计算。注意 output 和 target 形状要一致，且取值一般在 0 到 1 之间。若模型输出已过 Sigmoid，可改用 nn.BCEWithLogitsLoss，它内部会自动完成 Sigmoid。BCELoss 是二分类训练中常用的损失之一。

## 3.8 torch.optim 模型训练的优化器选择
torch.optim 提供了多种优化算法，用于在训练中更新模型参数以降低损失。

核心是 Optimizer 基类，构造时传入模型参数的可迭代对象，并提供学习率调度、权重衰减等配置接口。

常见选择有：
（1）SGD 优化器：最基础的随机梯度下降，按样本梯度更新参数。
（2）Adam 优化器：融合 AdaGrad 和 RMSProp 思想，利用梯度一阶矩和二阶矩估计自适应更新。
（3）学习率调度器：可随训练进度动态调整学习率，提升训练效果。

在 Transformer 架构里，优化器不属于模型结构，而是训练阶段的更新算法，目标是最小化损失函数。训练时通常选用 Adam，自适应学习率，兼具 Momentum 和 RMSProp 的优点。

代码中优化器一般放在主训练循环里，每个步骤根据损失对参数的梯度更新权重，通常在每个批次结束后执行。

## 3.9 从训练到测试：模型完整生命周期
PyTorch 里常规做法是把数据拆成训练集、验证集、测试集，用于训练、调参和最终评估。

- （1）数据预处理：先做归一化、均值方差计算等，再划分数据集。
- （2）定义模型：继承 torch.nn.Module，实现 __init__ 和 forward。
- （3）定义损失函数和优化器：内置有交叉熵损失 nn.CrossEntropyLoss 和均方误差 nn.MSELoss，优化器可选 SGD 或 Adam 等。

[PRE5]

- （4）训练模型：标准流程如下。
  - ① 清空梯度：optimizer.zero_grad() 把上轮梯度清零。

[PRE6]
  - ② 正向传播：把数据送入模型得到预测。

[PRE7]
  - ③ 计算损失：用损失函数比较预测与标签。

[PRE8]
  - ④ 反向传播：loss.backward() 计算梯度。

[PRE9]
  - ⑤ 更新权重：optimizer.step() 根据梯度更新参数。

[PRE10]

- （5）验证模型：每个训练周期结束后在验证集上评估，防止过拟合并调整超参数。
- （6）测试模型：训练收敛后在从未见过的测试集上评估，给出公正性能指标。
- （7）保存和加载模型：用 torch.save 和 torch.load 保存加载模型，方便复用。

整体就是这样，实操中还常涉及调试、超参数搜索等环节。

## 大模型本地化：用开源模型搭建自己的系统

## 3.10 PyTorch 重写 NumPy 神经网络
相比第 2 章用 NumPy 手写的网络，这里改用 PyTorch。NumPy 数组被转换为 PyTorch 张量，用 torch.tensor 创建输入 x 和标签 y。用 torch.randn 替代 np.random.randn 初始化权重 w 和偏置 b。

activate 函数改用 torch.where 实现条件判断，前向传播里用 torch.matmul 替代 np.dot 做矩阵乘法。测试后向传播结果与之前一致，区别在于输出类型是 torch.Tensor 而非 NumPy 数组。

### 3.10.1 单层感知机实战
先定义一个继承 nn.Module 的 Perceptron 类，内部包含线性层 linear 和 Sigmoid 激活层，forward 方法实现前向过程。

随后用训练数据 X、标签 y 初始化模型并打印结构，选用二分类常用的 BCELoss 和随机梯度下降优化器。

训练阶段反复进行前向、算损失、反向、优化。调用 optimizer.zero_grad() 清零梯度，loss.backward() 反向传播，optimizer.step() 更新参数。训练结束后再在训练集上测试并算准确率。

[PRE11]

运行结果为

[PRE12]

### 3.10.2 简单神经网络