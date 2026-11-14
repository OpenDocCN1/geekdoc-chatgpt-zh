
当有一系列的 x 输入到图 9-3 左侧结构中后，展开就变成了右侧的样子，其实就是一个含有隐藏层的网络，只不过隐藏层的输出变成了两份，一份传到下一个节点，另一份传给本身节点。其时序图如图 9-4 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_232_0.png)

图 9-4 RNN 正向传播时序

- 假设有 3 个时序 $t_1$、$t_2$、$t_3$，如图 9-4 所示，在 RNN 中可以分解成以下 3 个步骤。
- (1) 开始时 $t_1$ 通过自己的输入权重和 0 作为输入，生成了 $out_1$。
- (2) $out_1$ 通过自己的权重生成了 $h_1$，然后和 $t_2$ 经过输入权重转化后一起作为输入，生成了 $out_2$。
- (3) $out_2$ 通过同样的隐藏层权重生成了 $h_2$，然后和 $t_3$ 经过输入权重转化后一起作为输入，生成了 $out_3$。

## 9.1.4 随时间反向传播

与单神经元相似，RNN 也需要反向传播误差来调整自己的参数。RNN 网络使用随时间反向传播（BackPropagation Through Time，BPTT）的链式求导算法来反向传播误差。

- 先来回顾一下反向传播的 BP 算法，如图 9-5 所示。
- 这是一个含有一个隐藏层的网络结构。隐藏层只有一个节点。具体的过程如下：
- (1) 有一个批次含有 3 个数据 A、B、C，批次中每个样本有两个数（x1、x2）通过权重（w1、w2）来到隐藏层 H 并生成批次 h，如图 9-5 中 w1、w2 两条直线所在方向。
- (2) 该批次的 h 通过隐藏层权重 p1 生成最终的输出结果 y。
- (3) y 与最终的标签 p 比较，生成输出层误差 loss(y,p) 。
- (4) loss(y,p) 与生成 y 的导数相乘，得到 Del_y。Del_y 为输出层所需要的修改值。
- (5) 将 h 的转置与 del_y 相乘得到 del_p1。这是源于 h 与 p1 相等得到的 y，见第 (2) 步。

![](img/d841d305683d65f55b29f4e3f25e532c_233_0.png)

图 9-5 BP 反向传播 1

- (6) 最终将该批次的 del_p1 求和并更新到 p1 上。

- (7) 同理，再将误差反向传递到上一层：计算 Del_h。得到 Del_h 后再计算 del_w1、del_w2 并更新。

若 BP 的算法读者已经理解了，下面再来比较一下 BPTT 算法，如图 9-6 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_233_1.png)

图 9-6 RNN 反向传播

图 9-6 中，同样是一个批次的数据 A、B、C，按顺序进入循环神经网络。正向传播的实例是 B 正在进入神经网络的过程，可以看到 A 的 h 参与进来并一起经过 p1 生成了 B 的 y。因为 C 还没有进入，为了清晰，这里用灰色表示。

当所有块都进入之后，会将 p 标签与输出进行 Del_y 的运算，由于 C 块的 y 是最后生成的，所以我们先从 C 块开始对 h 的输出传递误差 Del_h。

图 9-6 中的反向传播是表示 C 块已经反向传播完成，开始 B 块反向传播的状态，可以看到 B 块 Del_h 是由 B 块的 del_y 和 C 块的 del_h 通过计算得来的。这就是与 BP 算法不同的地方（BP 中 Del_h 直接与自己的 Del_y 相关，不会与其他的值有联系）。

作为一个批次的数据，正向传播时是沿着 ABC 的顺序，当反向传播时，就按照正向传播的相反顺序，即每个节点的 CBA 逐个计算并传递梯度。

## 9.2 简单 RNN

了解完 RNN 的原理后，下面一起来实现一个简单的 RNN 网络。

### 9.2.1 实例 55: 简单循环神经网络实现——裸写一个退位减法器

本例将把前面所讲述的内容用代码实现一遍。如果前面的描述读者还不明白，可以通过这个例子，加深对前面内容的理解。

本例是一个纯手写的代码例子，使用 Python 手动搭建一个简单的 RNN 网络，让它来拟合一个退位减法。退位减法也具有 RNN 的特性，即输入的两个数相减时，一旦发生退位运算，需要将中间状态保存起来，当高位的数传入时将退位标志一并传入参与运算。

下面就来用代码实现 RNN 拟合减法，具体步骤如下。

### 实例描述

使用 Python 编写简单循环神经网络拟合一个退位减法的操作，观察其反向传播过程。

- 1. 定义基本函数

首先手动写一个 Sigmoid 函数及其导数（导数用于反向传播）。

```
代码9-1 subtraction
01 import copy, numpy as np
02 np.random.seed(0)       #固定随机数生成器的种子，可以每次得到一样的值
03 def sigmoid(x):     #激活函数
04     output = 1/(1+np.exp(-x))
05     return output
06
07 def sigmoid_output_to_derivative(output): #激活函数的导数
08     return output*(1-output)
```

