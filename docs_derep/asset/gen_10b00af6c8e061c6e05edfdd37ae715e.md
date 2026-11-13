### 接续攻关：轮廓峰值点的定位与区域切分实战

搞定凹点识别后，咱们得接着琢磨怎么在轮廓上找“峰值点”。中学数学告诉我们，连续光滑曲线 $y=f(x)$ 的峰值对应导数 $f'(x)=0$ 的位置（如图 9.17）。可咱们面对的是离散的多边形顶点序列，根本谈不上连续可导，代数法直接失效。

换个思路：对于轮廓上任意顶点 $P_k$，向量 $v_k, v_{k+1}$ 照常按公式 (9-7)、(9-8) 算出，$v_s$ 代表扫描线方向。
*   **单调区段**（图 9.18 a/b）：$v_k, v_{k+1}$ 落在 $v_s$ 同侧。
*   **转折峰值**（图 9.18 c/d）：$v_k, v_{k+1}$ 分居 $v_s$ 两侧。

**核心结论**：$v_k, v_{k+1}$ 指向 $v_s$ 异侧，正是 $P_k$ 为轮廓峰值点的**充要条件**（感兴趣可自行推导证明）。

怎么用代码判断“异侧”？向量叉乘上场。若 $v_k \times v_s$ 与 $v_{k+1} \times v_s$ 方向相反，即满足：

[PRE0]

（等号照顾了向量平行的情况）

[PRE1]

[PRE2]

---

#### 凹峰点 = 凹点 + 峰值点

回到正题：顶点 $P_k$ 若同时满足凹点判别式 (9-9) 和峰值判别式 (9-12)，它就是**凹峰点**。

前面的推导默认填充倾角为 0（扫描线 $\parallel$ X 轴）。实打实的 3D 打印里倾角千变万化，必须泛化。
*   凹凸性具备**旋转不变性** $\rightarrow$ 式 (9-9) 始终有效。
*   峰值是“相对扫描线法线方向的极值”。倾角非零时，极值方向随法线旋转（图 9.19），但判别逻辑不变 $\rightarrow$ 式 (9-12) 依然管用。

**最终准则**：倾角任意时，$P_k$ 同时满足 (9-9) 与 (9-12) $\Leftrightarrow$ $P_k$ 为凹峰点。

---

#### 从凹峰点到单连通区域：切分策略

遍历轮廓顶点，筛出所有凹峰点，每个点过一条切分线。图 9.20 例中共 3 个凹峰点 $P_1,P_2,P_3$，对应切分线 $l_1,l_2,l_3$。

**切线不能太长**，否则多切出无用分区。实操是：在 $l_1$ 与轮廓的所有交点中，取 $P_1$ 两侧最近的两点 $L_1, R_1$，线段 $L_1R_1$ 即为有效切分段。一次切分：上方生成 2 个单连通区域（区域 1、2），下方剩 1 个多连通区域。递推处理 $P_2, P_3$…… 3 轮下来，原始多连通区域 $\rightarrow$ 7 个单连通区域。

[PRE3]

[PRE4]

**规律总结**：
$$N_s = 2 N_p + 1 \tag{9-13}$$
$N_s$：最终单连通区域数；$N_p$：凹峰点数。

至此，分区原理打通。理论上任意简单多边形多连通区域，给定填充方向均可切分为单连通序列。切分的终极目的是**方便后续串联子区域内的填充线段**。下一节咱们落地代码。

---

### 9.3.3 填充区域分区：工程落地篇

上一节原理对应 5 步走：
1.  输入多边形（须符合“外逆内顺”）。
2.  依据 (9-9)(9-12) 搜集全部凹峰点。
3.  每个凹峰点构造切分线段。
4.  沿切分线段切割多边形，得单连通子区域。
5.  输出子区域序列。

**关于倾角的特殊处理**：步骤里没显式传倾角。生成填充线时，为降低求交难度、提升数值稳定性，倾角非零时会**先旋转轮廓**，使填充线在平面内视作水平。分区属于填充流程一环，放在旋转**之后**执行，故分区阶段默认扫描线水平（倾角 0）。

---

#### 代码逐步拆解（核心类 `SplitRegion` / `SplitRegion.py`）

**步骤 1 & 2：初始化 & 凹峰点猎手 `findTurnPoints`**

