

## Finance 类定义与示例

```
In [54]: class Finance:
             url = 'http://hilpisch.com/aiif_eikon_eod_data.csv'
             def __init__(self, symbol, features):
                 self.symbol = symbol
                 self.features = features
                 self.observation_space = observation_space(4)
                 self.osn = self.observation_space.shape[0]
                 self.action_space = action_space(2)
                 self.min_accuracy = 0.475  ➊
                 self._get_data()
                 self._prepare_data()
             def _get_data(self):
                 self.raw = pd.read_csv(self.url, index_col=0,
                                        parse_dates=True).dropna()
             def _prepare_data(self):
                 self.data = pd.DataFrame(self.raw[self.symbol])
                 self.data['r'] = np.log(self.data / self.data.shift(1))
                 self.data.dropna(inplace=True)
                 self.data = (self.data - self.data.mean()) / self.data.std()
                 self.data['d'] = np.where(self.data['r'] > 0, 1, 0)
             def _get_state(self):
                 return self.data[self.features].iloc[
                     self.bar - self.osn:self.bar].values  ➋
             def seed(self, seed=None):
                 pass
             def reset(self):  ➌
                 self.treward = 0
                 self.accuracy = 0
                 self.bar = self.osn
                 state = self.data[self.features].iloc[
                     self.bar - self.osn:self.bar]
                 return state.values
             def step(self, action):
                 correct = action == self.data['d'].iloc[self.bar]  ➍
                 reward = 1 if correct else 0  ➎
                 self.treward += reward  ➏
                 self.bar += 1  ➐
                 self.accuracy = self.treward / (self.bar - self.osn)  ➑
                 if self.bar >= len(self.data):  ➒
                     done = True
                 elif reward == 1:  ➓
                     done = False
                 elif (self.accuracy < self.min_accuracy and
                       self.bar > self.osn + 10):  ⓫
                     done = True
                 else:  ⓬
                     done = False
                 state = self._get_state()
                 info = {}
                 return state, reward, done, info
```

➊ 定义所需的最低准确率。
➋ 选择用于定义金融市场状态的数据。
➌ 将环境重置为其初始值。
❹ 检查智能体是否选择了正确的动作（成功交易）。
➎ 定义智能体收到的奖励。
➏ 将奖励添加到总奖励中。
➐ 使环境向前运行一步。
➑ 计算给定所有步骤（交易）的成功的动作（交易）的准确率。
➒ 如果智能体到达数据集的末尾，则成功。
➓ 如果智能体采取了正确的动作，那么它可以继续前进。
⓫ 如果在一些初始步骤之后，准确率下降到最低水平以下，则该回合结束（失败）。
⓬ 对于其余情况，智能体可以继续前进。

`Finance` 类的实例表现得很像 `OpenAI Gym` 的环境。特别是，在这种基本情况下，该实例表现得与 `CartPole` 环境完全一样。

```
In [55]: env = Finance('EUR=', 'EUR=')  ➊

In [56]: env.reset()
Out[56]: array([1.819 , 1.8579, 1.7749, 1.8579])

In [57]: a = env.action_space.sample()
         a
Out[57]: 0

In [58]: env.step(a)
Out[58]: (array([1.8579, 1.7749, 1.8579, 1.947 ]), 0, False, {})
```

➊ 指定用于定义代表状态数据的交易标的代号和特征类型（交易标的代号或对数收益率）。

为 `CartPole` 游戏开发的 `DQLAgent` 能否学习在金融市场中进行交易？是的，它能，如下面的代码所示。然而，尽管智能体在训练回合中提高了其交易技能（平均而言），但结果并不“惊艳”（参见图 9-3）。

```
In [59]: set_seeds(100)
         agent = DQLAgent(gamma=0.5, opt=RMSprop)

In [60]: episodes = 1000

In [61]: agent.learn(episodes)
         episode: 1000/1000 | treward: 2511 | av: 1012.7 | max: 2511
In [62]: agent.test(3)
         episode:    3/3 | treward: 2511
Out[62]: [2511, 2511, 2511]

In [63]: plt.figure(figsize=(10, 6))
         x = range(len(agent.averages))
         y = np.polyval(np.polyfit(x, agent.averages, deg=3), x)
         plt.plot(agent.averages, label='moving average')
         plt.plot(x, y, 'r--', label='regression')
         plt.xlabel('episodes')
         plt.ylabel('total reward')
         plt.legend();
```

![{%}](Images/image00651.jpeg)

图 9-3：运行于 `Finance` 环境的 `DQLAgent` 的平均总奖励

> ![](Images/image00322.jpeg)　通用 RL 智能体
> 
> 本节为金融市场环境提供了一个类，该类模拟了 `OpenAI Gym` 环境的 `API`。它还可以将 DQL 智能体应用于新的金融市场环境，无须对智能体本身进行任何更改。尽管智能体在这种新环境中的表现可能并不“惊艳”，但它说明了本章介绍的强化学习方法是相当通用的。RL 智能体通常可以从与它们交互的不同环境中学习，这在一定程度上解释了为什么第 2 章所述的 DeepMind 的 `AlphaZero` 不仅能够掌握围棋，还能够掌握国际象棋和将棋。

## 9.7 更好的金融沙箱

上一节的目的是开发一个简单的类，该类允许在金融市场环境中进行强化学习。本节的主要目标是复制 `OpenAI Gym` 环境的 `API`。然而，没有必要将这样的环境限制为以单一类型的特征来描述金融市场的状态，也没有必要只使用 4 个滞后项。本节介绍了改进的 `Finance` 类，该类允许使用多个特征和灵活长度的滞后项，以及为所用基本数据集指定起点和终点。这将允许将数据集的一部分用于学习，另一部分用于验证或测试。下面提供的 `Python` 代码也允许使用杠杆，当日内数据被认为具有相对较小的绝对收益率时，这可能会有所帮助。

