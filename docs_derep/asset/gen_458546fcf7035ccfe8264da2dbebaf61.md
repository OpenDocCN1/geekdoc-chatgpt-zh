### 9.3.3 梅花桩行走的轨迹优化与控制架构

**质心动力学建模与轨迹参数化**

机器人的质心动力学方程可写作：

\( \begin{bmatrix} m(\ddot{\boldsymbol{p}}-\boldsymbol{g}) \\ m\hat{\dot{\boldsymbol{p}}}(\dot{\boldsymbol{p}}-\boldsymbol{g})+\dot{\boldsymbol{L}} \end{bmatrix} = \sum_{i=1}^{N_c} \begin{bmatrix} \mathbf{I}_{3 \times 3} \\ \hat{\boldsymbol{r}}_i \end{bmatrix} \boldsymbol{f}_i = \boldsymbol{Gf} \) (9-35)

为便于优化，将质心位置轨迹用高阶多项式参数化表达：

\( \boldsymbol{p}=\boldsymbol{p}_0+\boldsymbol{A}_p \boldsymbol{c} \quad \dot{\boldsymbol{p}}=\boldsymbol{A}_v \boldsymbol{c} \quad \ddot{\boldsymbol{p}}=\boldsymbol{A}_a \boldsymbol{c} \) (9-36)

其中 \(\boldsymbol{c}\) 为轨迹决策变量，\(\boldsymbol{A}_p, \boldsymbol{A}_v, \boldsymbol{A}_a\) 仅取决于采样时刻的系数矩阵。将 (9-36) 代入 (9-35) 并省略高阶微小项与转动项后，得到线性化的近似关系：

\( \begin{bmatrix} m(\ddot{\boldsymbol{p}}-\boldsymbol{g}) \\ m\hat{\dot{\boldsymbol{p}}}(\dot{\boldsymbol{p}}-\boldsymbol{g})+\dot{\boldsymbol{L}} \end{bmatrix} \approx \begin{bmatrix} m\boldsymbol{A}_a \\ m\hat{\dot{\boldsymbol{p}}}_0 \boldsymbol{A}_a + m\hat{\boldsymbol{g}} \boldsymbol{A}_p \end{bmatrix} \boldsymbol{c} + \begin{bmatrix} -m\boldsymbol{g} \\ m\hat{\dot{\boldsymbol{p}}}_0 \end{bmatrix} = \sum_{i=1}^{N_c} \begin{bmatrix} \mathbf{I}_{3 \times 3} \\ \hat{\boldsymbol{r}}_i \end{bmatrix} \boldsymbol{f}_i = \boldsymbol{Gf} \) (9-37)

式 (9-37) 中的所有系数均可由当前机器人状态与环境信息直接计算，从而构建二次规划 (QP) 问题。

**落脚点选择与接触建模**

以图 9-19 场景为例，机器人面临多条路径与落脚点组合。设总规划时长为 \(T\)，采样点数 \(N_s\)，采样间隔 \(\Delta t = T/N_s\)。第 \(i\) 条腿有 \(N_i\) 个候选落脚点，第 \(j\) 个候选点的位置、单位外法向分别为 \(\boldsymbol{r}_{i,j}, \boldsymbol{n}_{i,j}\)。布尔变量 \(b_{i,j,k}\) 表示腿 \(i\) 在第 \(k\) 采样时刻是否踩在候选点 \(j\) 上；\(\boldsymbol{f}_{i,j,k}\) 为对应的地面反作用力；\(c_{i,k}\in\{0,1\}\) 为已知的接触时序（1 表示着地，0 表示摆动）。索引范围：\(1\le i\le 4\)，\(1\le j\le N_i\)，\(0\le k\le N_s\)。

采样时刻 \(k\) 下腿 \(i\) 的足端位置由候选点加权求和给出：

\( \boldsymbol{r}_{i,k} = \sum_{j=1}^{N_i} b_{i,j,k} \boldsymbol{r}_{i,j,k} \quad \text{s.t.} \quad \sum_{j=1}^{N_i} b_{i,j,k} = c_{i,k},\; c_{i,k}\in\{0,1\} \) (9-38)

