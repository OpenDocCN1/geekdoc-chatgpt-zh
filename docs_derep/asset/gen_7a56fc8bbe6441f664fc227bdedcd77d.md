# 决策树节点的多态实现解析

[PRE0]

## 核心接口定义

任何决策树节点都需遵循基础契约：提供数值检测 `testValue`、分支选取 `getBranch` 以及递归执行 `makeDecision` 三大核心能力。

[PRE1]

## 区间判断节点：FloatDecision

此类节点持有 `minValue` 与 `maxValue` 两个阈值。其分支逻辑直观——仅当检测值落在闭区间 `[minValue, maxValue]` 内时走向 `trueNode`，否则转向 `falseNode`。

[PRE2]

## 基础决策逻辑：Decision 类

封装了通用的递归决策流程：先通过 `getBranch` 确定下一步走向，随即调用该分支的 `makeDecision` 继续深入，直至触达叶子节点。

[PRE3]

## 多路分发节点：MultiDecision

维护一个子节点数组 `daughterNodes`。检测函数返回整型索引，`getBranch` 直接按索引从数组中取出对应子节点，实现一对多的灵活分发。

[PRE4]

## 随机决策节点：RandomDecision

引入帧级缓存机制避免抖动：`lastFrame` 记录上次决策帧，`currentDecision` 保存布尔结果。仅当帧号推进（`frame > lastFrame + 1`）时才重新掷骰子（`randomBoolean()`），其余帧沿用旧决策，最后统一更新 `lastFrame`。

[PRE5]

## 带超时的随机决策：RandomDecisionWithTimeOut

在随机决策基础上增加强制刷新机制。新增 `timeOut` 与 `timeOutFrame`：若距上次决策超过 1 帧 **或** 当前帧触及 `timeOutFrame`，即强制生成新决策并把 `timeOutFrame` 后推 `timeOut` 帧。这样既保留了逐帧稳定性，又能保证长周期内必然刷新。