
为了保证轨迹的连续性还需要增加机器人的初始状态约束，即

$$\begin{aligned} p(t_s) &= p_0 \\ \dot{p}(t_s) &= v_0 \\ \ddot{p}(t_s) &= a_0 \end{aligned}$$ (9-40)

对于优化目标，通常可以通过对地面作用力求和来优化地面作用力的分配，从而起到优化关节力矩的作用。为了让机器人能够跟随遥控指令，希望在终止时刻机器人的位置尽可能接近期望的位置（通过对遥控进行积分得到）。将此目标纳入优化有一个额外的优势：当机器人处于桩阵的边缘，而操作人员错误地指令机器人继续向边缘运动时，由于动力学等约束的存在，优化结果能够使机器人在边缘运动而不会直接冲出桩阵。方程为

$$J = \omega_1 \| A_p(t_f) - r_{ref}(t_f) \|_2 + \omega_2 \sum_i \sum_j \sum_k \| f_{i,j,k} \|_2$$ (9-41)

式中，$\omega_i$ 为不同部分的权重。

最后，可以通过 MOSEK、GUROBI 等软件对该 MIQP 问题进行求解，在优化质心轨迹的同时也能够优化落脚点的位置。根据优化结果，利用机器人腿部的逆运动学即可计算得到关节的期望轨迹。

## 2. 控制系统框架

图 9-20 所示为宇树 G1 人形机器人梅花桩行走控制系统框图，具体分为以下几个部分：

![](img/47a2b26fde9e2a8fab8f8289e100ee37_265_0.png)

图 9-20 宇树 G1 人形机器人梅花桩行走控制系统框图

- 1) 操作指令。用户或上层系统通过操作指令控制机器人的运动速度及方向。
- 2) 轨迹规划器。接收操作指令后，根据步态信息确定后续腿的接触时序，结合地形的高度图和视觉识别的信息构建 MIQP 问题，求解机器人质心以及关节的运动轨迹。
- 3) 地形高度图和视觉识别。提供环境感知的信息，识别并确定环境中梅花桩的位置、大小和高度等信息。
- 4) 运动控制器。轨迹规划器将计算出的轨迹传递给运动控制器（MPC、WBC 以及 NMPC+WBC 等）。运动控制器根据这些轨迹指令，结合高频状态估计器提供的实时状态，控制机器人执行具体的动作。

- 5) 高频状态估计器。融合视觉定位、码盘、IMU 和相机的等多种传感器数据，实时估计机器人的位置、姿态、线速度和角速度等信息。

- 6) 视觉定位、码盘、IMU、相机。机器人配置的常见传感器。

## 9.3.4 机器人跳跃

跳跃是人形机器人实现复杂运动能力的关键指标之一，在应对障碍物、快速移动以及完成特定任务方面具有显著的优势。这一能力不仅增强了机器人的适应性和灵活性，而且在救援、娱乐、仓储和工业自动化等多个领域展现出了广泛的应用潜力。

在人形机器人跳跃控制方面，常见的方法包括手写轨迹和基于单刚体模型的轨迹优化。然而，这些方法均存在一定的局限性：

- 1) 手写轨迹。人工设计的轨迹往往难以完全契合机器人的动力学特性，尤其在处理复杂运动时，往往难以满足实际需求。

- 2) 基于单刚体模型的轨迹优化。由于单刚体模型无法精确反映关节角度、速度、力矩和地面摩擦等约束条件，可能导致优化结果在实际应用中的性能不尽如人意。

为了克服上述方法的不足，研究者们通常采用基于平面全身动力学模型的多阶段离线轨迹优化技术。这种方法具有以下显著优点：

- 1) 采用平面全身动力学模型。鉴于跳跃动作在矢状面（sagittal plane）具有良好的对称性，利用平面动力学模型可以大幅降低三维全身动力学模型的计算复杂性。此外，平面全身动力学模型能够尽可能地满足机器人的动力学特性，允许将关节角度、速度、力矩和地面摩擦等约束纳入优化问题中，从而确保轨迹的可行性。

- 2) 多阶段轨迹优化。跳跃动作涉及起跳、腾空和落地三个阶段，每个阶段的接触状态不同，对应的动力学模型也有所区别。同时，不同阶段之间的切换涉及不同的动力学转换模型。多阶段轨迹优化能够将整个运动过程视为一个完整的优化问题，确保整个动作的最优性或局部最优性。

### 1. 多阶段离线轨迹优化

通常，一个多阶段轨迹优化问题可以描述成如下形式：

