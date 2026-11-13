[PRE0]

**BehavioralLOD：基于重要性的行为动态切换机制**

`BehavioralLOD` 继承自 `Behavior`，核心职责是维护一组行为记录（`records`），并根据当前重要性（`importance`）动态决定执行哪个行为。它持有当前激活的行为引用（`current`），在每一帧或每个时间步调用 `run(time)` 时，会执行一套“校验-切换-执行”的标准流程。

**核心逻辑拆解：**

1.  **有效性守卫与寻找替代者**
   方法首先检查 `current` 是否存在且 `current.isValid(importance)` 返回真。若当前行为失效（或为空），便进入查找模式：顺序遍历 `records` 列表，寻找第一个满足 `record.isValid(importance)` 的记录作为 `next`。这实现了一个基于优先级（列表顺序）的降级/选择策略。

2.  **生命周期回调：平滑过渡**
   找到 `next` 后，并非简单赋值，而是通过回调处理上下文切换：
   *   **退出通知**：若旧行为存在且定义了 `exit` 回调，调用 `current.exit(next.behavior)`，告知旧行为“即将切换到谁”。
   *   **进入通知**：若新行为存在且定义了 `enter` 回调，调用 `next.enter(current.behavior)`，告知新行为“从哪里来”。
   这种双向通知机制保证了行为切换时状态的连贯性（如清理资源、初始化参数、平滑动画过渡）。

3.  **状态提交与执行**
   将筛选出的 `next` 赋值给 `current`（若未找到有效记录，`current` 可能变为 `null`）。最后，若 `current` 有效，调用 `current.behavior.run(time)` 执行实际逻辑。

**设计亮点：**
*   **LOD（细节层次）思想**：通过 `importance` 参数动态筛选，高重要性时执行复杂/高保真行为，低重要性时自动降级至简单/低开销行为，极适合大规模 AI 或远距离实体的性能优化。
*   **解耦**：`BehavioralLOD` 不关心具体行为内部逻辑，仅管调度与生命周期，符合单一职责原则。

[PRE1]

1 | class Behavior: