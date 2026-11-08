[PRE0]

完整实现可扫码获取。

【实验7-3】解码器 Transformer + 蒙特卡洛策略梯度实现迷宫深度强化学习

【实验7-3参考程序】

[PRE1]

实验7-3程序实现

[PRE2]

# 模型对历史轨迹与当前步各动作的概率预测

return a

...

# 变量预置

cost_saved = []

steps_saved = np.zeros(num_runs)

# 每个 episodes 的外层迭代

for run in range(num_runs):

    # 重置本轮环境

    maze_init()

    gameover = False

    action_observed_sequence = torch.zeros(0, dtype=torch.long, device=device)

    # 输入序列初始化

    actions = torch.zeros(0, dtype=torch.int, device=device)

    # 奖励记录初始化

    rewards = []

    # 迷宫单步内层循环

    while not gameover:

        # 读取当前状态观测

        o_t = torch.tensor(maze_get_observation(), device=device)

        # 将观测追加到历史序列

        action_observed_sequence = torch.cat((action_observed_sequence, o_t.unsqueeze(dim=0)))

        with torch.inference_mode():

            prob = model(action_observed_sequence.unsqueeze(dim=0))

            # 执行前向计算

        a_t = torch.multinomial(input=prob[0, -1, :], num_samples=1)

        # 基于输出概率采样动作

        actions = torch.cat((actions, a_t), dim=0)

        action_observed_sequence = torch.cat((action_observed_sequence, a_t))

        # 把本次动作加入序列

        # 前进一格迷宫