- 1) 优化目标为
- $$J = \sum_{p=1}^{P} J^{(p)} = \sum_{p=1}^{P} [\Phi^{(p)}(\boldsymbol{x}^{(p)}(t_0), t_0, \boldsymbol{x}^{(p)}(t_f), t_f; \boldsymbol{s}^{(p)}) + L^{(p)}(\boldsymbol{x}^{(p)}(t), \boldsymbol{u}^{(p)}(t), t; \boldsymbol{s}^{(p)}) \mathrm{d}t]$$

- 2) 动力学约束为
- $$\dot{\boldsymbol{x}}^{(p)} = \boldsymbol{f}^{(p)}(\boldsymbol{x}^{(p)}, \boldsymbol{u}^{(p)}, t; \boldsymbol{s}^{(p)}) \quad (p = 1, \cdots, P)$$

- 3) 边界约束为
- $$\phi_{\min} \leqslant \boldsymbol{f}^{(p)}(\boldsymbol{x}^{(p)}(t_0), t_0^{(p)}, \boldsymbol{x}^{(p)}(t_f), t_f^{(p)}; \boldsymbol{s}^{(p)}) \leqslant \phi_{\max} \quad (p = 1, \cdots, P)$$

- 4) 路径约束为
- $$\boldsymbol{C}_{\min}^{(p)} \leqslant \boldsymbol{C}^{(p)}(\boldsymbol{x}^{(p)}(t), \boldsymbol{u}^{(p)}(t), t; \boldsymbol{s}^{(p)}) \leqslant \boldsymbol{C}_{\max}^{(p)} \quad (p = 1, \cdots, P)$$

- 5) 连续约束为

\(\boldsymbol{P}^{(s)}(\boldsymbol{x}^{(p_i^s)}(t_f), t_f^{(p_i^s)}; \boldsymbol{s}^{(p_i^s)}, \boldsymbol{x}^{(p_r^s)}(t_0), t_0^{(p_r^s)}; \boldsymbol{s}^{(p_r^s)})=0 \quad (p_i, p_r \in [1, \cdots, P], s=1, \cdots, L) \quad (9-46)\)

式中，\(\boldsymbol{x}^{(p)}(t) \in \mathbf{R}^{n_p}\)、\(\boldsymbol{u}^{(p)}(t) \in \mathbf{R}^{m_p}\)、\(\boldsymbol{s}^{(p)} \in \mathbf{R}^{s_p}\)、\(t \in \mathbf{R}\) 分别为不同阶段 \(p \in [1, \cdots, P]\) 机器人的状态量、控制量、静态参数以及时间；\(n_p\)、\(m_p\)、\(s_p\) 分别为 \(p\) 阶段状态量、控制量和静态参数的个数；\(L\) 为连接约束的个数；\(p_i^s \in [1, \cdots, P](s=1, \cdots, L)\) 和 \(p_r^s \in [1, \cdots, P](s=1, \cdots, L)\)，分别为连接约束的左右两边的阶段编号。

### 2. 平面模型

在机器人跳跃动作中，运动轨迹在矢状面上呈现对称性。基于这一特性，只需采用平面全身动力学模型进行研究，从而避免三维全身动力学模型所带来的复杂计算性。在跳跃过程中，主要的能量产生关节包括 hip_pitch、knee_pitch、ankle_pitch、shoulder_pitch 及 elbow_pitch。因此，可以使用图 9-21 所示的平面模型进行分析。

![](img/47a2b26fde9e2a8fab8f8289e100ee37_267_0.png)

图 9-21 宇树 G1 人形机器人侧面示意图与平面简化模型

定义机器人的广义坐标、广义速度和关节力矩分别为

\(\boldsymbol{q} = (x; z; \boldsymbol{q}_{base}; \boldsymbol{q}_{hip}; \boldsymbol{q}_{knee}; \boldsymbol{q}_{ankle}; \boldsymbol{q}_{shoulder}; \boldsymbol{q}_{elbow}) \\
\dot{\boldsymbol{q}} = (\dot{x}; \dot{z}; \dot{\boldsymbol{q}}_{base}; \dot{\boldsymbol{q}}_{hip}; \dot{\boldsymbol{q}}_{knee}; \dot{\boldsymbol{q}}_{ankle}; \dot{\boldsymbol{q}}_{shoulder}; \dot{\boldsymbol{q}}_{elbow}) \\
\boldsymbol{\tau} = (\tau_{hip}; \tau_{knee}; \tau_{ankle}; \tau_{shoulder}; \tau_{elbow}) \quad (9-47)\)

