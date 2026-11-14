# 范例 2-2 实现 AND 操作的感知机（ Perceptron.py ）

```
01 # 定义激活函数
02 def func_activator(input_value):
03     return 1.0 if input_value >= 0.0 else 0.0
04 def get_training_dataset():
05     # 构建训练数据
06     dataset = [[-1, 1, 1, 1], [-1, 0, 0, 0], [-1, 1, 0, 0], [-1, 0, 1, 0]]
07     # 期望的输出列表，注意要与输入——对应
08     # [-1,1,1] -> 1, [-1, 0,0] -> 0, [-1, 1,0] -> 0, [-1, 0,1] -> 0
09     return dataset
10 def train_and_perceptron():
11     p = Perceptron(3, func_activator)
12     # 获取训练数据
13     dataset = get_training_dataset()
14     p.train(dataset, 10, 0.1)    #指定迭代次数：10 轮，学习率设置为 0.1
15     #返回训练好的感知机
16     return p
17 if __name__ == '__main__':
18     # 训练 and 感知机
19     and_perceptron = train_and_perceptron()
20     # 打印训练获得的权值
21     print (and_perceptron)
22     # 测试
23     print ('1 and 1 = %d' % and_perceptron.predict([-1, 1, 1]))
24     print ('0 and 0 = %d' % and_perceptron.predict([-1, 0, 0]))
25     print ('1 and 0 = %d' % and_perceptron.predict([-1, 1, 0]))
26     print ('0 and 1 = %d' % and_perceptron.predict([-1, 0, 1]))
```

# 运行结果

final weights
    w0 = 0.20
    w1 = 0.10
    w2 = 0.20
1 and 1 = 1
0 and 0 = 0
1 and 0 = 0
0 and 1 = 0

# 代码分析

需要说明的是，范例 2-2 不能单独运行，它需要与范例 2-1 配合起来才能运行。下面简单介绍一下部分代码的功能。

对于监督学习，为了训练一个神经网络模型，通常要提供一些训练样本来训练神经网络。每个训练样本既包括输入特征（如图片的灰度值），