若处于接触期（\(c_{i,k}=1\)），\(\boldsymbol{r}_{i,k}\) 等于被选中候选点的位置；否则为零向量。约束 \(\sum_j b_{i,j,k}=c_{i,k}\) 保证同一时刻足端仅出现在唯一位置。

为防止打滑，接触力需满足摩擦锥约束：

\( \boldsymbol{D}(\boldsymbol{n}_{i,j}, \mu) \boldsymbol{f}_{i,j,k} \leqslant \boldsymbol{f}_{lim} \) (9-39)

其中 \(\boldsymbol{D}(\boldsymbol{n}_{i,j}, \mu)\) 由接触法向与摩擦系数 \(\mu\) 构造。

**初始状态与优化目标**

轨迹连续性要求加入初始状态约束：

[PRE0] (9-40)

优化目标通常通过最小化地面反力的二次和来间接优化关节力矩。同时，为使机器人跟随遥控指令，在终止时刻让质心位置逼近期望位置（由遥控速度积分得到）。该目标还能在机器人处于桩阵边缘且收到错误向边缘外指令时，利用动力学约束自然地将轨迹限制在安全区域内。完整目标函数为：

[PRE1] (9-41)

式中 \(\omega_i\) 为各项权重。

最终得到的混合整数二次规划 (MIQP) 可用 MOSEK、GUROBI 等求解器高效求出，同步得到质心轨迹与最优落脚点。再经腿部逆运动学即可生成关节期望轨迹。

#### 2. 控制系统框架

图 9-20 展示了宇树 G1 人形机器人梅花桩行走的控制系统框图，主要包含以下模块：

[PRE2]

**图 9-20 宇树 G1 人形机器人梅花桩行走控制系统框图**

- **操作指令**：用户或上层系统下发期望运动速度与方向。
- **轨迹规划器**：接收指令后，依据步态确定未来腿部接触时序，融合地形高度图与视觉识别信息构建 MIQP 问题，求解质心与关节轨迹。
- **地形高度图与视觉识别**：提供环境感知，提取梅花桩的位置、尺寸、高度等几何信息。
- **运动控制器**：接收规划轨迹，结合高频状态估计器的实时状态反馈，通过 MPC、WBC 或 NMPC+WBC 等算法输出关节级控制指令。
- **高频状态估计器**：融合视觉定位、码盘、IMU、相机等多源传感器数据，实时估计位置、姿态、线/角速度。
- **传感器套件**：视觉定位、码盘、IMU、相机等常用硬件。

---

#### 9.3.4 机器人跳跃运动优化

跳跃是人形机器人展现复杂动态能力的关键指标，能显著提升越障、快速位移与特定任务完成能力，在救援、娱乐、仓储、工业自动化等领域潜力巨大。

现有跳跃控制方法主要有两类，均存在短板：

- **手工轨迹**：人工设计难以完美贴合机器人动力学特性，复杂动作下往往力有不逮。
- **单刚体模型优化**：忽略关节角度、速度、力矩及地面摩擦等约束，导致优化轨迹在实机上难以执行。

为突破上述瓶颈，主流研究转向**基于平面全身动力学模型的多阶段离线轨迹优化**，其核心优势在于：

1. **平面全身动力学模型**：跳跃动作在矢状面近似对称，平面模型大幅降低 3D 全身动力学的计算复杂度，同时保留关节级约束（角度/速度/力矩/摩擦），保证轨迹物理可行性。
2. **多阶段优化框架**：跳跃包含起跳、腾空、落地三阶段，接触状态与动力学模型各异，阶段间存在动力学转换。多阶段优化将全过程视为统一问题，保证整体最优或局部最优。

##### 1. 多阶段离线轨迹优化的一般形式

一个标准的多阶段优化问题包含以下要素：

- **优化目标**  
  [PRE3]

- **动力学约束**  
  [PRE4]

- **边界约束**  
  [PRE5]

- **路径约束**  
  [PRE6]

- **阶段连续性约束**  

\(\boldsymbol{P}^{(s)}(\boldsymbol{x}^{(p_i^s)}(t_f), t_f^{(p_i^s)}; \boldsymbol{s}^{(p_i^s)}, \boldsymbol{x}^{(p_r^s)}(t_0), t_0^{(p_r^s)}; \boldsymbol{s}^{(p_r^s)})=0 \quad (p_i, p_r \in [1, \cdots, P], s=1, \cdots, L) \quad (9-46)\)

