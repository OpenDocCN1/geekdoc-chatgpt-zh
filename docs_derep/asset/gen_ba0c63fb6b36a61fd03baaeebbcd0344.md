# 短视频推荐多目标融合：BatchRL-MTF 离线训练与在线探索深度解析

## 离线训练流程概览

在离线训练环节，数据处理模块会从数据库拉取近期用户画像与多维反馈日志，将其转化为结构化序列样本。每条样本 $\tau_i$ 记录了用户从进入到离开的完整会话轨迹，其中 $s_i$ 代表状态特征，$a_i$ 代表融合动作参数。经过 BatchRL-MTF 模型的充分训练后，最终得到的模型参数会被推送至线上服务集群。

接下来，我们将先夯实背景知识，再深入剖析模型架构细节，最后复盘在线探索策略与实验结论。

## 相关背景知识：会话建模与多目标融合范式

[PRE0]

图6-13 用户与推荐系统的会话级交互闭环

在短视频流场景中，用户发起请求，系统返回有序候选列表，客户端回传点赞、评论、分享、关注等行为日志。这一“请求-下发-反馈”循环持续至用户退出，天然契合强化学习（RL）范式：推荐系统作为智能体，排序策略即动作，用户行为映射为即时奖励，用户画像构成状态空间。整个会话周期实质上是一个马尔可夫决策过程（MDP），由五元组 $(S, A, P, R, \gamma)$ 定义：

- **状态空间 S**：涵盖用户基础属性（年龄、性别、地域等）与历史交互行为序列。
- **动作空间 A**：在多目标融合语境下，动作具体化为融合参数 $a$，它直接决定最终物品排序顺序。
- **奖励函数 R**：智能体依据状态 $s_t$ 施加动作 $a_t$ 下发列表，用户消费行为即刻转化为即时奖励。
- **状态转移 P**：概率分布 $p(s_{t+1} | s_t, a_t)$ 描述状态迁移规律。
- **折扣因子 $\gamma$**：取值区间 $[0,1]$，平衡即时与长远收益。

当前主流精排架构分为两级：**多任务学习（MTL）** 预测完播率、点赞概率等多行为得分；**多任务融合（MTF）** 将这些得分聚合为最终排序分。融合函数 $f(o \mid \alpha)$ 采用对数加权求和形式（公式 6-21）：
$$f(o \mid \alpha) = \sum_{i}^{n} \alpha_i \log(o_i + \beta)$$
其中 $\alpha_i$ 为第 $i$ 个行为权重，$o_i$ 为预测得分，$\beta$ 为先验偏置常数。MTF 的核心任务即为每个用户寻找最优权重向量 $\alpha$。

## 核心模型设计：BatchRL-MTF 架构深度拆解

BatchRL-MTF 的设计核心聚焦于奖励函数构建，以及 Actor 与 Critic 两大子网络的协同优化。

### (1) 奖励函数：统筹粘性与活跃的双维度度量

精准量化会话级满意度是离线策略学习成败的关键。短视频场景下，用户满意度由播放时长、完播率、交互动作、退出倾向等多元信号共同表征。BatchRL-MTF 构建的即时奖励 $r(s,a)$ 融合了**用户粘性**与**活跃度**两大维度（公式 6-22）：
$$r(s,a) = \sum_{j=1}^k \lambda_j \sum_{i=1}^{m_j} w_{ij} v_{ij}$$
$j$ 索引行为类别，$\lambda_j$ 为类别权重，$m_j$ 为该类别包含的具体行为数，$w_{ij}$ 与 $v_{ij}$ 分别为行为权重与数值。权重系数的校准结合了基于 APP 使用时长的因果推断分析结果。

### (2) Actor 网络：CVAE 对抗外推误差 + 扰动网络增强探索多样性

Actor 由**动作生成网络 $G_\theta$** 与**动作扰动网络 $P_\omega$** 组成。$G_\theta = \{E_{\theta1}, D_{\theta2}\}$ 采用条件变分自编码器（CVAE）架构，旨在生成与训练集 $B$ 分布一致的候选动作，从根本上**缓释离线 RL 的外推误差难题**，显著提升策略稳健性。

