敌人探测器可以将以下代码写入黑板中：

```
target: enemy-10f9
```

移动和射击任务会询问黑板当前的目标（Target）值，并使用这些值来参数化它们的行为。在编写该任务时，应该设置为为：如果黑板没有目标，则该任务失败，并且行为树还可以查找其他要做的事情。

在伪代码中，这可能看起来像：

```
class MoveTo extends Task:
    # 正在使用的黑板
    blackboard: Blackboard

    function run() -> bool:
        target = blackboard.get('target')
        if target:
            character = blackboard.get('character')
            steering.arrive(character, target)
            return true
        else:
            return false
```

敌人探测器可能看起来像：

```
class SelectTarget extends Task:
    blackboard: Blackboard

    function run() -> bool:
        character = blackboard.get('character')
        candidates = enemiesVisibleTo(character)
        if candidates.length > 0:
            target = biggestThreat(candidates, character)
            blackboard.set('target', target)
            return true
        else:
            return false
```

在这两种情况下，我们都已经假设任务可以通过在黑板上查找信息来找出它所控制的角色。在大多数游戏中，开发人员都希望许多角色能使用一些行为树，因此每个角色都需要它自己的黑板。

有些实现会将黑板与特定子树相关联，而不是仅对整个树提供一个黑板。这允许子树