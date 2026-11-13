
世界坐标系相同，但原点位于质心)。中心动量与关节角速度的关系为

```
h = A(q)v
```

求导并整理有

```
A(q)\dot{v} = \dot{h} - \dot{A}(q)v
```

该任务最终表示为

```
A = A(q), b = \dot{h} - \dot{A}(q)v
```

### 5. 人形机器人站立控制

为实现人形机器人站立，设计以下 3 种任务：

- 1) 所有接触点加速度为 0。
- 2) 中心动量变化率为 \(\dot{I}_{des} = K_p(\text{CoM}_{des} - \text{CoM}_m) + K_D(0 - I_m / m)\)。
- 3) 腿部以外的关节角加速度为 \(\dot{v}_{des} = K_p(q_{des} - q_m) + K_D(v_{des} - v_m)\)。

人形机器人站立控制的全身控制的二次规划问题最终表示为

```
\min_{\dot{v}, u, f_c} \sum_{i \in \text{站立}} (A_i v - b_i)^T W_i (A_i v - b_i) + W_\tau \| \tau \|^2
\text{s.t. } M(q)\dot{v} + c(q, \dot{q}) = u + J_c^T(q)f_c
f_c \in \text{摩擦锥}
u_{min} \leqslant u \leqslant u_{max}
```

采用权重形式的全身控制，即把一些任务作为目标函数，并赋予不同的权重 \(W_i\)，这样可以同时处理更多的任务，避免任务之间的冲突导致问题无解，所以只保留了动力学约束、摩擦力约束和力矩范围约束。最终宇树 G1 人形机器人的站立效果如图 9-13 所示。

![](img/47a2b26fde9e2a8fab8f8289e100ee37_258_0.png)

图 9-13 宇树 G1 人形机器人的站立效果

## 9.3.2 机器人行走

### 1. 人形机器人行走规划

在人形机器人平衡站立的基础上，通过上层的步态行为规划来实现人形机器人的行走功能，其控制流程框图如图 9-14 所示。

![](img/47a2b26fde9e2a8fab8f8289e100ee37_259_0.png)

图 9-14 人形机器人行走控制流程框图

### 2. 简化模型

上层规划采用简化模型，即倒立摆模型。把人形机器人抽象为一个倒立摆模型（见图 9-15），其质量位于质心，和地面只有一个接触点位于压力中心。

进一步假设倒立摆高度保持恒定为 \( z_{fixed} \)，只考虑倒立摆模型在水平面的动力学，有

\(\ddot{r} = \omega_0^2(r - \text{CoP})\) (9-23)

式中， \( r \) 为质心的水平分量； \( \omega_0 = \sqrt{\frac{g}{z_{fixed}}} \) 。通过定义瞬时捕获点

\( r_{icp} = r + \frac{\dot{r}}{\omega_0} \) ，倒立摆动力学可以重新表述为

\(\dot{r}_{icp} = \omega_0(r_{icp} - \text{CoP})\) (9-24)

不难发现式（9-24）有解析解，通过设计合理的中心压力（对应人形机器人落脚点位置）来生成期望的瞬时捕获点轨迹。

### 3. 步态规划

人形机器人的步态规划内容包括，质心轨迹、落脚点位置和摆动腿轨迹。

结合倒立摆模型的动力学特性，生成符合人形机器人步态约束的落脚点位置和质心轨迹。采用 MPC 在预期落脚点和步态规划器计算的步态时间的基础上，规划出动态可行的水平质心轨迹。MPC 被构建为一个稀疏二次规划问题，联合优化水平质心和中心压力轨迹。

简单起见，采用固定的步态时序（见图 9-16）。根据物理规律，倒立摆模型的质心水平分量 \( x_{CoM} = [x_{CoM} \quad y_{CoM}]^T \) 的变化遵循下式：

\(\ddot{x}_{CoM} = \omega_0^2 (x_{CoM} - x_{CoP})\) \\
\(\ddot{y}_{CoM} = \omega_0^2 (y_{CoM} - y_{CoP})\) (9-25)

![](img/47a2b26fde9e2a8fab8f8289e100ee37_259_1.png)

图 9-15 人形机器人抽象为倒立摆模型

式中， \( x_{\text{CoP}} = [x_{\text{CoP}} \quad y_{\text{CoP}}]^T \)，为地面上中心压力的水平分量。

![](img/47a2b26fde9e2a8fab8f8289e100ee37_260_0.png)

图 9-16 人形机器人站立与行走步态的时序

