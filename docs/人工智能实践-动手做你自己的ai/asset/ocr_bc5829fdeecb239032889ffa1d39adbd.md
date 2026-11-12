```
"""
根据计算奖励的方式来记录相应信息
"""
return [self.eng.get_lane_waiting_vehicle_count(),
self.eng.get_lane_vehicles()]
```

# 七、训练 Q 值网络

在智能体接收到环境反馈的奖励后，训练便开始了。

智能体训练Q值网络的过程如下：首先，智能体会将同环境交互的信息存储在经验池中。
然后在训练时，从经验池中随机抽取若干状态转移，通过 Deep Q-Network 的更新公式，用目标网络为状态转移计算目标Q值，并把目标Q值作为Q值网络的训练标签。其间，要注意维护目标网络与Q值网络的同步。

```
def train_network(self):
    """
    采样经验，计算目标Q值，训练Q值网络
    """
    # 当经验池容量过小时不进行训练
    if (len(self.memory) < self.training_start):
        return

    # 随机采样经验池中的历史交互数据(s, a, r, s')
    sampled_memory = self._sample_memory()

    # 计算目标Q值，作为q值网络的训练标签
    # 对采样的数据进行格式转换。将一批训练数据(s, a)堆叠成X，并让标签Y与之对应，整理为训练数据X与训练标签Y的格式
    X, Y = self.get_training_sample(sampled_memory)

    # 增加目标网络的同步计数器
    self.q_bar_outdated += 1
    # 训练Q值网络
    self._fit_network(X, Y)
```

这一部分的关键是计算目标Q值，生成训练标签的过程，主要的难点在于数据格式的转换。

神经网络的一次训练是对一批训练数据同时做计算与更新。Q值网络是用 Keras 实现的，