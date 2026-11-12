约束，并采用了一种新的方式，将原始的联合动作值函数转换为一个易于分解的函数，具有相同的最优动作。QTRAN 是比 VDN 或 QMIX 更通用的分解方法，因此相比以前的方法覆盖了更广泛的多智能体强化学习任务。实验表明，在一些特殊博弈场景中（如加大对非合作行为的惩罚力度），QTRAN 具有更大的应用潜力[4]。

用 $\bar{u}_i = \arg \max_{u_i} Q_i(\tau_i, u_i)$ 表示智能体 $i$ 的最优局部动作。其中，$\bar{u} = [\bar{u}_i]_{i=1}^N$ 表示联合最优局部动作；$Q = [Q_i]_{i=1}^N \in \mathbb{R}^N$ 表示联合值函数向量。QTRAN 算法给出了可分解的充分条件。

定理 5.1 如果 $\sum_{i=1}^N Q_i(\tau_i, u_i) - Q_{jt}(\tau, u) + V_{jt}(\tau) \begin{cases} = 0, & u = \bar{u} \\ > 0, & u \neq \bar{u} \end{cases}$ 成立，则采取联合动作值函数 $Q_{jt}(\tau, u)$ 可以由 $[Q_i(\tau_i, u_i)]$ 分解得到，其中，$\tau$ 表示联合动作观察历史，$V_{jt}(\tau) = \max_u Q_{jt}(\tau, u) - \sum_{i=1}^N Q_i(\tau_i, \bar{u}_i)$。 QTRAN 算法如算法 5.2 所示。

# 算法 5.2 QTRAN-base (基础算法) 和 QTRAN-alt (改进算法)

```
初始化样本池 \( D \)
用随机参数 \(\theta\) 初始化 \([Q]\)、\(Q_{jt}\) 和 \(V_{jt}\)
初始化目标网络参数 \(\theta^- = \theta\)
For eposide = 1 to \( M \) do
    对每个智能体 \(i\)，初始化状态张量 \(s^0\) 和观察张量 \(o^0 = [O(s^0, i)]_{i=1}^N\)
    For \(t = 1\) to \( T \) do
        对每个智能体 \(i\)，依概率 \(\epsilon\) 选择随机动作 \(u_i'\)，否则 \(u_i' = \arg \max_{u_i'} Q_i(\tau_i', u_i')\)
        执行动作 \(u_i'\)，返回观察量和奖励值 \((o^{t+1}, r^t)\)
        将 \((\tau', u', \tau'', r^{t+1})\) 存储到 \(D\)
        从 \(D\) 中随机小批量采样
        令 \(y^{\text{dqn}}(r, \tau'; \theta^-) = r + \gamma Q_{jt}(\tau', \bar{u}'; \theta^-), \bar{u}' = [\arg \max_{u_i} Q_i(\tau_i, u_i; \theta^-)]_{i=1}^N\)
    如果是 QTRAN-base 算法，最小化损失更新参数 \(\theta\):
    \(L(\tau, u, r, \tau'; \theta) = L_{\text{td}} + \lambda_{\text{opt}} L_{\text{opt}} + \lambda_{\text{nopt}} L_{\text{nopt}}\)
    \(L_{\text{td}}(\tau, u, r, \tau'; \theta) = [Q_{jt}(\tau, u) - y^{\text{dqn}}(r, \tau'; \theta^-)]^2\)
```