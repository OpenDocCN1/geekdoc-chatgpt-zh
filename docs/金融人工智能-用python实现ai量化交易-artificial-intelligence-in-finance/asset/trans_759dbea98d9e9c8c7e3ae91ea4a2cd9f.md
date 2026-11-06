
```
In [64]: class Finance:
             url = 'http://hilpisch.com/aiif_eikon_eod_data.csv'
             def __init__(self, symbol, features, window, lags,
                          leverage=1, min_performance=0.85,
                          start=0, end=None, mu=None, std=None):
                 self.symbol = symbol
                 self.features = features  ➊
                 self.n_features = len(features)
                 self.window = window
                 self.lags = lags  ➊
                 self.leverage = leverage  ➋
                 self.min_performance = min_performance  ➌
                 self.start = start
                 self.end = end
                 self.mu = mu
                 self.std = std
                 self.observation_space = observation_space(self.lags)
                 self.action_space = action_space(2)
                 self._get_data()
                 self._prepare_data()
             def _get_data(self):
                 self.raw = pd.read_csv(self.url, index_col=0,
                                        parse_dates=True).dropna()
             def _prepare_data(self):
                 self.data = pd.DataFrame(self.raw[self.symbol])
                 self.data = self.data.iloc[self.start:]
                 self.data['r'] = np.log(self.data / self.data.shift(1))
                 self.data.dropna(inplace=True)
                 self.data['s'] = self.data[self.symbol].rolling(
                                                       self.window).mean()  ➍
                 self.data['m'] = self.data['r'].rolling(self.window).mean()  ➍
                 self.data['v'] = self.data['r'].rolling(self.window).std()  ➍
                 self.data.dropna(inplace=True)
                 if self.mu is None:
                     self.mu = self.data.mean()  ➎
                     self.std = self.data.std()  ➎
                 self.data_ = (self.data - self.mu) / self.std  ➎
                 self.data_['d'] = np.where(self.data['r'] > 0, 1, 0)
                 self.data_['d'] = self.data_['d'].astype(int)
                 if self.end is not None:
                     self.data = self.data.iloc[:self.end - self.start]
                     self.data_ = self.data_.iloc[:self.end - self.start]
             def _get_state(self):
                 return self.data_[self.features].iloc[self.bar -
                                         self.lags:self.bar]
             def seed(self, seed):
                 random.seed(seed)
                 np.random.seed(seed)
             def reset(self):
                 self.treward = 0
                 self.accuracy = 0
                 self.performance = 1
                 self.bar = self.lags
                 state = self.data_[self.features].iloc[self.barself.lags:self.bar]
                 return state.values
             def step(self, action):
                 correct = action == self.data_['d'].iloc[self.bar]
                 ret = self.data['r'].iloc[self.bar] * self.leverage  ➏
                 reward_1 = 1 if correct else 0
                 reward_2 = abs(ret) if correct else -abs(ret)  ➐
                 factor = 1 if correct else -1
                 self.treward += reward_1
                 self.bar += 1
                 self.accuracy = self.treward / (self.bar - self.lags)
                 self.performance *= math.exp(reward_2)  ➑
                 if self.bar >= len(self.data):
                     done = True
                 elif reward_1 == 1:
                     done = False
                 elif (self.performance < self.min_performance and
                       self.bar > self.lags + 5):
                     done = True
                 else:
                     done = False
                 state = self._get_state()
                 info = {}
                 return state.values, reward_1 + reward_2 * 5, done, info
```

➊ 定义状态的特征。
➋ 要使用的滞后项长度。
➌ 所需的最低总收益。
❹ 添加金融特征变量（简单移动平均线、动量、滚动波动率）。
➎ 数据的高斯归一化。
➏ 该步的杠杆收益率。
➐ 该步的基于收益率的奖励。
➑ 该步结束后的总收益。

新的 `Finance` 类为金融市场环境的建模提供了更大的灵活性。以下代码是一个有两个特征和 5 个滞后项的示例。

```
In [65]: env = Finance('EUR=', ['EUR=', 'r'], 10, 5)

In [66]: a = env.action_space.sample()
         a
Out[66]: 0

In [67]: env.reset()
Out[67]: array([[ 1.7721, -1.0214],
                [ 1.5973, -2.4432],
                [ 1.5876, -0.1208],
                [ 1.6292,  0.6083],
                [ 1.6408,  0.1807]])

In [68]: env.step(a)
Out[68]: (array([[ 1.5973, -2.4432],
                 [ 1.5876, -0.1208],
                 [ 1.6292,  0.6083],
                 [ 1.6408,  0.1807],
                 [ 1.5725, -0.9502]]),
          1.0272827803740798,
          False,
          {})
```

> **不同类型的环境和数据**
> 需要注意的是，`CartPole` 环境和 `Finance` 环境的两个版本之间存在根本区别。在 `CartPole` 环境中，没有可用的数据，仅选择具有某种程度随机性的初始状态。给定此状态和智能体采取的操作，可以应用确定性转换来生成新状态（数据）。之所以这是可能的，是因为模拟了遵循物理定律的物理系统。
> 另外，`Finance` 环境是从真实的历史市场数据开始的，并且仅以与 `CartPole` 环境类似的方式（逐步和逐个状态地）将可用数据呈现给智能体。在这种情况下，智能体的行为并没有真正影响环境。相反，环境演化是具有确定性的，智能体只是学习如何在该环境中以最佳方式进行有利可图的交易。
> 从这个意义上说，`Finance` 环境更像是在迷宫中寻找最短路径的问题。在这种情况下，代表迷宫的数据是预先给出的，并且当智能体在迷宫中移动时，它只会看到数据的相关子集（当前状态）。

## 9.8　FQL 智能体

本节依托新的 `Finance` 环境，对简单的 `DQL` 智能体进行了改进，以提升其在金融市场环境下的性能。`FQLAgent` 类能够处理多个特征和灵活长度的滞后项。它还可以将学习环境（`learn_env`）与验证环境（`valid_env`）区分开来，从而使我们能够在训练期间获得智能体的样本外性能的更真实表现。`DQLAgent` 类与 `FQLAgent` 类的类和 `RL/QL` 学习方法拥有相同的基本结构。
