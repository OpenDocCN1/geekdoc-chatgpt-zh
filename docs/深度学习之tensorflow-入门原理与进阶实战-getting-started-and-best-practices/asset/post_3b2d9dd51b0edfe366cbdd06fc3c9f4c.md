
## 代码9-30 基本Seq2Seq（续）

```python
97 sess = tf.InteractiveSession()
98
99 def train_batch(batch_size):
100     X, Y = generate_data(isTrain=True, batch_size=batch_size)
101     feed_dict = {encoder_input[t]: X[t] for t in range(len(encoder_input))}
102
103     feed_dict.update({expected_output[t]: Y[t] for t in range(len(expected_output))})
104
105     c = np.concatenate(([np.zeros_like(Y[0])],Y[:-1]),axis = 0)
106
107     feed_dict.update({decode_input[t]: c[t] for t in range(len(c))})
108
109     _, loss_t = sess.run([train_op, loss], feed_dict)
110     return loss_t
111
112 def test_batch(batch_size):
113     X, Y = generate_data(isTrain=True, batch_size=batch_size)
114     feed_dict = {encoder_input[t]: X[t] for t in range(len(encoder_input))}
115
116     feed_dict.update({expected_output[t]: Y[t] for t in range(len(expected_output))})
117
118     c = np.concatenate(([np.zeros_like(Y[0])],Y[:-1]),axis = 0)      #来预测最后一个序列
119
120     feed_dict.update({decode_input[t]: c[t] for t in range(len(c))})
121     output_lossv,reg_lossv,loss_t = sess.run([output_loss,reg_loss,loss], feed_dict)
122     print("-------------------------")
123     print(output_lossv,reg_lossv)
124     return loss_t
125
126 # 训练
127 train_losses = []
128 test_losses = []
129
130 sess.run(tf.global_variables_initializer())
131 for t in range(nb_iters + 1):
132     train_loss = train_batch(batch_size)
133     train_losses.append(train_loss)
134     if t % 50 == 0:
135         test_loss = test_batch(batch_size)
136         test_losses.append(test_loss)
137         print("Step {}/{}，train loss: {}, TEST loss: {}".format(t,nb_iters, train_loss, test_loss))
138 print("Fin. train loss: {}, TEST loss: {}".format(train_loss, test_loss))
139
140 # 输出 loss 图例
141 plt.figure(figsize=(12, 6))
142 plt.plot(np.array(range(0, len(test_losses))) /
143     float(len(test_losses) - 1) * (len(train_losses) - 1),
144     np.log(test_losses),label="Test loss")
145
146 plt.plot(np.log(train_losses),label="Train loss")
147 plt.title("Training errors over time (on a logarithmic scale)")
148 plt.xlabel('Iteration')
149 plt.ylabel('log(Loss)')
150 plt.legend(loc='best')
151 plt.show()
```

对于 decode_input 的输入要重点说明一下，将其第一个序列的输入变为 0，作为起始输入的标记，接上后续的 Y 数据（未来序列）作为解码器部分的 Decoder 来输入。由于第一个序列被占用了，保证总长度不变的情况下，Y 的最后一个序列没有作为 Decoder 的输入。但是输出时会有关于未来序列预测的全部序列值，并在计算 loss 时与真实值 Y 进行平方差。

最终将 loss 值通过 plot 打印出来，生成结果如下，loss 结果曲线如图 9-28 所示。

training examples :
(15, 3, 1)
(seq_length, batch_size, output_dim)

7.66522 113.373
Step 0/100,train loss: 8.341724395751953,   TEST loss:8.005338668823242
----------------------------------------------
1.11881 99.788
Step 50/100,train loss:2.0858113765716553,    TEST loss:1.418175220489502
----------------------------------------------
0.618375 83.6507
Step 100/100,train loss:0.9577032327651978,   TEST loss:0.8693273067474365
Fin. train loss:0.9577032327651978,    TEST loss:0.8693273067474365

![](img/d841d305683d65f55b29f4e3f25e532c_322_0.png)

图 9-28 loss 结果曲线

## 5. 准备可视化数据