符号说明：\(\boldsymbol{x}^{(p)}(t)\in\mathbf{R}^{n_p}\)、\(\boldsymbol{u}^{(p)}(t)\in\mathbf{R}^{m_p}\)、\(\boldsymbol{s}^{(p)}\in\mathbf{R}^{s_p}\)、\(t\in\mathbf{R}\) 分别为第 \(p\) 阶段的状态、控制、静态参数与时间；\(n_p,m_p,s_p\) 为对应维度；\(L\) 为连接约束数；\(p_i^s, p_r^s\) 为第 \(s\) 个连接约束左右两侧的阶段编号。

##### 2. 平面简化模型

利用跳跃在矢状面的对称性，仅建立平面模型（图 9-21），规避 3D 全身模型的繁重计算。主要发力关节为髋/膝/踝/肩/肘的俯仰关节。

[PRE7]

**图 9-21 宇树 G1 人形机器人侧面示意图与平面简化模型**

定义广义坐标、广义速度与关节力矩：

\(\boldsymbol{q} = (x; z; \boldsymbol{q}_{base}; \boldsymbol{q}_{hip}; \boldsymbol{q}_{knee}; \boldsymbol{q}_{ankle}; \boldsymbol{q}_{shoulder}; \boldsymbol{q}_{elbow}) \\

\dot{\boldsymbol{q}} = (\dot{x}; \dot{z}; \dot{\boldsymbol{q}}_{base}; \dot{\boldsymbol{q}}_{hip}; \dot{\boldsymbol{q}}_{knee}; \dot{\boldsymbol{q}}_{ankle}; \dot{\boldsymbol{q}}_{shoulder}; \dot{\boldsymbol{q}}_{elbow}) \\

\boldsymbol{\tau} = (\tau_{hip}; \tau_{knee}; \tau_{ankle}; \tau_{shoulder}; \tau_{elbow}) \quad (9-47)\)

通过运动学方程可得各连杆质心位置、速度、姿态与角速度，进而计算总动能（平动+转动）与总势能。应用拉格朗日方程导出平面动力学的质量矩阵 \(\boldsymbol{H}\)、非线性项 \(\boldsymbol{c}\)、选择矩阵 \(\boldsymbol{S}\)。结合脚掌几何，求得脚尖与脚后跟雅可比 \(\boldsymbol{J}_{toe}, \boldsymbol{J}_{heel}\)，将足底平面接触简化为两点接触，从而描述跳跃过程中的地面作用力。

##### 3. 优化问题的具体构建

完整跳跃分为三阶段：  
**阶段 1 起跳** —— 关节输出力矩产生垂直/水平推力实现离地；  
**阶段 2 腾空** —— 空中飞行，调整姿态准备着陆；  
**阶段 3 落地** —— 触地冲击，屈腿缓冲并快速稳定。

编号为 \(P=3\)，状态 \(\boldsymbol{x}=[\boldsymbol{q};\dot{\boldsymbol{q}}]\)，控制 \(\boldsymbol{u}=\boldsymbol{\tau}\)。连续约束数 \(L=2\)，分别对应起跳→腾空、腾空→落地。

依据 (9-42)，目标函数含 Mayer 项 \(\Phi^{(p)}(\cdot)\) 与 Lagrange 项 \(\int L^{(p)}(\cdot)dt\)。为实现最优跳跃性能，采用**最小化全过程关节力矩二次和**作为目标：

[PRE8]

> 注：为简化记号，下文若无特殊说明，状态/控制/相关变量均指同一阶段 \(p\) 内的量，省略上标 \(^{(p)}\)。

**动力学约束 (9-43)** 刻画各阶段模型特性。根据接触状态不同，列写对应动力学方程：

[PRE9]

当脚尖/脚后跟接地时，还需施加**非滑移约束**：接触点加速度为零、速度保持零（初速度为零且加速度为零即隐含速度为零）：

[PRE10]

综合 (9-49) 与 (9-50) 即可得到各阶段的完整动力学约束：