在预测出质心高度和机器人角动量后，可以在一个长度为 \( T \) 的预测窗口内对这些方程进行积分。这里使用 0.02s 的积分步长对预测窗口内的水平质心轨迹进行数值积分。预测的支撑点被转换为一系列 \( K \) 个凸支撑多边形及其在预测窗口中的相应时间间隔。为了确保动态可行的计划，中心压力必须始终位于活动支撑区域内。这是通过在每个支撑间隔的中点优化两个中心压力（一个位于其中点，受限于活动支撑多边形；另一个位于其末端，受限于活动支撑和下一个的交集）来实现的。通过线性插值得到的 \( N=2K \) 个点，保证了中心压力始终在凸支撑区域内。联合优化中心压力为

```
\( z = [x_{\text{CoP,0}} \quad y_{\text{CoP,0}} \quad x_{\text{CoP,1}} \quad y_{\text{CoP,1}} \quad \dots \quad x_{\text{CoP,N}} \quad y_{\text{CoP,N}}]^T \) (9-26)
```

预测窗口末端的最终水平质心位置和速度为

```
\( \xi_T = [x_{\text{CoM,T}}^T \quad \dot{x}_{\text{CoM,T}}^T]^T \) (9-27)
```

同样，采用优化观点把 CoM 和 CoP 规划表为一个二次规划问题，得

```
\(\min_{z, \xi_T} (A_T \xi_T - c_T)^T W_T (A_T \xi_T - c_T) + (z-c)^T W_c (z-c) + (Fz)^T W_v (Fz)\)
\(\text{s.t.} \quad x_{\text{CoP,0}} = x_{\text{CoP}}^i\)
\(x_{\text{CoM,T}} + A_p z + b_p = x_{\text{CoM}}^i\)
\(x_{\text{CoM,T}} + A_v z + b_v = \dot{x}_{\text{CoM}}^i\)
\(x_{\text{CoP,n}} \in C_n \quad \forall n \in [0, N-1]\) (9-28)
```

式中，\( A_T \xi_T \) 为捕获点。第一个目标是使最终捕获点接近最后支撑多边形的质心 \( c_T \)。目标的第二项鼓励每个 CoP 接近其各自约束多边形的质心。目标的第三项惩罚较大的 CoP 速度，\( F \) 是有限差分矩阵。\( W_T \)、\( W_c \) 和 \( W_v \) 是相应的目标权重矩阵。为了确保控制输出的连续性，等式约束包括初始 CoP 位置以及初始水平质心位置 \( x_{\text{CoM}}^i \) 和速度 \( \dot{x}_{\text{CoM}}^i \)。矩阵 \( A_p \)、\( A_v \)、\( b_p \)、\( b_v \) 是根据方程为质心的离散反向时间积分矩阵，这使在优化最终质心 \( x_{\text{CoM,T}} \) 时能够约束初始质心。最后的约束确保每个 CoP 位于其各自的凸约束多边形 \( C_n \) 内。

图 9-17 所示的 CoM 和 CoP 轨迹是基于倒立摆模型优化的结果。MPC 在每次步态规划器更新时进行评估，使用的预测窗口为 \( T = 2\text{s} \)。

足端轨迹：在得到 CoP 落脚点位置的基础上，采用插值的方式得到足端摆动轨迹。以宇树人形机器人 G1 足端为例，其脚掌有 6 个自由度，分别是 3 自由度位置和 3 自由度姿态。下面分别对位置和姿态进行插值。

![](img/47a2b26fde9e2a8fab8f8289e100ee37_261_0.png)

- (1) 位置插值 对水平分量和竖直分量用不同的方法进行插值，确保水平分量的速度起始和终端为零，竖直分量采用抛物线拟合。归一化时间 \( t \in [0,1] \) 后插值计算方式为

```
\(\begin{aligned} x &= \frac{1}{2}[(1+\cos(\pi t))x_s + (1-\cos(\pi t))x_f] \\ y &= \frac{1}{2}[(1+\cos(\pi t))y_s + (1-\cos(\pi t))y_f] \\ z &= z_{\max} - z_{\max}(2t-1)^2 \end{aligned}\)
(9-29)
```

- (2) 姿态插值 采用球面线性插值（Spherical Linear Interpolation，SLERP），是四元数的一种线性插值运算，主要用于在两个表示旋转的四元数之间平滑插值。

```
\(\text{slerp}(q_s, q_f, t) = \frac{q_s \sin[(1-t)\theta] + q_f \sin(t\theta)}{\sin \theta}\) (9-30)
```

