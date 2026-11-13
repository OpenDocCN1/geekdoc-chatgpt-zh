
}
public virtual float GetDuration()
{
    return 0f;
}
}

## 2. 创建 GoalGOB 父类及其成员函数:

```csharp
using UnityEngine;
using System.Collections;

public class GoalGOB
{
    public string name;
    public float value;
    public float change;
}
```

## 3. 定义函数用于处理不满足的值，以及随着时间变化的值:

```csharp
public virtual float GetDiscontentment(float newValue)
{
    return newValue * newValue;
}

public virtual float GetChange()
{
    return 0f;
}
```

## 4. 定义 ActionChooser 类:

```csharp
using UnityEngine;
using System.Collections;

public class ActionChooser : MonoBehaviour
{
}
```

## 5. 实现函数，用于计算无意识的行动:

```csharp
public float CalculateDiscontentment(ActionGOB action, GoalGOB[] goals)
{
    float discontentment = 0;
    foreach (GoalGOB goal in goals)
    {
        float newValue = goal.value + action.GetGoalChange(goal);
        newValue += action.GetDuration() * goal.GetChange();
        discontentment += goal.GetDiscontentment(newValue);
    }

    return discontentment;
}
```

## 6. 实现用于选择行动的函数:

```csharp
public ActionGOB Choose(ActionGOB[] actions, GoalGOB[] goals)
{
    ActionGOB bestAction;
    bestAction = actions[0];
    float bestValue = CalculateDiscontentment(actions[0], goals);
    float value;
    // 下面的步骤
}
```

## 7. 根据权衡原则选出最佳行动：

```csharp
foreach (ActionGOB action in actions)
{
    value = CalculateDiscontentment(action, goals);
    if (value < bestValue)
    {
        bestValue = value;
        bestAction = action;
    }
}
```

## 8. 返回最佳行动：

```csharp
return bestAction;
```

## 运行原理

计算不满足行动的函数根据目标值改变了多少，然后从行动和行动的执行时间的角度，帮助避免无意识的行动。然后，选择行动的函数根据最小影响（不满）计算出最佳的行动。

## 3.8 实现黑板架构

本章中，我们要模拟平时如何有组织地头脑风暴和制定决策。首先，这叫作黑板架构，或者黑板系统，就像一组专家聚集在一个房间里，房间有一个大黑板。专家们面对某个问题（事实、未知，等等），每个人可以添加或者删除黑板上的东西，并给出自己解决问题的意见。关键在于如何决定谁来领导。

假设我们有一个 RTS 游戏，需要构建一个城市，然后保卫这个城市，占据领土。对于这样一个任务，如果我们可以实现前面提到的解决不同问题的模块，比如构建和维持、守卫、攻击。就这一点而言，我们需要子模块（专家系统）来划分知识点和决策。根据游戏的当前状态，专家被赋予控制权以便采取行动来达到当前目标。

下面我们要精心解决这个问题。

## 准备工作

我们需要定义某个数据结构用于在实现主要的步骤前初始化：

### 1. 创建数据结构，用灵活的方式存储数据，代码如下：

```csharp
public struct BlackboardDatum
{
    public int id;
    public string type;
    public object value;
}
```

### 2. 定义专家类，代码如下：

```csharp
public abstract class BlackboardExpert
{
    public virtual float GetInsistence(Blackboard board)
    {
        return 0f;
    }
    public virtual void Run(Blackboard board) {}
}
```

### 3. 创建数据结构，用于保存专家要采取的行动，代码如下：

```csharp
public struct BlackboardAction
{
    public object expert;
    public string name;
    public System.Action action;
}
```

## 操作步骤

现在，我们创建处理黑板的类，以及要模拟的主要逻辑：

### 1. 定义 Blackboard 类，代码如下：

```csharp
using System.Collections.Generic;

public class Blackboard
{
    public List<BlackboardDatum> entries;
    public List<BlackboardAction> pastActions;
    public List<BlackboardExpert> experts;
}
```

### 2. 实现构造函数：

```csharp
public Blackboard()
{
    entries = new List<BlackboardDatum>();
    pastActions = new List<BlackboardAction>();
    experts = new List<BlackboardExpert>();
}
```

### 3. 定义迭代逻辑，代码如下：

```csharp
public void RunIteration()
{
    // next steps
}
```

### 4. 添加所需变量：

```csharp
BlackboardExpert bestExpert = null;
float maxInsistence = 0f;
```

