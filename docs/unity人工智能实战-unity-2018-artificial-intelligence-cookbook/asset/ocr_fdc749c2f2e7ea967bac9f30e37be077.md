```
{
    state = newState;
}
```

# 9. 实现函数，获取从击球器到小球的距离：

```
private float DistanceToPuck()
{
    Vector3 puckPos = puck.transform.position;
    Vector3 paddlePos = paddle.transform.position;
    return Vector3.Distance(puckPos, paddlePos);
}
```

# 10. 声明用于攻击的成员函数，后面的步骤将定义其函数体：

```
private void Attack()
{
    if (hasAttacked)
    return;
    // 下面的步骤
}
```

# 11. 启用 agent 组件并计算到小球的距离：

```
agent.enabled = true;
float dist = DistanceToPuck();
```

# 12. 检测小球是否在范围内，如果超出范围的话，就跟住它：

```
if (dist > leave.dangerRadius)
{
    Vector3 newPos = puck.transform.position;
    newPos.z = paddle.transform.position.z;
    seek.target.transform.position = newPos;
    seek.enabled = true;
    return;
}
```

# 13. 如果在范围内，则撞击小球：

```
hasAttacked = true;
seek.enabled = false;
Vector3 paddlePos = paddle.transform.position;
Vector3 puckPos = puck.transform.position;
Vector3 runPos = paddlePos - puckPos;
runPos = runPos.normalized * 0.1f;
runPos += paddle.transform.position;
leave.target.transform.position = runPos;
leave.enabled = true;
```

# 14. 实现函数，用于重置撞击小球的参数，代码如下：

```
private void AttackReset()
{
    float dist = DistanceToPuck();
    if (hasAttacked && dist < leave.dangerRadius)
    return;
    hasAttacked = false;
```