式中， \( \theta = \arccos(q_s \cdot q_f) \)。当 \( q_s \) 和 \( q_f \) 夹角非常小时，如脚掌一直保持水平时，需要采用 \( \sin \theta \approx 0 \) 代替，球面线性插值退化为线性插值，即

```
\(\text{slerp}(q_s, q_f, t) = (1-t)q_s + tq_f\) (9-31)
```

### 4. 人形机器人行走控制

为实现人形机器人行走，基于站立控制的任务类型重新设计以下 4 种任务：

- 1) 所有接触点加速度为 0；
- 2) 中心动量变化率更新为 \(\dot{\boldsymbol{I}}_{\text{des}} = \boldsymbol{K}_{\text{P}}(\text{CoM}_{\text{ref}} - \text{CoM}) + \boldsymbol{K}_{\text{D}}(\boldsymbol{I}_{\text{ref}} - \boldsymbol{I}/m)\)。其中， \(\text{CoM}_{\text{ref}}\) 为步态规划的质心轨迹， \(\boldsymbol{I}_{\text{ref}}\) 为步态规划输出的参考中心动量。
- 3) 腿部以外的关节角加速度为 \(\dot{\boldsymbol{v}}_{\text{des}} = \boldsymbol{K}_{\text{P}}(\boldsymbol{q}_{\text{des}} - \boldsymbol{q}_m) + \boldsymbol{K}_{\text{D}}(\boldsymbol{v}_{\text{des}} - \boldsymbol{v}_m)\)。
- 4) 摆动腿末端空间加速度采用步态规划输出的足端轨迹参考值，并结合双测地线 PD 控制器。人形机器人行走 WBC 的二次规划问题最终表示为

```
\min_{v,u,f_c} \sum_{i \in 步态} (A_i v - b_i)^T W_i (A_i v - b_i) + W_\tau \| \tau \|^2 \\ \text{s.t. } M(q)\dot{v} + c(q,\dot{q}) = u + J_c^T(q) f_c \quad (9-32) \\ f_c \in 摩擦锥 \\ u_{\min} \leqslant u \leqslant u_{\max}
```

宇树 G1 人形机器人的行走效果如图 9-18 所示。

![](img/47a2b26fde9e2a8fab8f8289e100ee37_262_0.png)

图 9-18 宇树 G1 人形机器人的行走效果

可以发现，人形机器人行走和站立对应的优化问题形式完全一样，说明这套控制框架有较好的扩展性，后面也能适用于其他任务。本节采用了较简单的倒立摆模型来规划质心和落脚点轨迹，也可以用其他更精确的模型，如第 3 章介绍的单刚体模型或中心动力学模型，甚至 9.3.4 节介绍的完整动力学模型。采用的模型越精确，规划出的轨迹越符合实际机器人动力学特性。但是通常会花费更多的时间来求解，因此需要权衡模型精度和求解时间，针对不同任务选择合适的模型。

### 5. 软件工具

人形机器人行走和站立控制算法涉及在线计算多刚体动力学、运动学、二次规划求解等。依托开源社区的发展，目前可以利用开源工具开发算法。这里介绍动力学、优化问题构建和二次规划求解的相关工具。

Pinocchio 是一个用于高效计算机器人模型或任何多连杆刚体模型动力学（及其导数）的库。Pinocchio 是计算多连杆刚体动力学最有效的库之一。它实现了根据 Featherstone 于 2008 年提出的经典算法，并引入了一些这些算法的高效变体和一些新的算法，特别是包括一整套计算主要算法导数的算法。Pinocchio 是开源的，主要用 C++ 编写，并提供 Python 接口，具有伯克利软件分发（Berkeley Software Distribution，BSD）许可证。

CasADi 是一个用于数值优化的符号框架，能够在稀疏矩阵值计算图上实现正向和反向模式的自动微分。它支持生成 C 代码，并能直接调用如算子分裂法二次规划求解器（Operator Splitting Quadratic Programming，OSQP）、内点法最优化求解器（Interior Point Optimizer，IPOPT）等优化求解器。它支持 C++、Python 或 Matlab 等编程语言。

OSQP 是一个用于二次规划求解的优化求解器。它使用 C++ 编写并提供 Python 接口，底层采用 ADMM 算法。

以人形机器人控制为例，可以用 Pinocchio 计算机器人动力学方程，然后用 CasADi 构建二次规划问题，最后调用 OSQP 进行求解。

