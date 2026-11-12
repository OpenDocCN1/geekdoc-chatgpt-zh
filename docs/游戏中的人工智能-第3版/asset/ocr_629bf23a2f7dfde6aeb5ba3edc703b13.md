具有它们自己的私有数据存储区域。这些存储区域在子树的节点之间共享，但不在子树之间共享。这可以使用特定的装饰器实现，其工作是在运行子任务之前创建一个新的黑板：

```
class BlackboardManager extends Decorator:
    blackboard: Blackboard = null

    function run() -> bool:
        blackboard = new Blackboard()
        result = child.run()
        delete blackboard
        return result
```

使用这种方法可以提供黑板的分层结构。当某个任务来查找一些数据时，开发人员会想要开始查找最近的黑板，然后再查找在它上面的黑板，以此类推，直至找到结果或到达链中的最后一块黑板：

```
class Blackboard:
    # 用来回退的黑板
    parent: Blackboard

    # 作为关联数组的数据
    data: HashTable[string -> any]

    function get(name: string) -> any:
        if name in data: return data[name]
        else if parent: return parent.get(name)
        else: return null
```

让黑板以这种方式回退允许黑板以与编程语言相同的方式工作。在编程语言中，这种结构称为范围链（Scope Chain）。①

在我们的实现中缺少的最终元素是为行为树找到最近的黑板的机制。实现这一目标的最简单方法是将黑板传递给树，作为 run 方法的参数。但是，前面已经说过，我们并不想改变接口，更准确地说，是想要避免为不同的任务设置不同的接口，因此，任务必须知道要传递的参数。通过使所有任务接受黑板作为它们唯一的参数，可以保留任务的匿名性。

① 值得注意的是，我们在这里构建的范围链称为动态范围链（Dynamic Scope Chain）。在编程语言中，动态范围是实现范围链的原始方式，但很快人们就发现它们引起了严重的问题，并且编写可维护的代码非常困难。现代语言已全部转移到静态范围链（Static Scope Chain）。但是，对于行为树来说，动态范围不是一个大问题，并且它可能更直观。当然，我们并不知道有任何开发人员已经以这种正式术语思考过数据共享，因此我们并不了解任何具有这两种方法实践经验的人。