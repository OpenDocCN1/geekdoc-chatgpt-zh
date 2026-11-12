# TensorFlow 1.x 实战入门：从零手写 Softmax 识别 MNIST 手写数字

想快速上手 TensorFlow 经典入门案例？本文带你一步步复现基于 Softmax 回归的 MNIST 手写数字识别模型，覆盖数据加载、可视化探查、模型构建、损失定义到训练迭代的完整流程。

---

### 一、 极速获取与解析 MNIST 数据集

TensorFlow 内置工具让数据准备变得异常丝滑。只需指定本地路径，缺失即自动下载，`one_hot=True` 直接给标签转成独热编码格式，省去预处理烦恼。

[PRE0]python

数据到手第一件事当然是“体检”。训练集包含 5.5 万张 28×28 展平为 784 维的图片，对应 10 维标签；测试集 1 万张，验证集 5 千张，维度完全对齐。

[PRE1]python

光看 shape 不过瘾？用 `pylab` 把第 2 张训练图还原成 28×28 灰度图直接渲染，手写数字轮廓瞬间跃然屏上。再把第 54 张（索引 53）的像素向量与独热标签打印出来，数据细节一目了然。

---

### 二、 批量喂数据的利器：`next_batch`

训练时不可能一次塞全量数据，`next_batch(batch_size)` 就是随机抓取小批量样本的神器，配合 `feed_dict` 实时喂给占位符，显存占用可控且收敛更稳。

[PRE2]python

---

### 三、 搭建 Softmax 回归模型核心图

#### 1. 定义输入接口
用 `tf.placeholder` 预留入口：`x` 接收任意批量的 784 维向量，`y_` 接收对应的 10 维真实标签。

[PRE3]python
[PRE6]python

#### 2. 初始化可学习参数
权重矩阵 `W` 形状 `[784, 10]`，偏置 `b` 形状 `[10]`，全零初始化虽简单但足以跑通流程。

[PRE4]python

#### 3. 前向推理与概率输出
线性变换 `x·W + b` 后接 `softmax`，输出 `y` 即为 10 个类别的概率分布，数值和为 1。

[PRE5]python

#### 4. 定义损失函数
交叉熵 `cross_entropy = -Σ(y_ * log(y))` 衡量预测分布与真实分布的差距，值越小拟合越好。

[PRE6]python

#### 5. 选定优化器
梯度下降优化器，学习率设为 0.03，目标是最小化交叉熵损失。

[PRE7]python

---

### 四、 会话管理与训练循环

#### 1. 变量初始化
早期版本用 `tf.initialize_all_variables()` 一键初始化所有 `Variable`。

[PRE8]python

#### 2. 启动会话并跑初始化
显式创建 `tf.Session` 并 `run(init)`，图才真正在设备上跑起来。

[PRE9]python

#### 3. 迭代训练 1000 轮
每轮抓 100 张训练图（`batch_size=100`），构造 `feed_dict` 执行 `train_step`，参数随梯度逐步更新。

[PRE10]python

---

### 五、 复盘要点速记

| 环节 | 关键动作 | 代码标记 |
| :--- | :--- | :--- |
| **数据** | 自动下载、独热编码、形状校验、可视化 | `PRE0` `PRE1` |
| **批次** | `next_batch` 随机小批量采样 | `PRE2` |
| **模型** | Placeholder → Variable → Softmax → CrossEntropy → GD | `PRE3`–`PRE7` |
| **运行** | Session + Init + 1000×Mini-batch 循环 | `PRE8`–`PRE10` |

> **温馨提示**：以上代码基于 TensorFlow 1.x 风格（静态图 + Session）。若用 TF 2.x，请改用 `tf.compat.v1` 或直接拥抱 Eager Execution / Keras API，写法会更简洁。

动手敲一遍，你就完成了深度学习“Hello World”的经典闭环！🚀