### 5. 实现循环，用于决定下一步是哪个专家行动，代码如下：

```csharp
foreach (BlackboardExpert e in experts)
{
    float insistence = e.GetInsistence(this);
    if (insistence > maxInsistence)
    {
        maxInsistence = insistence;
        bestExpert = e;
    }
}
```

### 6. 如果某个专家被选中了，就让他行动，代码如下：

```csharp
if (bestExpert != null)
    bestExpert.Run(this);
```

## 运行原理

首先，我们把所有数据结构准备好，用于创建干净灵活且可复用的黑板系统。然后，准备好黑板，以及用于决定下一步轮到哪个专家行动的主循环。与第 1 章中基于优先级的行为混合算法非常类似，但它是用于解决问题的。每个专家计算出他的方法的重要程度，然后用最重要的方法来解决问题。要注意定义 GetInsistence 函数的返回值的范围。

## 更多参考

我们需要通过覆盖 Expert 类中的成员函数来实现自己的成员函数，要注意，GetInsistence 函数中的黑板条目是由每个专家读写的，然后在运行 Run 函数期间执行操作。如果专家的 Run 函数的计算成本很高，最好用协程来处理。

最后，通过把黑板定义为游戏状态，每个专家都可以读写数据，这可以在每个项目中重新访问。要让代码如期运行，请记住算法的优先级部分。

## 3.9 尝试 Unity 的动画状态机

当我们与艺术家合作实现动画，或者用自己的技术实现动画时，通常会在 Unity 的动画状态图上看到很多东西。然而，用代码实现动画不太常见。通常用 MonoBehaviour 脚本修改动画的状态，然后用单独的方式处理行为。

本节中会使用第 1 章中的基础移动行为，因为我们要用动画控制行为的状态。

## 准备工作

本节不包含 Animator 窗口中关于动画的初始化工作，读者可以自己实现。我们将专注于使用强大的内置有限状态机来控制 agent。

## 实现步骤

先初始化所有内容：

- 1. 使用 Project 窗口中的 Create 按钮创建一个新的 Animator Controller，如图 3-3 所示。

![](img/71cedc28e32e083292208e378461ea21_89_0.png)

图 3-3

- 2. 创建一个新的默认状态，命名为 Wandering。
- 3. 创建一个新的状态，命名为 Pursuing。
- 4. 通过两次转换把这两个状态互相连接，如图 3-4 所示。

![](img/71cedc28e32e083292208e378461ea21_89_1.png)

图 3-4

- 5. 创建两个触发器参数： Pursue 和 Wander，如图 3-5 所示。

![](img/71cedc28e32e083292208e378461ea21_90_0.png)

图 3-5

- 6. 通过 Pursuing -> Wandering 转换把 Wander 参数赋给条件集，如图 3-6 所示。

![](img/71cedc28e32e083292208e378461ea21_90_1.png)

图 3-6

- 7. 在 Wandering -> Pursuing 转换中执行逆分配。
- 8. 我们需要在 Wander 状态上创建一个脚本，命名为 AFSMWandering，如图 3-7 所示。
- 9. 在 Pursue 状态上创建一个脚本，命名为 AFSMPursue。
- 10. 创建一个新的 GameObject，命名为 Wanderer，附加到下面的组件上：

  - Animator
  - Agent
  - Wander（禁用）
  - Seek（禁用）

![](img/71cedc28e32e083292208e378461ea21_90_2.png)

图 3-7

图 3-8 展示了游戏对象现在的设置，可以看它在上一步中提到的操作后的表现。

![](img/71cedc28e32e083292208e378461ea21_91_0.png)

图 3-8

现在实现 Wander 状态。

- 1. 打开前面创建的代码文件 AFSMWanderer.cs
- 2. 添加下面的变量：

```csharp
public float pursueDistance = 3f;
private Wander _wander;
private Seek _pursue;
```

- 3. 取消 OnStateEnter 成员函数的注释：
- 4. 添加代码：

```csharp
AgentBehavior[] behaviors;
GameObject gameObject = animator.gameObject;
behaviors = gameObject.GetComponents<AgentBehavior>();
```

```csharp
foreach (AgentBehavior b in behaviors)
    b.enabled = false;

_wander = gameObject.GetComponent<Wander>();
_pursue = gameObject.GetComponent<Seek>();
if (_wander == null || _pursue == null)
    return;
_wander.enabled = true;
animator.gameObject.name = "Wandering";
```

