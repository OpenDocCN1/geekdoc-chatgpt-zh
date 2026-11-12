# 2. 实现

现在开发人员可以使用推广应用之后的槽位成本概念来实现分配算法。和前面的实现一样，calculateAssignment 方法是编队管理器类的一部分。

```
class FormationManager
    # ... 其他内容和前面的实现一样 ...
    function updateSlotAssignments():
    # 保存槽位及其相应的成本
    class CostAndSlot:
    cost: float
    slot: int
    # 保存游戏角色的分配难度值及其槽位列表
    class CharacterAndSlots:
    character: Character
    assignmentEase: float
    costAndSlots: CostAndSlot[]
    characterData: CharacterAndSlots[]
    # 编译角色数据
    for assignment in slotAssignments:
    datum = new CharacterAndSlots()
    datum.character = assignment.character
    # 添加每个有效槽位给它
    for slot in 0..pattern.numberOfSlots:
    cost: float = pattern.getSlotCost(assignment.
    character)
    if cost >= LIMIT: continue
    slotDatum = new CostAndSlot()
    slotDatum.slot = slot
    slotDatum.cost = cost
    datum.costAndSlots += slotDatum
    # 添加此槽位到角色的分配难度
    datum.assignmentEase += 1 / (1 + cost)
```