# CartPole GRPO 训练与可视化速览

这套实现围绕一个极简策略网络展开，用来在并行 CartPole 环境里做 GRPO 式训练，并配了一份可以直接跑的测试脚本。

**策略网络**
网络只有两层全连接：第一层把状态映射到 128 维并用 ReLU 激活，第二层输出未归一化的动作分数，最后经 softmax 转成动作概率分布。训练时与测试时结构必须完全一致。

**并行轨迹采集**
采集函数接收向量化环境、策略网络、最大步长和设备。先用随机种子批量重置环境，随后在最多 500 步的循环里：
把当前状态转成张量求概率，用分类分布采样动作并记录对数概率；执行动作后合并 terminated 与 truncated 得到完成标记；把状态、动作、对数概率入列；对已结束的环境把本步奖励清零，并加上水平位置惩罚 -abs(next_states[:,0])；累加每环境奖励。
所有环境都结束或达到步长上限即退出。收尾时把奖励按步长归一化，状态、动作、对数概率重排成 [group_size, T, ...] 的形状，返回打包好的轨迹字典和归一化后的 episode 奖励。

**优势计算与更新**
优势是把归一化奖励做标准化：减去均值除以标准差，外加 1e-8 防除零。GRPO 更新先把优势扩成列向量，再解包轨迹中的状态、历史对数概率和已选动作，为 PPO 截断更新做准备，超参数里迭代次数 20，截断阈值 0.2。

**测试与可视化**
测试脚本定义了与训练完全一致的 PolicyNet，提供模型加载、单回合执行、GIF 保存和命令行入口。支持指定模型路径，默认 ./grpo_cartpole_policy_update_final.pth；可控制测试回合数，默认 5；渲染模式可选 human 窗口显示或 rgb_array 生成帧；开启保存 GIF 时会把表现最好的那条轨迹导出为 cartpole_demo.gif。运行时用 argmax 选择动作，统计平均、最佳、最差奖励并打印。

[PRE0]python

##### test_cartpole.py

import gym

import torch

import numpy as np

import matplotlib.pyplot as plt

import matplotlib.animation as animation

from argparse import ArgumentParser

##### 定义策略网络（必须与训练代码完全一致）

class PolicyNet(torch.nn.Module):

    def __init__(self, state_dim, action_dim):

        super().__init__()

        self.fc1 = torch.nn.Linear(state_dim, 128)

        self.fc2 = torch.nn.Linear(128, action_dim)

    def forward(self, state):

        x = torch.relu(self.fc1(state))

        return torch.softmax(self.fc2(x), dim=1)

def load_model(model_path, device='cpu'):

    """加载训练好的模型"""

    # 初始化网络结构

    model = PolicyNet(state_dim=4, action_dim=2)

    try:

        # 加载训练权重

        model.load_state_dict(torch.load(model_path, map_location=device))

        model.eval()

        print(f"成功加载模型: {model_path}")

        return model

    except Exception as e:

        print(f"模型加载失败: {str(e)}")

        exit(1)

def run_episode(env, model, max_steps=500, render=True):

    """运行单个测试回合"""

    state, _ = env.reset()

    total_reward = 0

    frames = []

    for step in range(max_steps):

        if render:

            frame = env.render()

            if env.render_mode == 'rgb_array':

                frames.append(frame)

        # 使用模型选择动作

        with torch.no_grad():

            state_tensor = torch.FloatTensor(state).unsqueeze(0)

            action_probs = model(state_tensor)

        action = torch.argmax(action_probs).item()

        # 执行动作

        next_state, reward, terminated, truncated, _ = env.step(action)

        total_reward += reward

        state = next_state

        if terminated or truncated:

            print(f"回合结束, 步数: {step + 1}, 总奖励: {total_reward:.1f}")

            break

    return total_reward, frames

def save_gif(frames, filename, fps=30):

    """保存为GIF动画"""

    plt.figure(figsize=(6, 4))

    plt.axis('off')

    ims = [[plt.imshow(frame, animated=True)] for frame in frames]

    ani = animation.ArtistAnimation(plt.gcf(), ims, interval=50, blit=True)

    ani.save(filename, writer='pillow', fps=fps)

    print(f"动画已保存至: {filename}")

def main():

    # 命令行参数解析

    parser = ArgumentParser(description='CartPole测试程序')

    parser.add_argument('--model', type=str,

                        default='./grpo_cartpole_policy_update_final.pth',

                        help='模型文件路径 (默认: ./grpo_cartpole_policy_update_final.pth)')

    parser.add_argument('--episodes', type=int, default=5,

                        help='测试回合数 (默认: 5)')

    parser.add_argument('--render', type=str, choices=['human', 'rgb_array'],

                        default='human',

                        help='渲染模式: human (窗口显示) 或 rgb_array (生成帧)')

    parser.add_argument('--save_gif', action='store_true',

                        help='保存为GIF动画 (仅在rgb_array模式有效)')

    args = parser.parse_args()

    # 设备设置

    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

    # 创建环境

    try:

        env = gym.make('CartPole-v1', render_mode=args.render)

    except gym.error.Error as e:

        print(f"环境创建失败: {str(e)}")

        print("请确保: 1.已安装最新gym库 2.确认环境名称正确")

        exit(1)

    # 加载模型

    model = load_model(args.model, device)

    # 运行测试

    total_rewards = []

    best_frames = []

    max_reward = 0

    for ep in range(args.episodes):

        print(f"\n=== 第 {ep + 1}/{args.episodes} 测试回合 ===")

        reward, frames = run_episode(env, model)

        total_rewards.append(reward)

        # 记录最佳表现

        if reward > max_reward and args.render == 'rgb_array':

            max_reward = reward

            best_frames = frames

    # 输出统计信息

    print("\n=== 测试结果 ===")

    print(f"平均奖励: {np.mean(total_rewards):.1f} ± {np.std(total_rewards):.1f}")

    print(f"最佳奖励: {max(total_rewards)}")

    print(f"最差奖励: {min(total_rewards)}")

    # 保存最佳表现动画

    if args.save_gif and args.render == 'rgb_array' and len(best_frames) > 0:

        save_gif(best_frames, "cartpole_demo.gif")

    elif args.save_gif and args.render != 'rgb_array':