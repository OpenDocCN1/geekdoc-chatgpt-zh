转换的触发和激发这种分离机制使得转换也具有它们自己的动作。一般来说，从一个状态转换到另一个状态也涉及执行某些行动。在这种情况下，激发转换可以将所需的动作添加到该状态返回的动作中。

# # 5.3.3 伪代码

状态机可以保存状态列表，指示哪一个状态是当前状态。它具有触发和激发转换的更新函数，以及返回一组要执行的动作的函数：

```
class StateMachine:
    # 在某一时间处于某一状态（初始状态）
    initialState: State
    currentState: State = initialState

    # 检查并应用转换，返回一个动作列表
    function update() -> Action[]:
        # 假设没有任何转换被触发
        triggered: Transition = null

        # 遍历检查每个转换
        # 存储触发的第一个转换
        for transition in currentState.getTransitions():
            if transition.isTriggered():
                triggered = transition
                break

        # 检查是否有转换需要激发
        if triggered:
            # 找到目标状态
            targetState = triggered.getTargetState()

            # 添加旧状态的退出动作
            # 添加转换动作以及新状态的条目
            actions = currentState.getExitActions()
            actions += triggered.getActions()
            actions += targetState.getEntryActions()

            # 完成转换并返回动作列表
            currentState = targetState
            return actions
```