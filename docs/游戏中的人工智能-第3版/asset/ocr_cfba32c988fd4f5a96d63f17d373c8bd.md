# 2. 阶段

当然，采用相对素数的频率仍然会发生冲突。图 10.3 的示例显示了频率分别为 2、3 和 5 的 3 种行为。每 6 帧，行为 A 和 B 发生冲突，每 30 帧，所有这些帧都会发生冲突。

选择相对素数作为频率使得冲突点不那么频繁，但无法消除它们。

为了解决这个问题，我们为每个行为添加了一个额外的参数。此参数称为阶段（Phase），它不会更改频率，但会在行为被调用时进行偏移。假设有 3 种行为，它们的频率都是 3。在原始调度程序下，它们将同时运行，即每 3 帧运行一次。如果我们可以偏移，那么它们就可以在连续的帧上运行，因此每个帧都将有一个行为在运行，但是所有的行为都仍将每 3 帧运行一次。

# 3. 伪代码

可以通过以下方式实现基本调度程序：

```
class FrequencyScheduler:
    # 保存每个行为要调度的数据
    class BehaviorRecord:
        thingToRun: function
        frequency: int
        phase: int

    # 保存行为记录的列表
    behaviors: BehaviorRecord[]

    # 保存当前帧编号
    frame: int

    # 将行为添加到列表
    function addBehavior(func: function, frequency: int, phase: int):
        # 编译和添加记录
        record = new Record()
        record.functionToCall = func
        record.frequency = frequency
        record.phase = phase
        behaviors += record

    # 每帧调用一次
    function run ():
        # 递增帧编号
        frame += 1
```