## 9.3.3 机器人复杂地形行走

在现实环境中，机器人常需要在不平坦、不规则的地形上行走，如救灾现场、建筑工地、户外探索等。具备适应复杂地形行走能力的机器人，可以大大扩展应用场景，提高实用性和可靠性。本节将以梅花桩场景（见图 9-19）为例，详细介绍机器人在复杂地形中的轨迹规划及控制方法。

![](img/47a2b26fde9e2a8fab8f8289e100ee37_263_0.png)

图 9-19 梅花桩场景示例

在梅花桩场景中，视觉感知系统扮演着至关重要的角色。机器人需要通过视觉传感器（如摄像头、深度摄像头等）实时获取周围环境的信息，识别梅花桩的位置和高度，以及其他潜在的障碍物和地形特征。这些信息是机器人进行轨迹规划的基础。识别出的梅花桩信息包括中心点位置 p、最大内接圆半径 r、高度 h 以及梅花桩表面的单位外法向量 n。为了保证运动的安全性，通常取足够大的梅花桩的中心点作为机器人的期望落脚点。

### 1. 运动轨迹规划

在现有的方法中，常根据机身速度计算机器人未来的名义落脚点，然后在名义落脚点附近选择最近的可选落脚点。这种方法无法保证落脚点选取的最优性。在质心运动轨迹的生成上，通常根据遥控指令及机器人当前状态进行积分，获得近似的质心轨迹。这种方法未考虑机器人的动力学特性，可能导致轨迹不合理，进而影响后期的轨迹跟踪效果。

本节将通过混合整数二次规划（Mixed-Integer Quadratic Programming，MIQP）来实现落脚点选取及机器人质心轨迹的优化。MIQP 能够同时处理二次规划中的连续性问题和整数规划中的离散性问题。在梅花桩这样复杂的地形上，机器人的落脚点受到梅花桩位置的限制，仅有有限的离散落脚点可供选择，同时还需要优化机器人连续的质心运动轨迹。MIQP 的应用可以有效解决这一问题，确保机器人在复杂地形上的运动安全性和轨迹合理性。

首先对机器人的动力学模型进行简化。完整的动力学方程为

```
\(\ddot{H}q+c=S^{T}\tau+J^{T}f\)
```

式中， \(q \in R^{N_G}\)、 \(H \in R^{N_G \times N_G}\)、 \(c \in R^{N_G}\)、 \(S \in R^{N_J \times N_G}\)、 \(\tau \in R^{N_J}\)、 \(N_J\)、 \(N_G = N_J + 6\) 分别为广义坐标、惯量矩阵、非线性项、选择矩阵、关节力矩、真实关节个数、广义坐标数； \(J=[J_1^T J_2^T \cdots J_{N_c}^T]^T \in R^{3N_c \times N_G}\) 和 \(f=[f_1^T f_2^T \cdots f_{N_c}^T]^T \in R^{3N_c}\) 分别为世界系下接触点的雅各比矩阵和地面作用力， \(N_c\) 为接触点个数。如果忽略腿部影响，可将式 (9-33) 简化为质心动力学，即

```
\(\begin{bmatrix} m(\ddot{p}-g) \\ \dot{L} \end{bmatrix} = \sum_{i=1}^{N_c} \begin{bmatrix} I_{3\times3} \\ \hat{r}_i - \hat{p} \end{bmatrix}\)
```

式中，\( m \)、\( \boldsymbol{p} \in \mathbf{R}^3 \)、\( \boldsymbol{g} \in \mathbf{R}^3 \)、\( \boldsymbol{r}_i \in \mathbf{R}^3 \)、\( \boldsymbol{L} \in \mathbf{R}^3 \) 分别为机器人总质量、世界系下质心位置、重力加速度、接触点位置以及关于质心的角动量；\( ^ \) 为斜对称矩阵，\( \hat{\boldsymbol{a}}\boldsymbol{b}=\boldsymbol{a} \times \boldsymbol{b} \)。式 (9-34) 前三行为牛顿定律描述机器人的平动，后三行为欧拉方程描述机器人的转动。将前三行的结果代入到后三行可进一步简化，得

\( \begin{bmatrix} m(\ddot{\boldsymbol{p}}-\boldsymbol{g}) \\ m\hat{\dot{\boldsymbol{p}}}(\dot{\boldsymbol{p}}-\boldsymbol{g})+\dot{\boldsymbol{L}} \end{bmatrix} = \sum_{i=1}^{N_c} \begin{bmatrix} \mathbf{I}_{3 \times 3} \\ \hat{\boldsymbol{r}}_i \end{bmatrix} \boldsymbol{f}_i = \boldsymbol{Gf} \) (9-35)

