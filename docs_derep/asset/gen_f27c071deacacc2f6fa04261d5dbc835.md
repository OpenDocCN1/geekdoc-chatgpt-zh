[PRE0]

执行上述代码后，输出结果为：

rel4= [ 2.17215538 2.76967359]

对比之前的 rel1 可以看出，对于正确分类与错误分类的样本，其交叉熵数值之间的差距，不再像标准 one-hot 编码那样显著。

#### 6.5.3 实例 24：sparse 交叉熵的实战应用

接下来通过一个具体案例，演示 `sparse_softmax_cross_entropy_with_logits` 函数的调用方法。该函数接受非 one-hot 格式的标签，因此我们需要将先前的标签转换为具体的数值索引 [2, 1]，完整代码如下。

##### 实例描述

利用 `sparse_softmax_cross_entropy_with_logits` 函数对非 one-hot 标签计算交叉熵，并对比其与 one-hot 标签用法的异同。

##### 代码6-1 softmax应用（续）

[PRE1]

代码运行结果如下：

rel5= [ 0.02215516 3.09967351]

可见 rel5 的输出与前文 rel1 的结果完全一致。

#### 6.5.4 实例 25：Loss 值的计算与验证

在实际神经网络构建中，仅获得代码 6-1 中的数组是不够的，还需对其求平均，最终将其压缩为一个具体的标量数值。

##### 实例描述

本例分别对前文的交叉熵结果 `result1` 与 softmax 处理后的 `logits_scaled` 计算 loss，旨在验证以下结论：

- (1) 经过 `softmax_cross_entropy_with_logits` 计算后，直接对结果取均值即得 loss。
- (2) 对 softmax 后的概率分布，使用 `-tf.reduce_sum(labels * tf.log(logits_scaled))` 计算 loss。
- (3) 对 softmax 后的结果使用 `-tf.reduce_sum(labels*tf.log(logits_scaled),1)`，其等价于 `softmax_cross_entropy_with_logits` 的输出。
- (4) 结合（1）和（3）可推导：对（3）的结果求均值同样能得到正确的 loss，合并公式为：`tf.reduce_sum(-tf.reduce_sum(labels*tf.log(logits_scaled),1)) = loss`（此为验证推导所得，读者可自行复现）。

##### 代码6-1 softmax应用（续）

[PRE2]

运行结果输出：

loss= 3.12183

这就是我们最终需要的损失函数值。

针对已得到 softmax 概率 (`rel3`) 的情况，loss 计算公式可进一步简化为：

loss2 = -tf.reduce_sum(labels * tf.log(logits_scaled))

下面附上验证代码。

##### 代码6-1 softmax应用（续）

[PRE3]

执行结果如下：

loss2= 3.12183

数值与 loss 完全吻合。

#### 6.5.5 课后练习

尝试修改上一章的代码（`5-2mnist 分类.py`），改用 `sparse_softmax_cross_entropy_with_logits` 函数计算交叉熵。

参考答案请见本书源码中的 `6-2 sparse_softmax_with_mnist.py`。

### 6.6 梯度下降——驱动模型逼近最小偏差

前文多次提及梯度下降，但未系统展开。本节将详细阐述其核心作用及常用变体技巧。

#### 6.6.1 梯度下降的原理与分类

梯度下降法是一种优化算法，亦称最速下降法，广泛应用于机器学习与 AI 中，通过迭代逼近最小偏差模型。其核心在于沿着负梯度方向搜索，以求得函数极小值。

训练过程中，每次前向传播均会产出预测值与真实值之间的损失值；损失越小，模型表现越优。梯度下降算法即介入于此，助力寻找全局最小损失值，进而反推最优参数 `b` 与 `w`，实现模型优化。

主流梯度下降方法主要分三类：批量梯度下降（BGD）、随机梯度下降（SGD）与小批量梯度下降（Mini-batch GD）。

- **批量梯度下降 (BGD)**：遍历全量数据集计算一次损失函数，再求梯度更新参数。每次参数更新需扫描全体样本，计算量大、速度慢，不支持在线学习。
- **随机梯度下降 (SGD)**：每读取一个样本即计算损失并更新参数。速度较快，但收敛性较差，易在最优解附近震荡，难以精准命中；连续两次更新甚至可能相互抵消，导致目标函数剧烈波动。
- **小批量梯度下降 (Mini-batch GD)**：为折中上述两者缺点而生。将数据集切分为若干小批次，按批次更新参数。批内样本共同决定梯度方向，有效降低随机性、防止跑偏；因批次样本量远小于全量数据，计算开销也可控。

#### 6.6.2 TensorFlow 中的优化器函数

重点介绍 TensorFlow 中实现随机梯度下降优化的相关函数。TensorFlow 通过 `Optimizer` 优化器类完成训练优化，针对不同算法提供了对应的类，汇总见表 6-3。

| 操作 | 描述 |

|---|---|

| tf.train.GradientDescentOptimizer(learning_rate,use_locking=False, name="GradientDescent") | 标准梯度下降优化器 |

| tf.train.AdadeltaOptimizer(learning_rate=0.001,rho=0.95, epsilon=1e-08, use_locking=False, name="Adadelta") | Adadelta 优化器 |

| tf.train.AdagradOptimizer(learning_rate,initial_accumulator_value=0.1, use_locking=False, name="Adagrad") | Adagrad 优化器 |

| tf.train.MomentumOptimizer(learning_rate,momentum,use_locking=False,name="Momentum",use_nesterov=False) | Momentum 优化器（含动量参数） |

| tf.train.AdamOptimizer(learning_rate=0.001,beta1=0.9, beta2=0.999, epsilon=1e-08, use_locking=False, name="Adam") | Adam 优化器（当前最常用） |

| tf.train.FtrlOptimizer(learning_rate,learning_rate_power=-0.5, initial_accumulator_value=0.1,l1_regularization_strength=0.0, l2_regularization_strength=0.0, use_locking=False, name="Ftrl") | FTRL 算法优化器 |

| tf.train.RMSPropOptimizer(learning_rate,decay=0.9,momentum=0.0, epsilon=1e-10, use_locking=False, name="RMSProp") | RMSProp 算法优化器 |

训练流程中，首先实例化一个优化器（如 `tf.train.GradientDescentOptimizer`），指定学习率：

[PRE4]

随后调用 `minimize()` 方法传入损失节点 `loss`，并在外层循环迭代，优化器便会按步沿着 loss 最小化方向自动更新参数。

整个求导与反向传播过程均在优化器内部自动完成。目前工程实践中 Adam 优化器应用最广，其算法细节超出本书范围，感兴趣的读者可自行拓展。

#### 6.6.3 学习率衰减——在训练速度与精度间寻找平衡

上述所有优化器的首个参数 `learning_rate` 即学习率。其大小直接决定了训练速度与最终精度的博弈：

- 学习率过大：训练加速，但精度受损，易跨过最优解；
- 学习率过小：精度有保障，但训练周期漫长，效率低下。

为此引入**学习率衰减** 策略，旨在兼顾二者优势：训练初期用大学习率快速推进，后期切换小学习率精细打磨。具体实现方式如下：

[PRE5]

衰减节奏由 `global_step` 与 `decay_steps` 共同决定，计算公式为：

[PRE6]

参数 `staircase` 默认为 False。若设为 True，则不具备衰减功能，仅按公式初始化一个固定学习率值。

代码示例如下：

[PRE7]

此处定义的学习率即为衰减学习率，含义为：当迭代至 `global_step` 步时，学习率每 10 万步按 0.96 的比率指数衰减。

此外，微调预训练模型时常需为不同层设置差异化学习率，后续章节将详述。

> **提示**：增大批次大小同样能达到类似衰减学习率的正则化效果。但这要求训练时的最小批次与推理应用场景一致。若条件允许，建议优先采用增大批次的策略，可减少开发与计算开销。

#### 6.6.4 实例 26：学习率衰减的代码演示

本例演示学习率衰减的具体用法。设迭代计数器 `global_step` 记录循环轮次，初始学习率 0.1，每 10 步按 0.9 的速率衰减。

##### 实例描述

定义学习率变量及衰减系数，设定迭代次数，打印每轮迭代的步数与当前学习率，直观观察衰减过程。

##### 代码6-3 退化学习率

[PRE8]

运行输出片段：

0.1

1 0.1

2 0.0989519

3 0.0979148

...

10 0.0909533

11 0.09

12 0.0890567

...

20 0.0818579

第一列为迭代次数，第二列为对应学习率。可见学习率平滑下降，第 11 步已从 0.1 降至 0.09。

**注意**：这是训练神经网络的常用策略——起步大步长，后期小步长。使用时务必将当前迭代次数 `global_step` 传入，否则衰减机制不生效。

### 6.7 模型参数初始化策略

定义可学习参数时，可通过 `get_variable` 或 `Variable` 两种方式。参数的初始化分布对网络收敛影响深远，TensorFlow 提供了丰富的初始化函数。使用 `get_variable` 时，其 `initializer` 参数可选用表 6-4 列出的函数。

#### 表 6-4 常用初始化函数速查

| 操作 | 描述 |

|---|---|

| tf.constant_initializer(value) | 指定常量值初始化 |

| tf.random_uniform_initializer(a, b) | 区间 [a, b] 内均匀分布初始化 |

| tf.random_normal_initializer(mean, stddev) | 指定均值和标准差的正态分布初始化 |

| tf.constant_initializer(value=0, dtype=tf.float32) | 零常量初始化 (float32) |

| tf.random_normal_initializer(mean=0.0,stddev=1.0, seed=None, dtype=tf.float32) | 标准正态分布随机初始化 |

| tf.truncated_normal_initializer(mean=0.0,stddev=1.0, seed=None, dtype=tf.float32) | **截断正态分布**：剔除 [mean-2*stddev, mean+2*stddev] 范围外的异常值 |

| tf.random_uniform_initializer(minval=0,maxval=None, seed=None, dtype=tf.float32) | 指定区间 [minval, maxval] 的均匀分布 |

| tf.uniform_unit_scaling_initializer(factor=1.0, seed=None, dtype=tf.float32) | 均匀分布且保持输出数量级稳定的初始化 |