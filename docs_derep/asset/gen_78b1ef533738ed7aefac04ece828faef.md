[PRE0]

# 决策树连续属性切分：寻找最优阈值的实战逻辑

处理连续型特征时，决策树无法像离散值那样直接按类别分叉，核心难点在于**如何确定那个“切一刀”的最佳分割点**。下面这段伪代码 `splitByContinuousAttribute` 就演示了基于**信息增益**最大化原则来寻找最优阈值的完整流程。

## 核心思路：排序后遍历“尝试切分”

算法不盲目猜测，而是先将样本按目标属性**从大到小排序**（`sortReversed`）。想象把所有样本排成一排，我们尝试在相邻两个样本中间“切一刀”，将数据分成左（`setA`）右（`setB`）两堆。

## 关键步骤解析

1.  **初始化基准**：计算原始样本集的**初始熵**（`initialEntropy`），作为后续对比的基线。
2.  **滑动窗口式遍历**：
    *   维护两个集合：`setA`（已划入左侧）、`setB`（待划分右侧，初始含所有样本）。
    *   循环将 `setB` 中属性值**最小**的样本弹出（`pop`），压入 `setA`（`push`）。这相当于将分割线向右微移一步。
    *   **保底约束**：循环条件 `setB.length() > 1` 保证了右侧至少剩 1 个样本，避免出现空分支。
3.  **评价指标——信息增益**：
    *   每次移动后，计算两个子集加权后的**整体熵**（`overallEntropy`）。
    *   **信息增益 = 初始熵 - 整体熵**。增益越大，说明该切分让子集“纯度”提升越明显。
4.  **记录最优解**：实时追踪 `bestGain` 与对应的 `bestSets`，遍历结束后即得到全局最优分割方案。

## 如何算出具体的“分割数值”？

找到最优分组（`bestSets`）后，阈值并非任意取，而是取**两组边界值的中点**：
*   左组 `setA` 最大值（最后一个元素）
*   右组 `setB` 最小值（最后一个元素，因倒序排列）
*   `threshold = (左最大 + 右最小) / 2`

这确保了阈值落在两个不同类别/数值的样本之间，分类边界清晰无歧义。

## 代码还原

```python
function splitByContinuousAttribute(examples, attribute):
    bestGain = 0
    bestSets = null

    # 预处理：倒序排列
    setA = []
    setB = sortReversed(examples, attribute)

    exampleCount = len(examples)
    initialEntropy = entropy(examples)

    # 遍历所有可能的分割点
    while setB.length() > 1:
        # 移动分割线：将最小值样本从右移至左
        setA.push(setB.pop())

        # 计算当前切分质量
        overallEntropy = entropyOfSets(setA, setB, exampleCount)
        informationGain = initialEntropy - overallEntropy

        # 更新全局最优
        if informationGain >= bestGain:
            bestGain = informationGain
            bestSets = [setA, setB]

    # 根据最优分组反推阈值
    setA = bestSets[0]
    setB = bestSets[1]
    threshold = (setA[setA.length()-1].getValue(attribute) + setB[setB.length()-1].getValue(attribute)) / 2

    return bestSets, threshold
```

## 一句话总结

**排序 → 滑动分割线 → 算信息增益 → 取增益最大处的中点为阈值**，这就是决策树处理连续特征的标准动作。理解这段逻辑，就能轻松实现 C4.5 等经典算法的核心切分模块。