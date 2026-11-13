# 7. 实现 Start 函数，用于初始化：

```
private void Start()
{
    agent = GetComponent<NavMeshAgent>();
    agent.autoBraking = false;
    currentPoint = FindClosestPoint();
    GoToPoint(currentPoint);
}
```

# 8. 实现 Update 函数：

```
private void Update()
{
    if (!agent.pathPending && agent.remainingDistance <
    pointDistance)
    GoToPoint((currentPoint + 1) % patrolPoints.Length);
}
```

# 运行原理

我们实现了一个需要导航 agent 控制器的巡逻组件。首先，计算离 agent 最近的点位，然后移动到这个位置。接下来，在循环中使用 pointDistance 值让 agent 知道它需要移动的下一个点位。最后，使用模操作获取下一个巡逻点位，而不需要太多验证，只需要保持当前点位的索引值增加即可。

# 延伸阅读

我们需要考虑巡逻点位在数组中的顺序，以便它们可以按预期运行。另外，可以通过创建一个用于决定下一个巡逻点的函数。这样如果想要根据游戏需求继续优化巡逻行为的话就会很方便：

```
private int CalculateNextPoint()
{
    return (currentPoint + 1) % patrolPoints.Length;
}
```

```
private void Update()
{
    if (!agent.pathPending && agent.remainingDistance < pointDistance)
    GoToPoint(CalculateNextPoint());
}
```