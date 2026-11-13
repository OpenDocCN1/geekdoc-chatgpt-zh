```
public void RemoveCharacter(GameObject agent)
{
    int index = slotAssignments.FindIndex(x =>
x.character.Equals(agent));
    slotAssignments.RemoveAt(index);
    UpdateSlotAssignments();
}
```

# 11. 实现更新槽的函数:

```
public void UpdateSlots()
{
    GameObject leader = pattern.leader;
    Vector3 anchor = leader.transform.position;
    Vector3 slotPos;
    Quaternion rotation;
    rotation = leader.transform.rotation;
    foreach (SlotAssignment sa in slotAssignments)
    {
    // 这里是下面的步骤
    }
}
```

# 12. 实现 foreach 循环:

```
Vector3 relPos;
slotPos = pattern.GetSlotLocation(sa.slotIndex);
relPos = anchor;
relPos += leader.transform.TransformDirection(slotPos);
Location charDrift = new Location(relPos, rotation);
Character character = sa.character.GetComponent<Character>();
character.SetTarget(charDrift);
```

# 运行原理

FormationPattern 类包含给定的槽的相对位置。例如，根据槽的数量以及它在 360 度中的位置，CircleFormation 子类实现了 GetSlotLocation 类。FormationPattern 是一个基类，因此由管理器添加一个层用于权限和整理队形。通过这种方式，设计者可以专注于由基类派生的简单队形脚本。

如前所述，FormationManager 类管理上层的层级并根据队列的需求和权限把位置排列整齐。计算过程基于队长的位置和旋转值，位置和旋转值则根据模式的原理做转换。

# 延伸阅读

值得一提的是 FormationManager 和 FormationPattern 类是同一个对象的组件。当管理器中的 leader 字段被设置成 null 时，leader 自己就是这个对象本身了。所以，可以用另一个 leader 对象，让检视窗口和类模块清晰明了。

# 其他参考

本方法背后的理论信息，可进一步参考：