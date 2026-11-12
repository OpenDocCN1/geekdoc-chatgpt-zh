某个策略遍历所有经过这个状态的路径并对其求期望：

\( v_\pi(s_t) = E_\tau \left[ \sum_{k=0}^\infty \gamma^k r_{t+k+1} \right] = \sum_\tau p(\tau) \sum_{k=0}^\infty \gamma^k r_{t+k+1} \) (3.35)

这里的 \(\tau\) 表示从状态 \(s_t\) 开始的某一条遍历结果。由 MDP 的定义可知：

\( v_\pi(s_t) = \sum_{(s_t, a_t, \cdots)} \pi(a_t|s_t) p(s_{t+1}|s_t, a_t) \cdots \sum_{k=0}^\infty \gamma^k r_{t+k+1} \) (3.36)

使用换元法，可得

\( v_\pi(s_t) = \sum_{(s_t, a_t, \cdots)} \pi(a_t|s_t) p(s_{t+1}|s_t, a_t) \cdots \sum_{k=0}^\infty \gamma^k r_{t+k+1} \)
\( = \sum_{a_t} \pi(a_t|s_t) \sum_{s_{t+1}} p(s_{t+1}|s_t, a_t) \sum_{(s_{t+1}, a_{t+1}, \cdots) \neg \tau'} \pi(a_{t+1}|s_{t+1}) \cdots \sum_{k=0}^\infty \gamma^k r_{t+k+1} \)
\( = \sum_{a_t} \pi(a_t|s_t) \sum_{s_{t+1}} p(s_{t+1}|s_t, a_t) \sum_{(s_{t+1}, a_{t+1}, \cdots) \neg \tau'} \pi(a_{t+1}|s_{t+1}) \cdots \left( r_{t+1} + \sum_{k=1}^\infty \gamma^k r_{t+k+1} \right) \)
\( = \sum_{a_t} \pi(a_t|s_t) \sum_{s_{t+1}} p(s_{t+1}|s_t, a_t) \left[ r_{t+1} + \sum_{(s_{t+1}, a_{t+1}, \cdots) \neg \tau'} \pi(a_{t+1}|s_{t+1}) \cdots \sum_{k=1}^\infty \gamma^k r_{t+k+1} \right] \) (3.37)

将下一时刻的状态值函数展开，可得

\( v_\pi(s_{t+1}) = \sum_{(s_{t+1}, a_{t+1}, \cdots) \neg \tau'} \pi(a_{t+1}|s_{t+1}) p(s_{t+2}|s_{t+1}, s_{t+1}) \cdots \sum_{k=1}^\infty \gamma^k r_{t+k+1} \)
\( v_\pi(s_t) = \sum_{a_t} \pi(a_t|s_t) \sum_{s_{t+1}} p(s_{t+1}|s_t, a_t) [r_{t+1} + v_\pi(s_{t+1})] \) (3.38)

由式 (3.38) 得到在强化学习中使用的状态值函数的递归形式。通过一个假设已知的值函数，任意一个状态的价值可以由其他状态的值函数递归得到。式 (3.38) 也称为贝尔曼公式，同时，根据值函数和动作值函数的关系，可以推导出动作值函数的递归方程为

\( q_\pi(s_t, a_t) = \sum_{s_{t+1}} p(s_{t+1}|s_t, a_t) \sum_{a_{t+1}|s_{t+1}} [r_{t+1} + q_\pi(s_{t+1}, a_{t+1})] \) (3.39)

式 (3.38) 和式 (3.39) 可以认为是基于值函数方法的基石。通过这个函数对智