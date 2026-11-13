理减小声音强度。

# 延伸阅读

我们可以开发一个更灵活的算法，通过定义不同类型的墙体来影响声音强度。算法的原理是发射射线，再把墙体的衰减值增加到声音衰减上。

+   1. 创建一个字典，用于保存字符串（使用 tag）类型的墙体，以及它们对应的衰减值：

```
public Dictionary<string, float> wallTypes;
```

+   2. 用这种方式减少声音强度：

```
intensity -= GetWallAttenuation(emitterPos, srPos);
```

+   3. 定义上一步中调用的函数：

```
public float GetWallAttenuation(Vector3 emitterPos, Vector3 receiverPos)
{
    // 这里是下面步骤的代码
}
```

+   4. 计算射线发射需要的值：

```
float attenuation = 0f;
Vector3 direction = receiverPos - emitterPos;
float distance = direction.magnitude;
direction.Normalize();
```

+   5. 发射射线并获取击中的物体：

```
Ray ray = new Ray(emitterPos, direction);
RaycastHit[] hits = Physics.RaycastAll(ray, distance);
```

+   6. 对于每个通过 tag 找到的墙体类型，把它们的值（保存在字典中的）加起来：

```
int i;
for (i = 0; i < hits.Length; i++)
{
    GameObject obj;
    string tag;
    obj = hits[i].collider.gameObject;
    tag = obj.tag;
    if (wallTypes.ContainsKey(tag))
        attenuation += wallTypes[tag];
}
return attenuation;
```

# 6.4 基于碰撞系统的嗅觉函数

嗅觉是从真实世界转化到虚拟世界中最麻烦的感知之一。现在有一些技术可以实现，然而大多数都倾向于用碰撞器或图逻辑实现。