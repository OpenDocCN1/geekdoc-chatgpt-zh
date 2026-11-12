```
# 先前活动的已经被丢弃
active = [action]
priorityCutoff = action.priority

else:
# 检查是否可以添加该动作
canAddToActive = true
for activeAction in active:
    if not activeAction.canDoBoth(action):
        canAddToActive = false
        break

# 如果两个动作都可以执行，则执行组合动作
if canAddToActive:
    queue -= action
    active += action
    priorityCutoff = action.priority

# 删除或运行活动的动作
for activeAction in copy(active):
    if activeAction.isComplete():
        active -= activeAction
    else:
        activeAction.execute()
```

execute 函数将执行所有调度、队列处理和动作执行。scheduleAction 函数只是向队列中添加一个新动作。

copy 函数将创建动作列表的副本（队列或活动集）。这在过程函数中的两个顶层循环中都是必需的，因为可以从循环内的列表中删除项。

# 5.10.4 数据结构和接口

动作管理器依赖于具有以下接口的常规动作结构:

```
class Action:
    expiryTime: float
    priority: float

    function interrupt() -> bool
    function canDoBoth(other: Action) -> bool
    function isComplete() -> bool
```