根据运动学方程，可以计算出每个连杆的质心位置、速度、姿态和角速度，从而得出机器人的总动能（平动和转动）和总势能。然后，利用拉格朗日方程计算出机器人平面动力学模型中的质量矩阵 \(\boldsymbol{H}\)、非线性项 \(\boldsymbol{c}\)、选择矩阵 \(\boldsymbol{S}\)。根据脚掌的几何信息，还可以计算出脚尖和脚后跟的雅各比矩阵 \(\boldsymbol{J}_{toe}\)、\(\boldsymbol{J}_{heel}\)。通过这些雅各比矩阵，可以将脚掌与地面的平面接触简化为脚尖与脚后跟的接触，从而描述机器人在跳跃过程中与地面的作用力。

### 3. 构建优化问题

完整的跳跃过程可以分为起跳阶段、腾空阶段、落地阶段三个阶段。在起跳阶段，机器人通过关节力矩产生足够的垂直和水平推力，实现离地。在腾空阶段，机器人在空中飞行，调整适当的姿态为落地做准备。在落地阶段，机器人与地面发生碰撞，通过屈腿吸收冲击力并迅速恢复平衡。

设定起跳阶段、腾空阶段和落地阶段的编号分别为 1、2、3，总阶段数 \( P = 3 \)，状态量 \( x = [q;\dot{q}] \)，控制量 \( u = \tau \)。需要连续约束的个数 \( L = 2 \)，分别是从起跳阶段到腾空阶段，从腾空阶段到落地阶段。

根据式 (9-42)，通常优化目标包含两部分 \( \Phi^{(p)}(x^{(p)}(t_0), t_0, x^{(p)}(t_f), t_f; s^{(p)}) \) 以及 \( L^{(p)}(x^{(p)}(t), u^{(p)}(t), t; s^{(p)})dt \)，分别为与边界状态相关的迈耶项以及与过程积分相关的拉格朗日项。对于跳跃运动来说，期望通过最小化能量消耗完成跳跃任务来实现机器人的最优跳跃性能，因此可以采用最小化整个过程的力矩二次方和作为优化的目标。那么，优化目标为

```
J = \sum_{p=1}^{P} J^{(p)} = \sum_{p=1}^{P} [\| u \|_2^{(p)}(t)] dt
```

注意，为了方便公式的书写，下述的状态量、控制量及相关变量如果没有特殊说明，均表示同一阶段 \( p \) 下的变量，因此省略上角标 \( ^{(p)} \)。

动力学约束式 (9-43) 描述了模型的动力学特性。根据不同阶段机器人与外界的接触情况，构造不同的动力学方程，那么有

```
H\ddot{q} + c = S^{\top}\tau + J_{\text{toe}}^{\top}f_{\text{toe}} + J_{\text{heel}}^{\top}f_{\text{heel}} \quad (p=1,3)

H\ddot{q} + c = S^{\top}\tau \quad (p=2)
```

当机器人脚尖、脚后跟与地面接触时还应该满足不打滑的约束，即接触点的加速度为 0、速度为 0（初速度为 0 且加速度为 0 即可保证速度为 0），即

```
J_{\text{toe}}\ddot{q} + \dot{J}_{\text{toe}}\dot{q} = 0 \quad (p=1,3)

J_{\text{heel}}\ddot{q} + \dot{J}_{\text{heel}}\dot{q} = 0 \quad (p=1,3)
```

结合式 (9-49) 和式 (9-50)，可以得到不同阶段的动力学约束，即

```
\begin{bmatrix} \ddot{q} \\ f_{\text{toe}} \\ f_{\text{heel}} \end{bmatrix} = \begin{bmatrix} H & -J_{\text{toe}}^{\top} & -J_{\text{heel}}^{\top} \\ J_{\text{toe}} & 0 & 0 \\ J_{\text{heel}} & 0 & 0 \end{bmatrix}^\dagger \begin{bmatrix} S^{\top}\tau - c \\ -J_{\text{toe}}\dot{q} \\ -J_{\text{heel}}\dot{q} \end{bmatrix} \quad (p=1,3)

\ddot{q} = H^{-1}(S^{\top}\tau - c) \quad (p=2)
```

边界约束式 (9-44) 描述了，机器人在每个阶段起始和结束时刻的相关约束。假设给定机器人初始和结束时刻的站立姿态 \( q_{\text{start}} \)、\( q_{\text{finish}} \)，同时所有的关节速度为 0，则边界约束可以描述为

```
x(t_0) = [q_{\text{start}}; 0] \quad (p=1)

x(t_f) = [q_{\text{finish}}; 0] \quad (p=3)
```