一般情况下，将整个输出值进行显示即可。但这里考虑到要配合使用时的演示，因此我们需要模型来预测未来序列，即没有 decode_input 的输入。前面说了，这种情况可以将 decode_input 全设为 0，但其识别效果不客观。为了模型可用，可以将预测值范围稍加改变，只预测之后一次时间序列的值。例如，知道前面的所有序列，预测当天股票的收盘价格、开盘价格等。这也是非常实际的应用。

于是在可视化部分，取时间序列 2 倍的样本，前一倍用于输入模型，会产生最后一天的预测值，同时也将后一倍的数据显示出来，用于比对每个序列的预测值。

### 代码9-30 基本Seq2Seq（续）

```python
149 # 测试
150 nb_predictions = 4
151 print("visualize {} predictions data:".format(nb_predictions))
152
153 preout = []
154 X, Y = generate_data(isTrain=False, batch_size=nb_predictions)
155 print(np.shape(X), np.shape(Y))
156 for tt in range(seq_length):
157     feed_dict = {encoder_input[t]: X[t+tt] for t in range(seq_length)}
158     feed_dict.update({expected_output[t]: Y[t+tt] for t in range(len(expected_output))})
159     c=np.concatenate(([np.zeros_like(Y[0])],Y[tt:seq_length+tt-1]),
160     axis = 0) #从前 15 个序列的最后一个开始预测
161
162     feed_dict.update({decode_input[t]: c[t] for t in range(len(c))})
163     outputs = np.array(sess.run([reshaped_outputs], feed_dict)[0])
164     preout.append(outputs[-1])
165
166 print(np.shape(preout)) #将每个未知预测值收集起来准备显示出来
167 preout = np.reshape(preout,[seq_length,nb_predictions,output_dim])
```

前 15 次时间序列用于输入，后 15 次循环来使用模型预测，每次都将输出的最后一个时间序列收集起来，最终得到 15 个时间序列批次的预测结果 preout。

## 6. 画图显示数据

将批次设为 4，随机取 4 个序列片段，每个片段的 15 个序列预测以图像形式显示出来。

### 代码9-30 基本Seq2Seq（续）

```python
167 for j in range(nb_predictions):
168     plt.figure(figsize=(12, 3))
169
170     for k in range(output_dim):
171         past = X[:, j, k]
172         expected = Y[seq_length-1:, j, k] #对应预测值的打印
173
174     pred = preout[:, j, k]
175
176     label1 = "past" if k == 0 else "_nolegend_"
177     label2 = "future" if k == 0 else "_nolegend_"
178     label3 = "Pred" if k == 0 else "_nolegend_"
179     plt.plot(range(len(past)), past, "o--b", label=label1)
180     plt.plot(range(len(past), len(expected) + len(past)),
181     expected, "x--b", label=label2)
182     plt.plot(range(len(past), len(pred) + len(past)),
183     pred, "o--y", label=label3)
184
185     plt.legend(loc='best')
186     plt.title("Predictions vs. future")
187     plt.show()
```

为了跟真实的序列值比较，这里将真实的序列值也从 15 个序列开始打印出来，index=14 的值即为预测的第一个值。运行上面的代码，结果如图 9-29 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_324_0.png)

### a) 序列片段 1

![](img/d841d305683d65f55b29f4e3f25e532c_324_1.png)

### b) 序列片段 2

![](img/d841d305683d65f55b29f4e3f25e532c_324_2.png)

### c) 序列片段 3

## 图 9-29 基于 Seq2Seq 实例结果

![](img/d841d305683d65f55b29f4e3f25e532c_325_0.png)

### d) 序列片段 4

## 图 9-29 基于 Seq2Seq 实例结果（续）

可以看到，生成的预测数据与真实数据相差并不大。

> 注意：这里使用了 feed_dict 的 update 方法来处理复杂的 feed_dict 的情况，通过 update 可以在原有的 feed_dict 中加入新的 feed 数据，将一行语句变为多行输入。

## 9.8.4 实例 73：预测当天的股票价格

既然前面我们用预测股票来打比方，那么这里就演示一个预测股票的例子。直接修改实例 72 中的数据源即可。

### 实例描述