```python
class SplitRegion:
    def __init__(self, polygons, adjustPolyDirs=False):
        self.polygons = polygons
        if adjustPolyDirs:               # 需要时校正为“外逆内顺”
            adjustPolygonDirs(self.polygons)
        self.splitPolygons = self.split()

    def findTurnPoints(self):            # 核心筛选逻辑
        vx = Vector3D(1, 0, 0)           # 扫描线方向（X 轴）
        turnPts = []
        for poly in self.polygons:
            pts = poly.points
            n = poly.count()
            for i in range(n - 1):
                # 邻边向量
                v1 = pts[-2 if i == 0 else i-1].pointTo(pts[i])
                v2 = pts[i].pointTo(pts[i+1])
                # 式(9-12)：叉乘 Z 分量异号或为零 → 峰值
                if v1.crossProduct(vx).dz * v2.crossProduct(vx).dz <= 0:
                    # 式(9-9)：叉乘 Z 分量 < 0 → 凹点
                    if v1.crossProduct(v2).dz < 0:
                        turnPts.append(pts[i])
        return turnPts
```
*   `adjustPolyDirs`：保险开关，防输入方向乱套。
*   `findTurnPoints`：双重过滤器，先查峰值（异侧），再查凹性（右转），命中即收录。

---

**步骤 3：切分线段从哪来？`findLRPoints` 精准定左右邻居**

思路：复用 9.1 节 `calcHatchPoints`（多扫描线批量求交）。传入凹峰点 Y 值组成的高度列表，拿回按 X 排好序的二维交点表 `ptses`。

```python
def findLRPoints(self, pt, ptses):       # pt: 凹峰点; ptses: 二维交点表
    for pts in ptses:                    # 按层遍历
        if pts and pts[0].y == pt.y:     # 定中目标高度层
            for i in range(len(pts)-1):  # 利用 X 有序特性
                if pts[i].x < pt.x < pts[i+1].x:
                    return pts[i], pts[i+1]  # 夹住 pt 的正是两侧最近点
    return None, None
```
*   交点已去重、已排序 $\rightarrow$ 夹逼法极简高效。
*   返回 $L, R$ 即可构造切分段 $LR$。

---

**步骤 4：最难的一刀——区域切割用“矩形”代“线段”**

直接拿线段切多边形，得追踪交点在原轮廓上的边索引，极其繁琐（图 9.21：需知交点 A、P、B 对应原轮廓序号 10、20、30 才能拼新轮廓）。多条切分线递归下去更是噩梦。

**奇招：切分线段 $\rightarrow$ 极扁矩形 + 布尔差运算**（图 9.22）。
*   $\delta \to 0^+$，$v_x,v_y$ 为轴向单位向量。
*   线段 $AB$ 扩成矩形 $P_1P_2P_3P_4$：

[PRE6]

$$P_1 = A - \delta v_x - \delta v_y \tag{9-14}$$
$$P_2 = B + \delta v_x - \delta v_y \tag{9-15}$$
$$P_3 = B + \delta v_x + \delta v_y \tag{9-16}$$
$$P_4 = A - \delta v_x + \delta v_y \tag{9-17}$$

*   X 向长度 $\approx |AB|$，Y 向高度 $2\delta \approx 0$ $\rightarrow$ 几何等价于线段切割。
*   多条切分线 = 多个极扁矩形，统统丢给 **Clipper 库**做布尔差，自动输出子多边形，彻底绕开拓扑重构地狱。

---

**收尾两大核心函数：`createSplitter` & `split`**

```python
def createSplitter(self, p1, p2, delta=1.0e-4):  # 极扁矩形工厂
    vx, vy = Vector3D(1,0,0), Vector3D(0,1,0)
    splitter = Polyline()
    splitter.addPoint(p1 - vx*delta - vy*delta)  # P1
    splitter.addPoint(p2 + vx*delta - vy*delta)  # P2
    splitter.addPoint(p2 + vx*delta + vy*delta)  # P3
    splitter.addPoint(p1 - vx*delta + vy*delta)  # P4
    return splitter                              # 闭合多段线即矩形

def split(self):                                 # 总调度
    turn_pts = self.findTurnPoints()             # (2) 找点
    splitters = [self.createSplitter(p, ...) for p in turn_pts]  # (3) 造矩形
    # (4) Clipper 布尔差批量切分
    # (5) 整理输出 self.splitPolygons
    ...
```
*   `delta=1e-4`：经验值，足够“瘦”且避免数值抖动。
*   `split` 串联全流程：找点 $\rightarrow$ 造刀（矩形） $\rightarrow$ 切菜（Clipper） $\rightarrow$ 装盘。

---

**小结**：原理上用“凹峰点”做刀把，工程上用“极扁矩形+布尔运算”做刀刃，把几何拓扑难题转化为成熟库的标准调用。下节咱们把这些单连通区域串起来，跑通完整平行填充路径生成链路。