- 5. 取消 OnStateUpdate 成员函数的注释：
- 6. 添加代码：

```csharp
Vector3 targetPos, agentPos;
targetPos = _pursue.target.transform.position;
agentPos = animator.transform.position;
float distance = Vector3.Distance(targetPos, agentPos);
if (distance > pursueDistance)
    return;
animator.SetTrigger("Pursue");
```

最后，我们在 Pursue 状态中实现类似代码：

- 1. 打开前面创建的代码文件 AFSMWanderer.cs
- 2. 添加下面的变量：

```csharp
public float stopDistance = 8f;
private Wander _wander;
private Seek _pursue;
```

- 3. 取消 OnStateEnter 成员函数的注释：
- 4. 添加代码：

```csharp
AgentBehavior[] behaviors;
GameObject gameObject = animator.gameObject;
behaviors = gameObject.GetComponents<AgentBehavior>();
foreach (AgentBehavior b in behaviors)
    b.enabled = false;

_wander = gameObject.GetComponent<Wander>();
_pursue = gameObject.GetComponent<Seek>();
if (_wander == null || _pursue == null)
    return;
_wander.enabled = true;
animator.gameObject.name = "Seeking";
```

- 5. 取消 OnStateUpdate 成员函数的注释：
- 6. 添加代码：

```csharp
Vector3 targetPos, agentPos;
targetPos = _pursue.target.transform.position;
agentPos = animator.transform.position;
if (Vector3.Distance(targetPos, agentPos) < stopDistance)
    return;
animator.SetTrigger("Wander");
```

## 实现原理

我们的有限状态机就是动画引擎。首先创建 agent 的状态和转换。然后创建继承于 StateMachineBehaviour 类的行为，把它附加到相应的状态上。另外，获取游戏对象和脚本组件所需要的信息，从而在每个状态中制作决策。如果有必要，调用合适的触发器以改变状态，从而改变 agent 的行为。

## 更多参考

通过使用 Unity 的动画状态机，我们获得了灵活性。用这种方式我们可以用最少的代码在几个 agent 间复用行为和制定决策。然而，获得了灵活性和模块化，却失去了脚本的中心化。所以要考虑在团队中使用的情况，要记住这是用于测试目的。

## 第 4 章

## 新的 NavMesh API

本章中，我们将通过下面的内容学习如何使用新的 NavMesh API：

- 初始化 NavMesh 开发组件
- 创建和管理 NavMesh，用于多种类型的 agent
- 在运行时创建和更新 NavMesh 数据
- 控制 NavMesh 实例的生命周期
- 连接多个 NavMesh 实例
- 创建动态的带有障碍物的 NavMesh
- 用 NavMesh API 实现某些行为

### 4.1 简介

NavMesh API 的引入及其扩展，对 AI 和游戏玩法开发者来说，像是打开了新世界的大门。让我们可以更好地优化关卡，甚至是实时优化，而不需要额外的工具（至少大部分时候）。

本章中，我们将学习如何操作运行于强大的导航引擎之上的新工具。

### 4.2 初始化 NavMesh 开发组件

在制作关卡和了解所有内容之前，我们需要下载 Unity 的 GitHub 库里的一些资源。

## 准备工作

有些读者可能有使用 Unity 的导航引擎的经验。掌握如何使用 Navigation 窗口创建 NavMesh 很重要，而且要理解 NavMeshAgent 组件的基本内容。

## 操作步骤

从 GitHub 库下载资源：

- 1. 用浏览器打开网址 https://github.com/Unity-Technologies/NavMesh Components。
- 2. 进入 releases 页面，找到 Code 选项卡，如图 4-1 所示。

![](img/71cedc28e32e083292208e378461ea21_95_0.png)

图 4-1

- 3. 下载与你的 Unity 版本对应的压缩文件，本书编写时使用的是 2018.1.0f2。
- 4. 把压缩文件解压到我们的工程代码之外的文件夹，解压后的文件也是一个 Unity 工程，如图 4-2 所示。

![](img/71cedc28e32e083292208e378461ea21_95_1.png)

图 4-2

- 5. 打开 Assets 文件夹，如图 4-3 所示。
- 6. 复制或剪切 NavMeshComponents 文件夹到你的工程的 Asset 文件夹中，如图 4-4 所示。

![](img/71cedc28e32e083292208e378461ea21_95_2.png)

图 4-3

![](img/71cedc28e32e083292208e378461ea21_95_3.png)

图 4-4
