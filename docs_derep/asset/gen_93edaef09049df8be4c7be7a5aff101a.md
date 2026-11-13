[PRE0]

**核心数据结构：StlModel 类解析**

`StlModel` 类的核心字段是 `triangles`，用于存放 `Triangle` 类型的三角面片集合。此外，`xMin`、`zMax` 等极值属性记录了模型在 XYZ 三轴上的包围盒边界，初始值均为 0，需调用 `getBounds()` 方法完成计算。针对 STL 文件读取，类内部封装了两个接口：`readStlFile` 直接解析 ASCII 格式文件，仅需传入路径；`extractFromVtkStlReader` 则从 `vtkSTLReader` 对象提取数据，利用 VTK 自身兼容性，间接实现了对二进制 STL 文件的支持。

## 4.2 空间三角形与平面求交核心算法

三维打印路径规划流程繁复，几何层面首要任务是模型姿态优化（影响支撑量），紧接着便是**切片**——用一组平行平面（通常垂直 Z 轴）切割模型，获取闭合交线。同层交线间可能存在包含关系，共同界定打印区域，随后在区域内规划填充轨迹，最后串联各层路径并转为 NC 代码。除主流程外，还涉及支撑生成、凸台处理、路径优化等环节。

[PRE1]

**切片几何本质：平面截三角面片**

STL 模型由离散三角面片构成，平面与模型求交，实则是平面与每个三角面片求交线段，再将线段首尾相连成闭合轮廓。本节重点攻克：给定三角形 `ABC` 与平面 `α(P, N)`（P 为过点，N 为法向），求解交线。

[PRE2]

前序章节已推导线段与平面求交算法，并封装为 `GeomAlgo.py` 中的 `intersectSegmentPlane`。利用该函数分别求三边 `AB`、`BC`、`AC` 与平面 `α` 的交点，再从中筛选两点构成交线段。如图 4.4 所示，交点数可能为 3、2 或 0 个（注：线段与面平行/重合、三角形与面仅顶点接触，均视为无交点）。

[PRE3]

[PRE4]

**交点分类处理逻辑：**

*   **3 个交点**：必含一对重合顶点，取不重合的两点为线段端点。
*   **2 个交点**：若不重合直接成线，重合则视为无交线。
*   **0 个交点**：直接判定无交线。

基于此逻辑，在 `GeomAlgo` 模块实现通用求交函数 `intersectTrianglePlane`。为针对切片场景（Z 轴平面）提效，进一步封装 `intersectTriangleZPlane`：

[PRE5]

| GeomAlgo.py | intersectTriangleZPlane: 空间三角形与 Z 平面快速求交 | 4-4 |
|---|---|---|
| 1 | `......` | `# 前置代码省略` |
| 2 | `def intersectTriangleZPlane(triangle, z):` | `# 输入：Triangle 对象、Z 高度` |
| 3 | `if triangle.zMinPnt().z > z:` | `# 三角形整体在平面上方` |
| 4 | `return None` | `# 直接返回无交线` |
| 5 | `if triangle.zMaxPnt().z < z:` | `# 三角形整体在平面下方` |
| 6 | `return None` | `# 直接返回无交线` |
| 7 | `return intersectTrianglePlane(triangle, Plane.zPlane(z))` | `# 否则调用通用算法求交` |

该函数先利用三角形 Z 方向包围盒（`zMinPnt`、`zMaxPnt`）做快速剔除，仅当平面穿过三角形包围盒时才触发精确求交，显著降低计算开销。

## 4.3 STL 模型批量截交：暴力法实现

切片过程中，每层平面会产出一组线段，需数据结构加以组织。定义 `Layer` 类（位于 `Layer.py`），核心字段含层高 `z`、原始线段列表 `segments`，以及由线段拼合而成的闭合轮廓 `contours`。

[PRE6]

拥有 `Layer` 结构与单三角形求交函数，即可实现 STL 模型全量切片。采用**暴力遍历策略**（`SliceAlgo.py` 中的 `intersectStl_brutal`）：
1.  **确定层高范围**：调用 `StlModel.getBounds()` 获取模型 Z 向极值，层高取值在 `[zMin, zMax]` 之间，步长设为较大值（如 10mm）以便快速验证。
2.  **逐层遍历求交**：对每个层高，遍历模型内**所有**三角面片，调用 `intersectTriangleZPlane` 计算交线。
3.  **结果收集**：将有效线段写入对应 `Layer` 对象。

此法虽未引入空间索引加速，逻辑直观，适合作为基准版本验证切片流程正确性。后续将探讨加速策略。

[PRE7]

| SliceAlgo.py | intersectStl_brutal: STL 模型暴力切片获取线段 | 4-6 |
|---|---|---|