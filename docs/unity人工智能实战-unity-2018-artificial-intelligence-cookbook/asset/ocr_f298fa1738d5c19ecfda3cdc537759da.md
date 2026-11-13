```
```csharp
input = Mathf.Log(totalActions/(float)count[best]);
input *= 2f;
tempScore = Mathf.Sqrt(input);
if (tempScore > bestScore)
{
    best = i;
    bestScore = tempScore;
}
```
```

# 11. 返回最佳行动：

```
```csharp
lastStrategy = best;
lastAction = GetActionForStrategy((RPSAction)best);
return lastAction;
```
```

# 12. 定义函数，用于获取对初始行动作出的最佳响应：

```
```csharp
public RPSAction GetActionForStrategy(RPSAction strategy)
{
    RPSAction action;
    // next steps
}
```
```

# 13. 实现游戏的基本规则：

```
```csharp
switch (strategy)
{
    default:
    case RPSAction.Paper:
        action = RPSAction.Scissors;
        break;
    case RPSAction.Rock:
        action = RPSAction.Paper;
        break;
    case RPSAction.Scissors:
        action = RPSAction.Rock;
        break;
}
```
```

# 14. 返回最佳行动：

```
```csharp
return action;
```
```

15. 定义成员函数，基于对方的行动，计算行动是否有用。代码开头是这样的：

```
```csharp
public float GetUtility(RPSAction myAction, RPSAction opponents)
{
    float utility = 0f;
    // next steps
}
```
```

# 16. 检查对方玩家是否已经出布：

```
```csharp
if (opponents == RPSAction.Paper)
{
    if (myAction == RPSAction.Rock)
        utility = -1f;
    else if (myAction == RPSAction.Scissors)
```
```