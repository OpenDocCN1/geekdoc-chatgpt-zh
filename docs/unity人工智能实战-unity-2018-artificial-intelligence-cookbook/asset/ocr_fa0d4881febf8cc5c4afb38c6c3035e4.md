# 操作步骤

本节需要向 Agent 类中添加一些改动。

+   1. 打开 Agent 类。

+   2. 添加一个成员变量用于保存刚体组件的引用：

```
private Rigidbody aRigidBody;
```

+   3. 在 Start 函数中获取刚体组件的引用：

```
aRigidBody = GetComponent<Rigidbody>();
```

+   4. 实现用于把方向值转化成向量值的函数：

```
public Vector3 OriToVec(float orientation)
{
    Vector3 vector = Vector3.zero;
    vector.x = Mathf.Sin(orientation * Mathf.Deg2Rad) * 1.0f;
    vector.z = Mathf.Cos(orientation * Mathf.Deg2Rad) * 1.0f;
    return vector.normalized;
}
```

+   5. 在 Update 函数的开头添加下面两行代码：

```
public virtual void Update ()
{
    if (aRigidBody == null)
    return;
    // ... previous code
```

+   6. 定义 FixedUpdate 函数：

```
public virtual void FixedUpdate()
{
    if (aRigidBody == null)
    return;
    // 下一步
}
```

+   7. 实现 FixedUpdate 函数：

```
Vector3 displacement = velocity * Time.deltaTime;
orientation += rotation * Time.deltaTime;
if (orientation < 0.0f)
    orientation += 360.0f;
else if (orientation > 360.0f)
    orientation -= 360.0f;
// ForceMode取决于你想达到什么效果
// 这里使用VelocityChange 是为了演示
aRigidBody.AddForce(displacement, ForceMode.VelocityChange);
Vector3 orientationVector = OriToVec(orientation);
aRigidBody.rotation = Quaternion.LookRotation(orientationVector,
```

# 实现原理

我们添加了一个成员变量用于保存刚体组件的引用，另外还实现了 FixedUpdate 函