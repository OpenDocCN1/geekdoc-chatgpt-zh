基本动作针对每种方法具有不同的实现。复合动作可以实现为此 Action 基类的子类。动作组合可以实现为：

```
class ActionCombination extends Action:
    # 保存子动作
    actions: Action[]

    function interrupt() -> bool:
        # 如果有任何子动作可以中断，则中断该动作
        for action in actions:
            if action.interrupt():
                return true
        return false

    function canDoBoth(other: Action) -> bool:
        # 如果所有子动作都可以执行，则执行组合动作
        for action in actions:
            if not action.canDoBoth(other):
                return false
        return true

    function isComplete() -> bool:
        # 如果所有子动作都已完成，则可以认为动作已经完成
        for action in actions:
            if not action.isComplete():
                return false
        return true

    function execute():
        # 执行所有子动作
        for action in actions:
            action.execute()
```

动作序列同样很简单。它们一次只公开一个子动作，可以实现为：

```
class ActionSequence extends Action:
    # 保存子动作
    actions: Action[]

    # 保存当前执行的子动作的索引
    activeIndex: int = 0
```