路径约束式 (9-45) 描述了，每一个采样时刻下，与机器人状态量和控制量的相关约束。例如以下 5 个约束：

- 1) 关节的角度不应该超过机器人的机械限位。
- 2) 关节的速度不应该超过机器人电动机的速度约束。
- 3) 关节的力矩不应该超过机器人电动机的转矩约束。
- 4) 接触点的地面反作用力应该满足地面摩擦系数决定的摩擦锥约束。
- 5) 除了脚掌以外，机器人其他部分不应与地面发生碰撞。

假设 \( q_{min} \)、\( q_{max} \)、\( \dot{q}_{min} \)、\( \dot{q}_{max} \)、\( \tau_{min} \)、\( \tau_{max} \) 分别为机器人的关节限位、关节速度限制和关节力矩限制，\( p_{link\_ends\_z} \) 包含了所有连杆两端的位置坐标在世界坐标系 \( z \) 轴上的投影（即离地高度），地面摩擦系数为 \( \mu \)，则最终可以将路径约束描述为

```
\( q_{min} \leqslant q(t) \leqslant q_{max} \quad (p=1,2,3) \)
\( \dot{q}_{min} \leqslant \dot{q}(t) \leqslant \dot{q}_{max} \quad (p=1,2,3) \)
\( \tau_{min} \leqslant \tau(t) \leqslant \tau_{max} \quad (p=1,2,3) \)
\( -p_{link\_ends\_z}(t) \leqslant \mathbf{0} \quad (p=1,2,3) \)
\( \mathbf{D}(\mu) \mathbf{f}_{toe}(t) \leqslant \mathbf{0} \quad (p=1,3) \)
\( \mathbf{D}(\mu) \mathbf{f}_{heel}(t) \leqslant \mathbf{0} \quad (p=1,3) \)
\( \mathbf{D}(\mu) = \begin{bmatrix} -1 & -\mu \\ 1 & -\mu \\ 0 & -1 \end{bmatrix} \quad \text{(9-53)} \)
```

连接约束式（9-45）描述了，机器人在不同阶段之间切换时状态量的变化。从起跳阶段到腾空阶段，机器人状态应当保持连续。从腾空阶段到落地阶段，由于存在与地面的碰撞，会导致机器人状态量发生变化。假设碰撞在瞬间完成且为完全非弹性碰撞，那么机器人碰撞前后的广义位置不变，但广义速度发生突变，且碰撞后脚尖与脚后跟的速度为 0。

```
\( \mathbf{H}(\dot{\mathbf{q}}^+ - \dot{\mathbf{q}}) = \mathbf{J}_{toe}^T \mathbf{f}_{toe} + \mathbf{J}_{heel}^T \mathbf{f}_{heel} \quad (p=2, t=t_f) \)
\( \mathbf{J}_{toe} \dot{\mathbf{q}}^+ = 0 \quad (p=2, t=t_f) \)
\( \mathbf{J}_{heel} \dot{\mathbf{q}}^+ = 0 \quad (p=2, t=t_f) \quad \text{(9-54)} \)
```

由此可以计算得到落地之后机器人的关节速度突变后的结果 \( \dot{\mathbf{q}}^+ \) 为

```
\( \begin{bmatrix} \dot{\mathbf{q}}^+ \\ \mathbf{f}_{toe} \\ \mathbf{f}_{heel} \end{bmatrix} = \begin{bmatrix} \mathbf{H} & -\mathbf{J}_{toe}^T & -\mathbf{J}_{heel}^T \\ \mathbf{J}_{toe} & \mathbf{0} & \mathbf{0} \\ \mathbf{J}_{heel} & \mathbf{0} & \mathbf{0} \end{bmatrix}^\dagger \begin{bmatrix} \mathbf{H} \dot{\mathbf{q}} \\ \mathbf{0} \\ \mathbf{0} \end{bmatrix} \quad (p=2, t=t_f) \quad \text{(9-55)} \)
```

从腾空阶段到落地阶段的连续约束为

```
\( \dot{\mathbf{q}}^+ = \dot{\mathbf{q}}^{(3)}(t_s) \quad \text{(9-56)} \)
```

最后，根据现有的非线性优化软件，如 GPOPS，PSOPT，CASADI 等，将优化问题按照软件要求进行封装，即可求解完整的跳跃运动轨迹。

如图 9-22 和图 9-23 所示，期望 G1 机器人从左边的初始站立状态，通过跳跃的方式越过中间的 1.25m 的沟，最终达到右侧的结束站立状态。
