# 强化学习实战：从折扣回报到 PPO 骨架

强化学习的核心是让智能体学会“现在的选择对未来有多大价值”。为了把未来奖励压缩成一个可比较的数值，我们引入了折扣累积回报的概念。下面的公式就是它的数学表达。

[PRE0]

$G_t = \sum_{k=0}^{\infty} r^k R_{t+k+1}$ where $r \in [0,1)$

用更直观的展开写法看，当前时刻的回报是由后续每一步奖励按衰减系数累加而成，越远的奖励权重越小。

[PRE1]

$R_{t+1} + \gamma R_{t+2} + \gamma^2 R_{t+3} \cdots$

在实践里，先把环境搭起来是最直接的。用 Gym 就可以快速起步，LunarLander-v2 是个经典的二维着陆任务，状态是8维向量，动作只有4种，适合做原型验证。

[PRE2]

pip install gym

pip install box2d box2d-kengz --user

环境准备好后，先用一个随机策略跑一遍，感受一下状态与奖励的变化规律。下面的最小可运行示例展示了如何重置环境、随机采样动作、一步步交互并打印反馈。

[PRE3]

python

import gym

import time

# 环境初始化

env = gym.make('LunarLander-v2', render_mode='human')

if True:

    state = env.reset()

    while True:

        # 渲染画面

        # env.render()

        # 从动作空间随机获取一个动作

        action = env.action_space.sample()

        # Agent与环境进行一步交互

        observation, reward, done, _, _ = env.step(action)

        print('state = {0}; reward = {1}'.format(state, reward))

        # 判断当前episode是否完成

        if done:

            print('游戏结束')

            break

    time.sleep(0.01)

env.close()# 环境结束

随机探索时，状态数值基本保持在同一范围内，而奖励会随着飞行姿态起伏，最后一步往往会因坠毁给出一个很大的负分。典型输出片段如下。

[PRE4]

state = (array([ 0.00245399, 1.4199276 , 0.24854021, 0.40032664, -0.0028367 ,

   -0.05629814, 0. , 0. ], dtype=float32), {}); reward = 3.693495331315576

state = (array([ 0.00245399, 1.4199276 , 0.24854021, 0.40032664, -0.0028367 ,

   -0.05629814, 0. , 0. ], dtype=float32), {}); reward = 2.9992074375700044

state = (array([ 0.00245399, 1.4199276 , 0.24854021, 0.40032664, -0.0028367 ,

   -0.05629814, 0. , 0. ], dtype=float32), {}); reward = 2.209163362966821

state = (array([ 0.00245399, 1.4199276 , 0.24854021, 0.40032664, -0.0028367 ,

   -0.05629814, 0. , 0. ], dtype=float32), {}); reward = -100

当有了交互基础，就可以搭建 PPO 的骨架。先定义经验缓存、策略网络和价值网络，再封装训练逻辑。

[PRE5]

python

import matplotlib.pyplot as plt

import torch

from torch.distributions import Categorical

import gym

import time

import numpy as np

import random

from IPython import display

class Memory:

    def __init__(self):

        """初始化"""

        self.actions = []  # 行动(共4种)

        self.states = []   # 状态, 由8个数字组成

        self.logprobs = [] # 概率

        self.rewards = []  # 奖励

        self.is_dones = [] # 游戏是否结束: is_terminals?

    def clear_memory(self):

        del self.actions[:]

        del self.states[:]

        del self.logprobs[:]

        del self.rewards[:]

        del self.is_dones[:]

class Action(torch.nn.Module):

    def __init__(self, state_dim=8, action_dim=4):

        super().__init__()

        # actor

        self.action_layer = torch.nn.Sequential(

            torch.nn.Linear(state_dim, 128),

            torch.nn.ReLU(),

            torch.nn.Linear(128, 64),

            torch.nn.ReLU(),

            torch.nn.Linear(64, action_dim),

            torch.nn.Softmax(dim=-1)

        )

    def forward(self, state):

        action_logits = self.action_layer(state)  # 计算4个方向的概率

        return action_logits

class Value(torch.nn.Module):

    def __init__(self, state_dim=8):

        super().__init__()

        # value

        self.value_layer = torch.nn.Sequential(

            torch.nn.Linear(state_dim, 128),

            torch.nn.ReLU(),

            torch.nn.Linear(128, 64),

            torch.nn.ReLU(),

            torch.nn.Linear(64, 1)

        )

    def forward(self, state):

        state_value = self.value_layer(state)

在价值网络返回后，进入 PPO Agent 的实现。evaluate 负责给出动作对数概率、状态价值和熵，update 则使用从后往前的折扣方式把即时奖励累加为回报，这是 PPO 训练的关键预处理步骤。

[PRE6]

python

return state_value

class PPOAgent:

    def __init__(self, state_dim, action_dim, n_latent_var, lr, betas, gamma, K_epochs, eps_clip):

        self.lr = lr  # 学习率

        self.betas = betas  # betas

        self.gamma = gamma  # gamma

        self.eps_clip = eps_clip  # 裁剪，限制值范围

        self.K_epochs = K_epochs  # 获取的每批次的数据作为训练使用的次数

        # action

        self.action_layer = Action()

        # critic

        self.value_layer = Value()

        self.optimizer = torch.optim.Adam([{"params": self.action_layer.parameters()}, {"params": self.value_layer.parameters()}], lr=lr, betas=betas)

        # 损失函数

        self.MseLoss = torch.nn.MSELoss()

    def evaluate(self, state, action):

        action_probs = self.action_layer(state)  # 这里输出的结果是4类别的[-1,4]

        dist = Categorical(action_probs)  # 转换成类别分布

        # 计算概率密度，log(概率)

        action_logprobs = dist.log_prob(action)

        # 计算信息熵

        dist_entropy = dist.entropy()

        # 评判，对当前的状态进行评判

        state_value = self.value_layer(state)

        # 返回行动概率密度、评判值、行动概率熵

        return action_logprobs, torch.squeeze(state_value), dist_entropy

    def update(self, memory):

        # 预测状态回报

        rewards = []

        discounted_reward = 0  # discounted = 不重要

        # 这里是不是可以这样理解，当前步骤是决定未来的步骤，而模型需要根据当前步骤对未来的最终结果进行修正，如果遵循了当前步骤，就可以看到未来的结果如何

        # 这样未来的结果会很差，所以模型需要远离会造成坏的结果的步骤

        # 所以就反过来计算

        # print(len(self.memory.rewards),len(self.memory.is_dones))这里就是做成批次，1200批次数据做一次

        for reward, is_done in zip(reversed(memory.rewards), reversed(memory.is_dones)):

            # 回合结束

            if is_done:

                discounted_reward = 0

            # 更新削减奖励(当前状态奖励 + 0.99*上一状态奖励)

            discounted_reward = reward + (self.gamma * discounted_reward)

            # 首部插入