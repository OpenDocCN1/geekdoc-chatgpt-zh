```
# 保存一个可能行为的数据
class BehaviorRecord:
    behavior: Behavior
    minImportance: float
    maxImportance: float
    enter: function
    exit: function

    # 检查该重要性值是否在正确的范围中
    function isValid(importance: float) -> bool:
        return minImportance >= importance >= maxImportance
```

enter 和 exit 成员保存了对一个函数的引用（它们也可以实现为要重载的方法，但是随后我们将需要处理多个行为记录的子类）。如果没有设置或需要中断，则其中任何一个都可以保留为不设置。

当相应的行为进入或退出时，将分别调用两个函数。其中，enter 调用 enterFunction 函数，exit 调用 exitFunction 函数，它们应具有以下形式：

```
function enterFunction(previous: Behavior)
function exitFunction(next: Behavior)
```

它们将下一个或上一个行为作为参数，以允许它们支持行为为压缩。在行为的退出方法中，可以将适当的数据传递给已经给出的下一个行为。

这是首选的方法，因为它允许退出行为清除其所有数据。如果使用 enterFunction 函数来尝试访问先前行为的数据，则可能已经清除了数据。当然，我们可以交换两个调用的顺序，以便在退出之前调用进入函数。糟糕的是，这意味着两种行为的内存同时处于活动状态，而这可能会导致内存峰值，所以我们宁可谨慎一点。在具有耗时的垃圾收集的平台（例如 Unity）上，我们绝对希望内存保持稳定，并且应该重复使用预先分配的内存。通常在这方面我们很容易犯错，而且在这两种行为都未完全配置时，需要等待很短的时间。

# 6. 实现说明

上面的伪代码被设计为使得行为细节层次可以作为其自身的行为。如前所述，这允许我们将其用作分层调度系统的一部分。

在完整的实现中，我们还应该记录决定运行哪个行为所花费的时间，然后从我们传递给行为的时间中减去该持续时间。虽然细节层次技术的选择很快，但最好保持计时尽可能准确。