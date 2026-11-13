```
void OnDestroy ()
{
    Destroy(targetAux);
}
```

# 4. 实现 GetSteering 函数：

```
public override Steering GetSteering()
{
    Vector3 direction = targetAux.transform.position -
    transform.position;
    float distance = direction.magnitude;
    float speed = agent.velocity.magnitude;
    float prediction;
    if (speed <= distance / maxPrediction)
    prediction = maxPrediction;
    else
    prediction = distance / speed;
    target.transform.position = targetAux.transform.position;
    target.transform.position += targetAgent.velocity * prediction;
    return base.GetSteering();
}
```

# 5. 创建 Evade 行为，过程相同，但要注意父类是 Flee：

```
public class Evade : Flee
{
    // 与Purse类完全相同
}
```

# 运行原理

这两个行为依赖于 Seek 和 Flee，然后根据目标的速度预测走向，在内部用一个额外的对象作为目标位置。

# 1.4 为物理引擎调整 agent

我们学习了如何为 agent 实现简单的行为，然而还需要考虑的是，游戏可能需要用到 Unity 的物理引擎。我们要注意 agent 是否附加了 RigidBody 组件，然后相应地调整实现代码。

# 准备工作

第一步是记住每个事件函数的执行顺序，现在还需要额外注意 FixedUpdate 函数，因为我们要使用物理引擎管理行为。

+   - FixedUpdate
+   - Update
+   - LateUpdate