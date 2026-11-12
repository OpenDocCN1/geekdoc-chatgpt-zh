```
# 回溯
object, triggered = progress.pop()

else:
    return false

while not topRule.complete(object)

return true

# 运行给定触发列表中的下一个规则
# 否则从所有规则中选择
function runOne(object: Object,
    triggered: Rule[]) -> Rule[]:
if not triggered:
    triggered = getTriggeredRules(object: Object)
    if not triggered:
        return null
    triggered.shuffle()
rule = triggered.peek()
rule.fire(object)
return triggered
```

# 2. 数据结构

我们构建的对象现在有一个 clone 方法，可以尝试多种不同的规则：

```
class Object:
    function clone() -> Object
```

可以通过 complete 函数询问规则本身是否成功：

```
class Rule:
    function complete(object: Object) -> bool
    function triggers(object: Object) -> bool
    function fire(object: Object)
```

如果规则表示的任务已经完成，则 complete 应该返回 true。目前这仅用于确定总体计划任务是否已经完成。

该代码可以进行扩展，以存储和重用部分规划，这些规划将在执行过程中满足规则。例如，当摩天大楼的所有细化均已添加到对象（即窗户已经创建或门已放置完毕）时，