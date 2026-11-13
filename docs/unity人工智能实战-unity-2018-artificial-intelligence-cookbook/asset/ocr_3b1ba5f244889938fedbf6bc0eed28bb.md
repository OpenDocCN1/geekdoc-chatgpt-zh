```
void Start()
{
    particles = new Dictionary<int, GameObject>();
}
```

# 6. 把附加了 OdourParticle 组件的碰撞对象添加到字典中：

```
public void OnTriggerEnter(Collider coll)
{
    GameObject obj = coll.gameObject;
    OdourParticle op;
    op = obj.GetComponent<OdourParticle>();
    if (op == null)
        return;
    int objId = obj.GetInstanceID();
    particles.Add(objId, obj);
    UpdateTarget();
}
```

# 7. 当气味粒子超出 agent 的范围或被销毁时从本地字典中释放它们：

```
public void OnTriggerExit(Collider coll)
{
    GameObject obj = coll.gameObject;
    int objId = obj.GetInstanceID();
    bool isRemoved;
    isRemoved = particles.Remove(objId);
    if (!isRemoved)
        return;
    UpdateTarget();
}
```

# 8. 创建函数，根据当前字典中的元素计算气味 centroid：

```
private void UpdateTarget()
{
    Vector3 centroid = Vector3.zero;
    foreach (GameObject p in particles.Values)
    {
        Vector3 pos = p.transform.position;
        centroid += pos;
    }
    target = centroid;
}
```

# 9. 实现函数，如果有中心点的话，获取气味中心点：

```
public Vector3? GetTargetPosition()
{
    if (particles.Keys.Count == 0)
        return null;
    return target;
}
```

# 运行原理

图 6-3 展示了嗅觉系统的工作原理。