```
{
if (!activated)
    return;
// 在这里编写你的行为逻辑
}
```

# 5. 创建最后一个类 DecisionTree:

```
using UnityEngine;
using System.Collections;
public class DecisionTree : DecisionTreeNode
{
    public DecisionTreeNode root;
    private Action actionNew;
    private Action actionOld;
}
```

# 6. 重写 MakeDecision 函数:

```
public override DecisionTreeNode MakeDecision()
{
    return root.MakeDecision();
}
```

# 7. 实现 Update 函数:

```
void Update()
{
    actionNew.activated = false;
    actionOld = actionNew;
    actionNew = root.MakeDecision() as Action;
    if (actionNew == null)
        actionNew = actionOld;
    actionNew.activated = true;
}
```

# 运行原理

决策节点选择要走的路线，递归地调用 MakeDecision 函数。值得注意的是分支必须是决策，而叶子节点必须是行动。另外，我们应该注意不要在树中创建环。如图 3-1 所示。

# 延伸阅读

可以从之前创建的抽象类中开始，创建自定义的决策和行动，比如，决定是攻击玩家还是逃离玩家。

下面的代码片段是自定义的 Boolean 类型的决策:

```
using UnityEngine;
using System.Collections;
```

![](bbox=[0.64, 0.71, 0.96, 0.88])

图 3-1