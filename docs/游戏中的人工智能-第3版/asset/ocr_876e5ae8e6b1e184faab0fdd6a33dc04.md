忽略某些通道。例如，如果子目标希望在面向不同方向时达到特定的速度，那么我们就知道该目标是不可能的。因此，我们可能会抛弃方向性的通道。

在转向管道的背景下，执行器的复杂度经常作为算法问题被提出。值得注意的是，这是一项实现决定，管道在需要时将支持综合执行器（开发人员显然必须付出执行时间的代价），但它们也支持几乎不需要运行时间的微不足道的执行器。

本章稍后将介绍作为一般性主题的驱动方式，因此我们将避免在此阶段讨论琐碎的细节。对于该算法的目的来说，我们将假设执行器采用目标并返回角色将要到达的路径的描述。

最终，我们需要实际执行转向。执行器的最终工作是返回实现预测路径所需的力和扭矩（或其他马达控制——参见第 3.8 节）。

# 2. 伪代码

转向管道可使用以下算法实现：

```
class SteeringPipeline:
    character: Kinematic

    # 在管道的每个阶段的组件列表
    targeters: Targeter[]
    decomposers: Decomposer[]
    constraints: Constraint[]
    actuator: Actuator

    # 算法尝试的次数
    # 该算法将尝试找到不受限制的路线
    constraintSteps: int

    # 死锁转向行为
    deadlock: SteeringBehavior

    function getSteering() -> SteeringOutput:
        # 首先需要获取顶级目标
        goal: Goal = new Goal()
        for targeter in targeters:
            targeterGoal = targeter.getGoal(character)
            goal.updateChannels(targeterGoal)

        # 现在来分解目标
        for decomposer in decomposers:
```