代码9-1 subtraction（续）
```
09 int2binary = {}
10 binary_dim = 8
11 ## 计算 0~256 的二进制表示
12 largest_number = pow(2,binary_dim)
13 binary = np.unpackbits(
14     np.array([range(largest_number)],dtype=np.uint8).T,axis=1)
15 for i in range(largest_number):
16 int2binary[i] = binary[i]
```

### 3. 定义参数

定义学习参数：隐藏层的权重 synapse_0、循环节点的权重 synapse_h（输入节点 16、输出节点 16）、输出层的权重 synapse_1（输入 16 节点，输出 1 节点）。为了减小复杂度，这里只设置 w 权重，b 被忽略。

代码9-1 subtraction（续）
```
17 # 参数设置
18 alpha = 0.9 #学习速率
19 input_dim = 2 #输入的维度是 2，减数和被减数
20 hidden_dim = 16
21 output_dim = 1 #输出维度为 1

23 # 初始化网络
24 synapse_0 = (2*np.random.random((input_dim,hidden_dim)) - 1)*0.05 #维度为 2*16，2 是输入维度，16 是隐藏层维度
25 synapse_1 = (2*np.random.random((hidden_dim,output_dim)) - 1)*0.05
26 synapse_h = (2*np.random.random((hidden_dim,hidden_dim)) - 1)*0.05
27 # => [-0.05, 0.05),

29 # 用于存放反向传播的权重更新值
30 synapse_0_update = np.zeros_like(synapse_0)
31 synapse_1_update = np.zeros_like(synapse_1)
32 synapse_h_update = np.zeros_like(synapse_h)
```

synapse_0_update 在前面很少见到，是因为它被隐含在优化器里了。这里全部“裸写”（不使用 TensorFlow 库函数），需要定义一组变量，用于反向优化参数时存放参数需要调整的调整值，对应于前面的 3 个权重 synapse_0、synapse_1 和 synapse_h。

### 4. 准备样本数据

大致是这样的过程：

- (1) 建立循环生成样本数据，先生成两个数 a 和 b。如果 a 小于 b，就交换位置，保证被减数大。
- (2) 计算出相减的结果 c。
- (3) 将 3 个数转换成二进制，为模型计算做准备。

将上面过程一一实现，代码如下。

代码9-1 subtraction（续）
```
33 # 开始训练
34 for j in range(10000):

37 a_int = np.random.randint(largest_number)
38    #生成一个数字b，b 的最大值取的是 largest_number/2，作为被减数，让它小一点
39    b_int = np.random.randint(largest_number/2)
40    #如果生成的b大了，那么交换一下
41    if a_int<b_int:
42        tt = a_int
43        b_int = a_int
44        a_int=tt

45    a = int2binary[a_int]    # 二进制编码
46    b = int2binary[b_int]    # 二进制编码
47    # 正确的答案
48    c_int = a_int - b_int
49    c = int2binary[c_int]
```

### 5. 模型初始化

初始化输出值为 0，初始化总误差为 0，定义 layer_2_deltas 存储反向传播过程中的循环层的误差，layer_1_values 为隐藏层的输出值，由于第一个数据传入时，没有前面的隐藏层输出值来作为本次的输入，所以需要为其定义一个初始值，这里定义为 0.1。

代码9-1 subtraction (续)
```
51    # 存储神经网络的预测值
52    d = np.zeros_like(c)
53    overallError = 0    #每次把总误差清零

55    layer_2_deltas = list()    #存储每个时间点输出层的误差
56    layer_1_values = list()    #存储每个时间点隐藏层的值

58    layer_1_values.append(np.ones(hidden_dim)*0.1)    #一开始没有隐藏层，所以初始化一下原始值为 0.1
```

### 6. 正向传播

循环遍历每个二进制位，从个位开始依次相减，并将中间隐藏层的输出传入下一位的计算（退位减法），把每一个时间点的误差导数都记录下来，同时统计总误差，为输出准备。

代码9-1 subtraction (续)
```
59    for position in range(binary_dim):    #循环遍历每一个二进制位

61    # 生成输入和输出
62    X = np.array([[a[binary_dim - position - 1],b[binary_dim - position - 1]]])    #从右到左，每次取两个输入数字的一个 bit 位
63    y = np.array([[c[binary_dim - position - 1]]]).T    #正确答案
64    # hidden layer (input ~+ prev_hidden)
65    layer_1 = sigmoid(np.dot(X,synapse_0) + np.dot(layer_1_values[-1],synapse_h))# (输入层 + 之前的隐藏层) -> 新的隐藏层，这是体现循环神经网络的最核心的地方
# output layer (new binary representation)
layer_2 = sigmoid(np.dot(layer_1,synapse_1))
#隐藏层 * 隐藏层到输出层的转化矩阵 synapse_1 -> 输出层
layer_2_error = y - layer_2                     #预测误差
layer_2_deltas.append((layer_2_error)*sigmoid_output_to_derivative(layer_2)) #把每一个时间点的误差导数都记录下来
overallError += np.abs(layer_2_error[0])        #总误差
d[binary_dim - position - 1] = np.round(layer_2[0][0]) #记录下每一个预测 bit 位
# 将隐藏层保存起来。下个时间序列便可以使用
layer_1_values.append(copy.deepcopy(layer_1))
# 记录下隐藏层的值，在下一个时间点用
future_layer_1_delta = np.zeros(hidden_dim)
```

