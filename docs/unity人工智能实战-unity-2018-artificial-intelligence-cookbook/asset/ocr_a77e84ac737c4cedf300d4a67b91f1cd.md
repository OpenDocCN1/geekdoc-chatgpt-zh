# ## 3.5 实现行为树

行为树可以看作是其他几个人工智能技术的综合体，比如有限状态机、规划，以及决策树。实际上，行为树与有限状态机有一些相似之处，但不是状态机，而是跨树结构的动作。

# ### 准备工作

本节需要理解协程。

# ### 操作步骤

就像决策树那样，我们要创建 3 个伪抽象类，用于处理这个过程：

+   1. 创建基类 Task:

```
```csharp
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class Task : MonoBehaviour
{
    public List<Task> children;
    protected bool result = false;
    protected bool isFinished = false;
}
```
```

+   2. 实现析构函数:

```
```csharp
public virtual void SetResult(bool r)
{
    result = r;
    isFinished = true;
}
```
```

+   3. 实现用于创建行为的函数:

```
```csharp
public virtual IEnumerator Run()
{
    SetResult(true);
    yield break;
}
```
```

+   4. 实现用于启动行为的通用函数:

```
```csharp
public virtual IEnumerator RunTask()
{
    yield return StartCoroutine(Run());
}
```
```

+   5. 创建 ConditionBT 类:

```
```csharp
using UnityEngine;
using System.Collections;
```
```