- **编码器 $E_{\theta1}(z \mid s, a)$** 将 $(s, a)$ 映射为潜在向量 $z$，约束其服从 $\mathcal{N}(\mu, \sigma^2)$ 并通过 KL 散度逼近标准正态分布 $\mathcal{N}(0,1)$。
- **解码器 $D_{\theta2}(a \mid s, z)$** 基于采样的 $z \sim \mathcal{N}(\mu, \sigma^2)$ 与状态 $s$ 重构动作。

优化目标包含重构损失与 KL 正则项（公式 6-23）：
$$\theta \leftarrow \arg\min_{\theta} \sum_{(s,a) \in B} (a - C_{\theta}(s))^2 + \text{KL}(E_{\theta}(z \mid s, a) \parallel \mathcal{N}(0,1))$$
其中两正态分布 KL 散度显式展开为（公式 6-24）：
$$\text{KL}(\dots) = -1.0 + 2 \log \frac{1}{\sigma} + \mu^2 + \sigma^2$$

为进一步拓宽动作覆盖面，扰动网络 $P_\omega(s, a, \rho)$ 在 CVAE 采样输出 $\{\hat{a}_i\}$ 基础上叠加扰动 $\xi_i$，最终通过 Critic 评估选取 Q 值最大者（公式 6-25）：
$$\pi(s) = \arg\max_{\hat{a}_i + \xi_i} Q(s, \hat{a}_i + \xi_i)$$
扰动网络参数沿用 DDPG 更新范式，最大化扰动后动作的 Q 值期望（公式 6-26）：
$$\omega \leftarrow \arg\max_{\omega} \sum_{s \in B} Q(s, \hat{a} + P_{\omega}(s, \hat{a}, \rho)), \quad \hat{a} \in C_{\theta}(s)$$

[PRE1]

图6-14 BatchRL-MTF 动作选择策略示意

### (3) Critic 网络：双重 Q 机制抑制过估计

