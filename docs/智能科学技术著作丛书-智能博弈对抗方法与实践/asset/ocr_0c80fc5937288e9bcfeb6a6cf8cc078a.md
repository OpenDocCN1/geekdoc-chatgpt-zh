般和博弈常用于双方或多方交互建模。扩展式博弈的表示方式有两种，正则式 (normal-form) 表示常用于同步行为 (simultaneous-move) 决策场景描述，序贯式 (sequence-form) 表示[33]常用于行为策略多阶段交互场景描述。回合制博弈 (turn based game, TBG)[34]常用于交替式决策场景描述，其中回合制定指博弈局中人轮流出招。

# 2. 元博弈模型

元博弈 (meta game)，即博弈的博弈 (game of game)，常用于博弈策略空间分析[35]，是研究实证博弈理论分析 (empirical game theoretic analysis, EGTA) 的基础模型[36]，目前已被广泛应用于各种可采用模拟器仿真的现实场景，包括供应链管理分析、广告拍卖和能源市场、设计网络路由协议、公共资源管理、对抗策略选择、博弈策略动态分析等。表 2.1 描述了博弈论、元博弈与强化学习相关要素的区别。

表 2.1 博弈论、元博弈与强化学习

| 博弈论 | 元博弈 | 强化学习 |
| :--- | :--- | :--- |
| 博弈 (game) | 博弈 (game) | 环境 (environment) |
| 参与方 (player) | 种群 (population) | 智能体 (agent) |
| 行动 (action) | 类型 (type) | 动作 (action) |
| 策略 (strategy) | 分布 (distribution) | 策略 (policy) |
| 收益 (payoff) | 适应度 (fitness) | 奖励 (reward) |

近年来，一些研究人员对博弈的策略空间几何形态进行了探索。Jiang 等[37]首次利用组合霍奇理论研究亥姆霍兹分解。Candogan 等[38]探索了策略博弈的“流”表示，提出策略博弈主要由势 (potential) 部分、调和 (harmonic) 部分和非策略部分组成。Hwang 等[39]从策略等价的角度研究了正则式博弈的分解方法。Balduzzi 等[40]研究提出任何一个泛函式博弈 (functional-form game, FFG) 可以做直和 (direct sum) 分解成传递压制博弈 (transitive game) 和循环压制博弈 (cycle game) 两部分，可以利用梯度、散度和旋度等刻画博弈策略空间几何形态。函数 φ(v, w) 表示从 v 到 w 的流，根据霍奇分解，传递压制博弈的旋度为 0，循环压制博弈的散度为 0，博弈的向量空间满足正交分解

```
TransitiveGame ⊕ CycleGame = im(grad) ⊕ ker(div)

grad(f)(v, w) := f(v) - f(w)

div(φ)(v, w) := ∫_w φ(v, w)dw
```