如果将机器人质心位置变化量通过高阶多项式进行参数化，则

\( \boldsymbol{p}=\boldsymbol{p}_0+\boldsymbol{A}_p \boldsymbol{c} \quad \dot{\boldsymbol{p}}=\boldsymbol{A}_v \boldsymbol{c} \quad \ddot{\boldsymbol{p}}=\boldsymbol{A}_a \boldsymbol{c} \) (9-36)

式中，\( \boldsymbol{c} \) 为轨迹参数；\( \boldsymbol{A}_p \)、\( \boldsymbol{A}_v \)、\( \boldsymbol{A}_a \) 为只与采样时间相关的系数矩阵。将式 (9-36) 代入式 (9-35) 并忽略影响较小的二次项及转动可得

\( \begin{bmatrix} m(\ddot{\boldsymbol{p}}-\boldsymbol{g}) \\ m\hat{\dot{\boldsymbol{p}}}(\dot{\boldsymbol{p}}-\boldsymbol{g})+\dot{\boldsymbol{L}} \end{bmatrix} \approx \begin{bmatrix} m\boldsymbol{A}_a \\ m\hat{\dot{\boldsymbol{p}}}_0 \boldsymbol{A}_a + m\hat{\boldsymbol{g}} \boldsymbol{A}_p \end{bmatrix} \boldsymbol{c} + \begin{bmatrix} -m\boldsymbol{g} \\ m\hat{\dot{\boldsymbol{p}}}_0 \end{bmatrix} = \sum_{i=1}^{N_c} \begin{bmatrix} \mathbf{I}_{3 \times 3} \\ \hat{\boldsymbol{r}}_i \end{bmatrix} \boldsymbol{f}_i = \boldsymbol{Gf} \) (9-37)

式 (9-37) 的系数部分均可根据机器人状态及环境信息计算得到，从而用于构建二次规划问题。

考虑图 9-19 所示的场景，机器人存在多种路径及落脚点的选择通过该场景。假设 \( T \) 为规划轨迹的总时间，\( N_s \) 为采样点数，\( \Delta t = \frac{T}{N_s} \) 为采样时间间隔，\( N_i \) 为腿 \( i \) 的候选落脚点个数，\( \boldsymbol{r}_{i,j} \) 为腿 \( i \) 候选落脚点 \( j \) 的位置，\( \boldsymbol{n}_{i,j} \) 为腿 \( i \) 候选落脚点 \( j \) 的单位外法向，\( b_{i,j,k} \) 为腿 \( i \) 候选落脚点 \( j \) 在 \( k \) 采样时刻是否踩中，\( \boldsymbol{f}_{i,j,k} \) 为腿 \( i \) 候选落脚点 \( j \) 在 \( k \) 采样时刻地面作用力，\( c_{i,k} \) 为腿 \( i \) 已知的接触时序（接触为 1，摆动为 0）。其中，\( 1 \leqslant j \leqslant 2 \)，\( 0 \leqslant j \leqslant N_i \)，\( 0 \leqslant k \leqslant N_s \)。因此对于采样点 \( k \)，腿 \( i \) 的位置为

\( \boldsymbol{r}_{i,k} = \sum_{j=1}^{N_i} b_{i,j,k} \boldsymbol{r}_{i,j,k} \quad \sum_{j=1}^{N_i} b_{i,j,k} = c_{i,k}, \quad c_{i,k} \in \{0,1\} \) (9-38)

若处于接触状态，则 \( \boldsymbol{r}_{i,k} \) 等于选中候选落脚点的位置，否则为 0。同时，\( c_{i,k} \in \{0,1\} \) 约束了同一时刻足端只能出现在一个位置。为避免打滑，腿 \( i \) 候选落脚点 \( j \) 在 \( k \) 采样时刻地面作用力需要满足接触面摩擦锥的约束，即

\( \boldsymbol{D}(\boldsymbol{n}_{i,j}, \mu) \boldsymbol{f}_{i,j,k} \leqslant \boldsymbol{f}_{lim} \) (9-39)

式中，\( \boldsymbol{D}(\boldsymbol{n}_{i,j}, \mu) \) 为根据接触点法向及地面摩擦系数计算得到的摩擦力约束矩阵。