Critic 承担状态-动作价值函数 $Q_\phi(s, a)$ 评估职责。采用双网络结构：两个当前网络 $Q_a, Q_b$ 与对应的两个目标网络 $Q'_a, Q'_b$。损失函数基于时序差分误差（公式 6-27）：
$$\phi_j \leftarrow \arg\min_{\phi_j} \sum_{(s,a) \in B} [y - Q_{\phi_j}(s, a)]^2, \quad j \in \{1, 2\}$$
学习目标 $y$ 融合即时奖励与目标网络双 Q 取最小机制（公式 6-28），有效抑制过估计偏差：
$$y = r + \gamma \max_{a'} \left[ \min_{j=1,2} Q_{\phi_j}(s', a') \right]$$
目标动作 $a'$ 由目标 Actor 网络采样生成（公式 6-29）：
$$a' \in \{ \hat{a}_i + P_{\omega'}(s', \hat{a}_i, \rho) \mid \hat{a}_i \in G_{\theta'}(s') \}_{i=1}^n$$
$n$ 为采样动作数，$s'$ 为下一时刻状态。

### 离线训练算法流程

算法 6-2 给出了完整迭代流程。利用 Replay Buffer 存储历史四元组 $(s, a, r, s')$，每轮采样 Mini-Batch，交替更新 $G_\theta$、$P_\omega$、$Q_\phi$；目标网络每 $L$ 轮软更新（参数 $\eta$），平衡收敛速度与稳定性。

**算法 6-2 BatchRL-MTF 离线训练伪代码**

**输入**：数据集 $\mathcal{B}$，Batch Size $M$，采样数 $n$，扰动范围 $\rho$，软更新率 $\eta$，折扣 $\gamma$，延迟步长 $L$，总轮数 $EP$

1. 随机初始化 $G_\theta = \{E_{\theta_1}, D_{\theta_2}\}$，$P_\omega$，$Q_\phi = \{Q_{\phi_1}, Q_{\phi_2}\}$
2. 初始化目标网络 $\omega' \leftarrow \omega, \phi'_1 \leftarrow \phi_1, \phi'_2 \leftarrow \phi_2$
3. **for** $ep = 1$ **to** $EP$ **do**
4.   从 $\mathcal{B}$ 采样 $M$ 条 $(s, a, r, s')$
5.   按公式 (6-23) 更新 $G_\theta$
6.   采样 $n$ 个动作 $\{\hat{a}_i \sim G_\theta(s')\}_{i=1}^n$
7.   构造扰动动作 $\{\hat{a}_i + P_{\omega'}(s', \hat{a}_i, \rho)\}_{i=1}^n$
8.   按公式 (6-26) 更新 $P_\omega$
9.   按公式 (6-27) 更新 $Q_\phi$
10.  **if** $ep \bmod L = 0$ **then**
11.    $\omega' \leftarrow \eta \omega + (1-\eta)\omega'$
12.    $\phi'_i \leftarrow \eta \phi_i + (1-\eta)\phi'_i, \quad i=1,2$
13.  **end for**

## 在线探索策略：收益与体验的动态平衡

离线阶段虽通过 CVAE 与扰动机制缓解了分布偏移，但对最优动作的主动探索能力仍受限。上线阶段必须引入显式探索策略，发掘高潜力动作空间。

- **随机探索**：按高斯分布纯随机采样动作，覆盖面广、易跳出局部最优，但动作质量无保障，用户体验损耗较大。
- **动作噪声探索**：在模型最优动作 $\pi^*_i(s)$ 基础上注入高斯噪声 $\varepsilon \sim \mathcal{N}(0, 0.1)$（公式 6-30），在利用确定性策略与探索未知区域间取得折中：
  $$\pi_b(s) = \pi^*_i(s) + \varepsilon$$

工程落地常采用**分流混合策略**（见下图）：小比例流量分别跑随机与噪声探索，整体体验损失可控。另有团队尝试引入用户模拟器离线生成反馈，虽避免在线损耗，但模拟偏差导致数据保真度不足，限制了策略上限。

[PRE2]

图6-15 在线混合探索策略架构

## 实验复盘：效果对比与外推误差实证验证

强化学习搜参实验得出核心结论：

1. **整体效果领先**：对比贝叶斯优化（BO）与进化策略（ES），BatchRL 与 CQL+SAC 在 APP 停留时长与正向互动率上均实现显著提升，验证了 RL 对长期价值建模的优势。
2. **外推误差有效受控**：图 6-16 展示了归一化至 $[-1,1]$ 后的动作分布对比。真实数据分布呈近似正态；TD3 输出动作严重越界分布极端，导致 Q 值预估严重失真，外推误差显著；相比之下，CQL+SAC 与 BatchRL-MTF 输出分布紧贴数据固有分布，**成功规避了外推陷阱**。

[PRE3]

图6-16 多算法在四类行为上的动作分布对比（详见彩插）

## 章节小结：多目标融合演进路线图与未来展望

随着信息流形态迭代，用户交互日益丰富，业务目标从单指标优化转向“宏观指标兜底 + 多元交互全面开花”，倒逼推荐算法进化。多任务学习（MTL）已成精排标配，而多目标融合（MTF）作为连接微观预测与宏观目标的关键桥梁，其建模质量直接决定业务上限。

回溯融合算法演进脉络：
- **启发式搜索**（随机/网格）适用于目标少、离线评估的早期阶段；
- **贝叶斯优化** 打通离线在线一致性，大幅提效；
- **进化策略** 引入个性化融合，兼具简洁与稳健；
- **强化学习** 聚焦长期累积奖励，理论上限最高，但工程落地中**训练稳定性仍是核心短板**，亟待后续攻关突破。

未来，如何在保证训练收敛稳健性的前提下，充分释放 RL 长期建模潜力，将是工业界推荐系统持续演进的核心命题。

## 参考文献

[1] PEDREGOSA F, 等. Scikit-learn: machine learning in Python [J]. JMLR, 2011.
[2] MOCKUS J. Bayesian approach to global optimization [M]. Springer, 2012.
[3] SEEGER M. Gaussian processes for machine learning [J]. IJNS, 2004.
[4] HO T K. Random decision forests [C]. ICDAR, 1995.
[5] AUER P, 等. Finite-time analysis of the multiarmed bandit problem [J]. ML, 2002.
[6] VIANA F, HAFTKA R. Surrogate-based optimization [C]. AIAA, 2010.
[7] JONES D R, 等. Efficient global optimization of expensive black-box functions [J]. JGO, 1998.
[8] RECHENBERG I. Evolutionsstrategien [M]. Springer, 1978.
[9] SALIMANS T, 等. Evolution strategies as a scalable alternative to reinforcement learning [EB/OL]. arXiv:1703.03864, 2017.
[10] SUTTON R S, BARTO A G. Reinforcement learning: An introduction [M]. MIT Press, 2018.