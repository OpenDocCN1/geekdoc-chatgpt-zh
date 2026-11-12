的选择。

# 1. 伪代码

以下是随机二元决策的伪代码：

```
class RandomDecision extends Decision:
    lastFrame: int = -1
    currentDecision: bool = false

    function testValue() -> bool:
        frame = getCurrentFrame()

        # 检查已经存储的决策是否太陈旧
        if frame > lastFrame + 1:
            # 做出新决策并保存
            currentDecision = randomBoolean()

        # 无论哪种方式，都需要更新帧值
        lastFrame = frame

        return currentDecision
```

为了避免必须遍历每个未使用的决策才能删除其先前的值，我们在做出决策之后保存了该决策的帧编号。如果该测试方法被调用，并且先前存储的值被保存在前一帧中，则我们将使用它。但如果它是在更早以前存储的，那么将创建一个新值。

此代码依赖于两个函数。

+   - frame()将返回当前帧的编号。这应该是在每帧基础上递增 1。如果该决策树不是每一帧都被调用，则 frame()应该用每次该决策树被调用时递增的函数替换。

+   - randomBoolean()将返回一个随机布尔值，true 或 false。

该算法用于随机决策，可以与上面提供的决策树算法一起使用。

# 2. 超时

如果角色永远做同样的事情，它可能看起来很奇怪。例如，在上面的示例中，只要角色不受攻击，那么决策树会让角色永远保持站立不动。

可以对已存储的随机决策使用超时信息进行设置，因此，角色偶尔会改变其行为。决策的伪代码现在如下：

```
class RandomDecisionWithTimeOut extends Decision:
    lastFrame: int = -1
```