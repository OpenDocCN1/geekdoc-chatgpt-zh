# 操作步骤

+   1. 创建 Projectile 类及其成员变量，以便操作物理行为：

```
using UnityEngine;
using System.Collections;

public class Projectile : MonoBehaviour
{
    private bool set = false;
    private Vector3 firePos;
    private Vector3 direction;
    private float speed;
    private float timeElapsed;
}
```

+   2. 定义 Update 函数：

```
void Update ()
{
    if (!set)
        return;

    timeElapsed += Time.deltaTime;
    transform.position = firePos + direction * speed * timeElapsed;
    transform.position += Physics.gravity * (timeElapsed * timeElapsed) / 2.0f;
    // extra validation for cleaning the scene
    if (transform.position.y < -1.0f)
        Destroy(this.gameObject);// or set = false; and hide it
}
```

+   3. 实现 Set 函数，以便射击游戏对象（例如，当这个对象在场景中实例化后调用此函数）：

```
public void Set (Vector3 firePos, Vector3 direction, float speed)
{
    this.firePos = firePos;
    this.direction = direction.normalized;
    this.speed = speed;
    transform.position = firePos;
    set = true;
}
```

# 运行原理

此行为使用高中物理知识来产生抛物线运动。

# 延伸阅读

我们还可以使用使另一种方法：不调用 Set 函数，而是在脚本中实现公有属性，或者把成员变量声明为公有的，在预制件中把脚本设置为默认禁用，在所有属性都初始化后再启用。这样就可以轻松地使用对象池模式了。