使用 Seq2Seq 模式对某个股票数据的训练学习，拟合特征，从而达到可以预测第二天股票价格的效果。

- 1. 准备数据

需要准备一个股票的数据，本例中的格式是 CSV，也可使用本书的配套例子中的数据“600000.csv”（笔者只是随意爬取了 A 股中的第一个股票，没有其他特殊意义），本书配套代码中提供了一个爬虫代码文件，见代码“9-31 STOCKDATA.py”文件。

- 2. 导入股票数据

直接在“9-30: 基本 seq2seq.py”文件基础上修改代码，添加载入股票函数 loadstock，里面使用了 pandas，所以要将该库导入进去。实例中将 close 收盘价格载入内存用于做样本生成。当然读者也可以自行修改字段，可以将开盘价、最高价格和最低价格等都载入内存作为样本数据，只需将对应的列名放入 predictor_names 数组中即可。

### 代码9-32 seq2seqstock

```python
01 import pandas as pd
02 pd.options.mode.chained_assignment = None # default='warn'
03 def loadstock(window_size):
04     names = ['date',
05     'code',
06     'name',
07     'Close',
08     'top_price',
09     'low_price',
10     'opening_price',
11     'bef_price',
12     'floor_price',
13     'floor',
14     'exchange',
15     'Volume',
16     'amount',
17     '总市值',
18     '流通市值']
19 data = pd.read_csv('600000.csv', names=names, header=None, encoding
20 = "gbk")
21
22 predictor_names = ["Close"]
23 training_features = np.asarray(data[predictor_names], dtype =
24 "float32")
25 kept_values = training_features[1000:]
26
27 X = []
28 Y = []
29 for i in range(len(kept_values) - window_size * 2):
30     # x 为前 window_size 个序列, y 为后 window_size 一个序列
31     X.append(kept_values[i:i + window_size])
32     Y.append(kept_values[i + window_size:i + window_size * 2])
33
34 X = np.reshape(X, [-1, window_size, len(predictor_names)])
35 Y = np.reshape(Y, [-1, window_size, len(predictor_names)])
36 print(np.shape(X))
37
38 return X, Y
```

### 3. 生成样本

直接修改代码中生成样本的函数 generate_data，和其对应的内部调用的 do_generate_x_y 函数，代码如下。

### 代码9-32 seq2seqstock（续）

```python
def generate_data(isTrain, batch_size):
    # 用前 40 个样本来预测后 40 个样本

    seq_length = 40
    seq_length_test = 80

    global Y_train
    global X_train
    global X_test
    global Y_test
    # 载入内存
    if len(Y_train) == 0:
    X, Y= loadstock( window_size=seq_length)

    # Split 80-20:
    X_train = X[:int(len(X) * 0.8)]
    Y_train = Y[:int(len(Y) * 0.8)]

    if len(Y_test) == 0:
        X, Y = loadstock( window_size=seq_length_test)

    # Split 80-20:
    X_test = X[int(len(X) * 0.8):]
    Y_test = Y[int(len(Y) * 0.8):]

    if isTrain:
        return do_generate_x_y(X_train, Y_train, batch_size)
    else:
        return do_generate_x_y(X_test, Y_test, batch_size)


def do_generate_x_y(X, Y, batch_size):
    assert X.shape == Y.shape, (X.shape, Y.shape)
    idxes = np.random.randint(X.shape[0], size=batch_size)
    X_out = np.array(X[idxes]).transpose((1, 0, 2))
    Y_out = np.array(Y[idxes]).transpose((1, 0, 2))
    return X_out, Y_out
```

### 4. 运行程序查看效果

由于股票数据没有固定的规则而言，并且数据量又较大，所以加大 batch 到 100，加大迭代次数到 100000，代码片段如下。

代码9-32 seq2seqstock (续)

```python
......
seq_length = sample_now.shape[0]
batch_size = 100

output_dim = input_dim = sample_now.shape[-1]
hidden_dim = 12
layers_num = 2

# 学习率
learning_rate =0.04
nb_iters = 100000
lambda_12_reg = 0.003 # L2 regularization of weights - avoids overfitting
......
```

其他地方均不用变，直接运行代码即可，输出如图 9-30 所示。