最后一行代码是为了反向传播准备的初始化。同正向传播一样，反向传播是从最后一次往前反向计算误差，对于每一个当前的计算都需要有它的下一次结果参与。
反向计算是从最后一次开始的，它没有后一次的输出，所以需要初始化一个值作为其后一次的输入，这里初始化为 0。

### 7. 反向训练

初始化之后，开始从高位往回遍历，一次对每一位的所有层计算误差，并根据每层误差对权重求偏导，得到其调整值，最终将每一位算出的各层权重的调整值加在一起乘以学习率，来更新各层的权重，完成一次优化训练。

代码9-1 subtraction (续)
```
#反向传播，从最后一个时间点到第一个时间点
for position in range(binary_dim):
    X = np.array([[a[position],b[position]]]) #最后一次的两个输入
    layer_1 = layer_1_values[-position-1] #当前时间点的隐藏层
    prev_layer_1 = layer_1_values[-position-2] #前一个时间点的隐藏层

    layer_2_delta = layer_2_deltas[-position-1] #当前时间点输出层导数
    # 通过后一个时间点（因为是反向传播）的隐藏层误差和当前时间点的输出层误差，计算当前时间点的隐藏层误差
    layer_1_delta = (future_layer_1_delta.dot(synapse_h.T) + layer_2_delta.dot(synapse_1.T)) * sigmoid_output_to_derivative(layer_1)
    # 等完成了所有反向传播误差计算，才会更新权重矩阵，先暂时把更新矩阵存起来
    synapse_1_update += np.atleast_2d(layer_1).T.dot(layer_2_delta)
    synapse_h_update += np.atleast_2d(prev_layer_1).T.dot(layer_1_delta)
synapse_0_update += X.T.dot(layer_1_delta)

future_layer_1_delta = layer_1_delta

# 完成所有反向传播之后，更新权重矩阵，并把矩阵变量清零
synapse_0 += synapse_0_update * alpha
synapse_1 += synapse_1_update * alpha
synapse_h += synapse_h_update * alpha
synapse_0_update *= 0
synapse_1_update *= 0
synapse_h_update *= 0
```

更新完后会将中间变量值清零。

### 8. 输出结果

每运行 800 次将结果输出，代码如下。

代码9-1 subtraction (续)
```
104 # 打印输出过程
105 if(j % 800 == 0):

107 print("总误差:" + str(overallError))
108 print("Pred:" + str(d))
109 print("True:" + str(c))
110 out = 0
111 for index,x in enumerate(reversed(d)):
112 out += x*pow(2,index)
113 print(str(a_int) + " - " + str(b_int) + " = " + str(out))
114 print("-----------------------------------")
```

运行代码，输出结果如下：

```
总误差:[ 3.97242498]
Pred:[0 0 0 0 0 0 0 0]
True:[0 0 0 0 0 0 0 0]
9 - 9 = 0
-----------------------------------
总误差:[ 2.1721182]
Pred:[0 0 0 0 0 0 0 0]
True:[0 0 0 0 1 0 0 0 1]
17 - 0 = 0
-----------------------------------
......
-----------------------------------
总误差:[ 0.04588656]
Pred:[1 0 0 1 0 1 1 0]
True:[1 0 0 1 0 1 1 0]
167 - 17 = 150
-----------------------------------
总误差:[ 0.08098026]
Pred: [1 0 0 1 1 0 0 0]
True: [1 0 0 1 1 0 0 0]
204 - 52 = 152
--------------------------------
总误差：[ 0.03262333]
Pred: [1 1 0 0 0 0 0 0]
True: [1 1 0 0 0 0 0 0]
209 - 17 = 192
--------------------------------
```

可以看到，刚开始还不准，随着迭代次数增加，到后来已经可以完全拟合退位减法了。

### 9.2.2 实例 56：使用 RNN 网络拟合回声信号序列

本例使用 TensorFlow 中的函数来演示搭建一个简单 RNN 网络，使用一串随机的模拟数据作为原始信号，让 RNN 网络来拟合其对应的回声信号。详细介绍如下。

样本数据为一串随机的由 0、1 组成的数字，将其当成发射出去的一串信号。当碰到阻挡被反弹回来时，会收到原始信号的回音。

如果步长为 3，那么输入和输出的序列如图 9-7 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_239_0.png)

图 9-7 回声序列

如图 9-7 所示，回声序列的前 3 项是 null，原序列的第一个信号 0，对应的是回声序列的第 4 项，即回声序列的每一个数都会比原序列滞后 3 个时序。本例的任务就是将序列截取出来，对于每个原序列来预测它的回声序列。

构建的网络结构如图 9-8 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_239_1.png)

图 9-8 echo 例子网络结构
