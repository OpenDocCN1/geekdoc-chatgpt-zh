上睡觉” 的动作就完全满足了“睡觉” 的目标。

如果不止一个动作可以实现目标，那么我们可以随机进行选择，或者简单地选择我们发现的第一个动作。

# 1. 伪代码

可以按以下方式实现该算法：

```
function chooseAction(actions: Action[], goals: Goal[]) -> Action:
    # 找到值最高的目标进行尝试并执行
    topGoal: Goal = goals[0]
    for goal in goals[1..]:
        if goal.value > topGoal.value:
            topGoal = goal
    # 找到要采取的最佳动作
    bestAction: Action = actions[0]
    bestUtility: float = -actions[0].getGoalChange(topGoal)
    for action in actions[1..]:
        # 在此可以反转变化值，因为低变化值是好的
        # （我们需要减少目标的坚持值）
        # 但是实际效用 utility 一般来说都是按比例的
        # 因为高值更好
        utility = -action.getGoalChange(topGoal)
        # 现在需要找到最低变化值（最高效用）
        if thisUtility > bestUtility:
            bestUtility = thisUtility
            bestAction = action
    # 返回要执行的最佳动作
    return bestAction
```

这只是两个 max()样式的代码块，一个用于目标，另一个用于动作。

# 2. 数据结构和接口

在前面的代码中，我们假设目标具有以下形式的接口：

```
class Goal:
    name: string
    value: float
```