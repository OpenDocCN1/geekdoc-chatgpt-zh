
型为网格模型，并通过 filename 属性指定加载网络模型文件的路径。

<material> 用于描述 pelvis 连杆的材质属性。<color> 子元素用于指定材质的颜色，通过 rgba 属性的前三位定义 RGB 值，最后一位定义透明度。

- 3) <collision> 段程序说明：

<origin xyz="0 0 0" rpy="0 0 0"/> 和上面同理，表示 pelvis 连杆的碰撞模型相对于 pelvis 连杆坐标系的位姿。

<geometry> 用于定义 pelvis 连杆的碰撞模型。<mesh> 子元素用于指定碰撞模型的类型为网格模型，并通过 filename 属性指定加载网络模型文件的路径。为了简化碰撞检测计算，也可定义一些简单的几何体作为碰撞模型，如示例中 left_hip_pitch_link 连杆所示。

- 4) <joint> 段程序说明：

<joint name="left_ankle_pitch_joint" type="revolute"> 使用 <joint> 元素定义了连接两者的关节。

<origin xyz="0 0.06445 -0.1027" rpy="0 -0.34907 0"/> 表示 left_hip_pitch_link 连杆坐标系相对于 pelvis 连杆坐标系的位姿。

<parent link="pelvis"/> 定义 pelvis 连杆为父连杆。

<child link="left_hip_pitch_link"/> 定义 left_hip_pitch_link 连杆为子连杆。

<axis xyz="0 1 0"/> 表示绕 left_hip_pitch_link 连杆坐标系的 y 轴旋转。

<limit lower="-2.35" upper="3.05" effort="88" velocity="32"/> 表示关节旋转角度范围为 -2.35 ~ 3.05rad，关节力矩限制为 88N • m，关节最大旋转速度为 32rad/s。

```
\(\boldsymbol{C}_I = \begin{bmatrix} I_{xx} & -I_{xy} & -I_{xz} \\ -I_{xy} & I_{yy} & -I_{yz} \\ -I_{xz} & -I_{yz} & I_{zz} \end{bmatrix}\)
```

\(\boldsymbol{C}_I\) 为定义于坐标系 \(\{C\}\) 的惯性张量矩阵，矩阵中各元素为

```
\(\begin{aligned} I_{xx} &= \iiint_V (y^2+z^2)\rho \mathrm{d}v \\ I_{yy} &= \iiint_V (x^2+z^2)\rho \mathrm{d}v \\ I_{zz} &= \iiint_V (x^2+y^2)\rho \mathrm{d}v \\ I_{xy} &= \iiint_V (xy)\rho \mathrm{d}v \\ I_{xz} &= \iiint_V (xz)\rho \mathrm{d}v \\ I_{yz} &= \iiint_V (yz)\rho \mathrm{d}v \end{aligned}\)
```

在 URDF 文件中，还可以使用多个 <collision> 元素同时定义一个部件的碰撞模型。这一特性允许将复杂的碰撞模型分解为多个简单的几何形状，从而简化碰撞检测的计算，同时不降低仿真精确程度。宇树 G1 人形机器人足端及其简化碰撞模型，如图 9-8 所示。

![](img/47a2b26fde9e2a8fab8f8289e100ee37_246_0.png)

图 9-8 宇树 G1 人形机器人足端及其简化碰撞模型

关于 URDF 的更多细节，可查阅与 ROS 相关的书籍。

### 2. Mujoco 仿真器机器人描述文件 MJCF

MJCF 是 Mujoco 仿真器提供的一种机器人描述文件格式。MJCF 以 XML 作为文件载体，能描述复杂的动力学系统。MJCF 的模块化设计，使用户能够从简单的小规模模型开始，随后逐步生成更加精细、复杂的模型。MJCF 与 URDF 的功能类似，但形式上更加简洁，且具有更丰富的表示能力。以下是以一个使用 MJCF 描述的空间二连杆机构：

```
<mujoco model="2R-link example">
<worldbody >
<body pos="0 0 1">
<joint axis="1 0 0" name="joint0" />
<geom type="capsule" size="0.06" fromto="0 0 0 0 0 -.4" />
<body pos="0 0 -0.4">
<joint axis="0 1 0" name="joint1" />
<geom type="capsule" size="0.04" fromto="0 0 0 .3 0 0" />
</body>
</body>
</worldbody >
<actuator >
<general name="joint0_ctrl" joint="joint0" />
<general name="joint1_ctrl" joint="joint1" />
</actuator >
<sensor >
<jointpos name="joint0_pos" joint="joint0" />
<jointpos name="jointq_pos" joint="joint1" />
</sensor >
</mujoco >
```

其中，第一个关节 joint0 和第二个关节 joint1 的旋转轴分别与世界坐标系的 x 轴和 y 轴对齐。<actuator> 标签下定义了 joint0 和 joint1 的电动机执行器。<sensor> 标签下定义了 joint0 和 joint1 的关节角度传感器。

尽管 MJCF 具有比 URDF 更丰富的特性，但 URDF 的使用范围更广，如一些开源动力学库 RBDL、Pinocchio 等支持导入 URDF 并计算动力学信息，现有的机器人描述格式仍然以 URDF 为主。为此，Mujoco 提供了一个将 URDF 转化为 MJCF 的工具。在编译安装完 Mujoco 可以使用 Mujoco 的 compile 程序将机器人 URDF 转为 MJCF。

```
compile input.urdf output.xml
```

其中，input.urdf 是待转化的 URDF 文件，output.xml 是输出的 MJCF 文件。

## 9.2.3 机器人系统集成

在搭建好机器人仿真模型后，还需根据需求创建机器人场景，为机器人添加执行器和传感器，使仿真集成为一个相对完整的系统。本小节将详细介绍如何在生成的人形机器人模型的基础上，创建机器人的运动场景，为机器人添加执行器和传感器，并设计程序获取机器人的运动信息和控制机器人的关节运动。

### 1. 创建机器人场景

9.2.2 节创建并生成了 Mujoco 仿真器使用的机器人描述文件，命名为 Unitree_G1.xml。在此基础上，还需要为机器人创建所处的场景，以便模拟机器人的各种运动。机器人的场景文件也使用 XML 文件进行描述。下面是一个最简单的场景文件实例：

```
<mujoco model="Unitree G1 scene">
<include file="Unitree_G1.xml"/>
<worldbody >
<light pos="1 0 3.5" dir="0 0 -1" directional="true"/>
<geom name="floor" size="0 0 0.05" type="plane"/>
</worldbody >
</mujoco >
```

其中，<include> 标签在场景中包含了之前生成好的机器人描述文件 Unitree_G1.xml。在 <worldbody> <light> 标签中，定义了整个场景的光照参数。在 <worldbody> <geom> 标签中，则创建了一个地平面。

使用 Mujoco 仿真器加载上面创建好的场景，如图 9-9 所示。

![](img/47a2b26fde9e2a8fab8f8289e100ee37_248_0.png)

图 9-9 人形机器人系统 Mujoco 仿真场景

在此基础上，还可以根据需要为机器人添加一些障碍物等，如在场景中添加一个方形障碍物等：

```
<worldbody >
<geom pos="1.2 0 0.04" type="box" size="0.1 2 0.04" quat="1.0 0 0 0"/>
</worldbody >
```

其中，pos="1.2 0 0.04" 为障碍物的位置；type="box" 表示障碍物的类型，这里为方形；size="0.1 2 0.04" 为障碍物的长宽高尺寸；quat="1.0 0 0 0" 为障碍物的姿态四元数。

### 2. 添加执行器

为了使用 Mujoco 提供的 API 实现对电动机执行器的精确控制，需要在模型中定义被控制的关节电动机。方法是在机器人的场景文件中添加 `<actuator>` 标签，并在标签内使用 `<motor>` 标签来定义电动机参数。其示例程序如下：

```
1 <mujoco model="Unitree_G1">
2 <actuator >
3 <motor name="left_hip_pitch_joint" joint="left_hip_pitch_joint" ctrllimited="true" ctrlrange="-88 88"/>
4 ......
5 <motor name="right_six_joint" joint="right_six_joint" ctrllimited="true" ctrlrange="-0.7 0.7"/>
6 </actuator >
7 </mujoco >
```

其中，name="left_hip_pitch_joint" 为电动机的名称；joint="left_hip_pitch_joint" 为电动机所绑定的关节；ctrllimited="true" 为启动电动机的限制约束；ctrlrange="-88 88" 为电动机在正负方向上能够输出的最大关节转矩。

### 3. 添加传感器

在 MJCF 中可以使用 `<sensor>` 标签来添加传感器，如关节角度、速度、IMU、机体位置、机体速度等。其示例程序如下：

```
1 <mujoco model="Unitree_G1">
2 <sensor >
3 <jointpos name="left_hip_pitch_pos" joint="left_hip_pitch_joint" />
4 <jointvel name="left_hip_pitch_vel" joint="left_hip_pitch_joint" />
5 <jointactuatorfrc name="left_hip_pitch_torque" joint="left_hip_pitch_joint" noise="0.01" />
6 <framequat name="imu_quat" objtype="site" objname="imu" />
7 <gyro name="imu_gyro" site="imu" />
8 <accelerometer name="imu_acc" site="imu" />
9 <framepos name="frame_pos" objtype="site" objname="imu"/>
10 <framelinvel name="frame_vel" objtype="site" objname="imu" />
11 </sensor >
12 </mujoco >
```

其中，`<jointpos>`、`<jointvel>` 和 `<jointactuatorfrc>` 分别为关节的位置、速度和转矩传感器，name 属性定义了传感器的名称，joint 属性为关节传感器所绑定的关节名称。`<framequat>`、`<gyro>` 和 `<accelerometer>` 分别为 IMU 的姿态四元数、角速度和加速度传感器。`<framepos>` 和 `<framelinvel>` 为机体位置和速度传感器。

### 4. 使用 Unitree SDK2 连接控制程序和仿真器

在创建好机器人系统场景后，就可以调用 Mujoco 仿真器的 API 来实现仿真和控制程序。在 Mujoco 仿真器的 API 中，机器人的模型参数被存储在 mj_model 数据结构中，仿真过程中计算的参数被储存在 mj_data 数据结构中。可以通过操作这两个数据结构来获取所需的仿真数据和控制仿真中的机器人。下面是实现机器人系统集成仿真的简要步骤。（1）仿真器端 对于仿真器端，主要包含两个步骤。一个是从仿真场景中获取传感器信息并发布给控制器，另一个则是接收控制器发来的指令并执行。首先，确定好控制程序框架：

```
1 #include "mujoco.h"
2 #include "stdio.h"
3 #include <unitree/robot/channel/channel_subscriber.hpp>
4 #include <unitree/robot/channel/channel_publisher.hpp>
5 #include <unitree/common/time/time_tool.hpp>
6 #include <unitree/idl/go2/LowState_.hpp>
7 #include <unitree/idl/go2/SportModeState_.hpp>
8 #include <unitree/idl/go2/LowCmd_.hpp>
9 #define TOPIC_LOWSTATE "rt/lowstate"
10 #define TOPIC_HIGHSTATE "rt/sportmodestate"
11 #define TOPIC_LOWCMD "rt/lowcmd"
12 char error[1000];
13 mjModel *mj_model;
14 mjData *mj_data;
15 unitree_go::msg::dds_::LowCmd_ low_cmd;
16 unitree_go::msg::dds_::LowState_ low_state;
17 unitree_go::msg::dds_::SportModeState_ high_state;
18 const int num_motor = 23; // 电动机数量
19 const int dim_motor_sensor = 3; // 每个电动机的传感器数量，位置、速度、转矩
20 void GetLowState (){
21 // 获取底层电动机状态
22 }
23 void GetHighState (){
24 // 获取高层位置和速度状态
25 }
26 void LowCmdHandler ( const void *msg ) {
27 // 接收电动机指令的回调函数
28 }
29
30 int main ( void ) {
31 // 加载模型
32 mj_model = mj_loadXML ( "scene.xml", NULL, error, 1000);
33 // 如果模型为空，退出程序
34 if ( ! mj_model ) {
35 printf ( "%s\n", error);
36 return 1;
37 }
38 // 创建一个 mjdata 数据结构类型用于存储仿真过程中的数据
39 mj_data = mj_makeData(m);
40 ChannelFactory::Instance()->Init(1, "lo");
41 // 发布底层电动机信息
42 ChannelPublisher<unitree_go::msg::dds_::LowState_> low_state_puber(TOPIC_LOWSTATE);
43 low_state_puber.InitChannel();
44
45 // 发布高层位置速度信息
46 ChannelPublisher<unitree_go::msg::dds_::SportModeState_> high_state_puber(TOPIC_HIGHSTATE);
47 high_state_puber.InitChannel();
48 // 接收控制指令
49 ChannelSubscriber<unitree_go::msg::dds_::LowCmd_> low_cmd_suber(TOPIC_LOWCMD);
50 // 初始化完成后，如果接收到 LowCmd_ 指令就会进入 HighStateHander 回调函数
51 low_cmd_suber.InitChannel(HighStateHander);
52 while ( 1 ) {
53 // 进行一步仿真计算，计算结果会存入 mj_data
54 mj_step(mj_model, mj_data);
55 // 获取仿真数据
56 GetLowState();
57 GetHighState();
58 // 发布仿真数据
59 low_state_puber->Write(low_state);
60 high_state_puber->Write(high_state);
61 }
62 // 释放内存
63 mj_deleteData(mj_data);
64 mj_deleteModel(mj_model);
65 return 0;
66 }
```

对于仿真数据的获取，仿真传感器的数据都储存于 mj_data->sensordata 中，可以按照传感器在机器人的 MJCF 描述文件中的顺序，从中依次取出对应的传感器数据，然后将其存入 Unitree SDK2 定义好的数据结构中，再使用 Channel Publisher 来发布数据。实现方法如下：

```
void GetLowState () {
if ( mj_data ) {
    for ( int i = 0; i < num_motor; i++ ) {
        low_state.motor_state()[i].q() = mj_data->sensordata[i];
        low_state.motor_state()[i].dq() = mj_data->sensordata[i + num_motor];
        low_state.motor_state()[i].tau_est() = mj_data->sensordata[i + 2 * num_motor];
    }
}
low_state.imu_state().quaternion()[0] = mj_data->sensordata[dim_motor_sensor + 0];
low_state.imu_state().quaternion()[1] = mj_data->sensordata[dim_motor_sensor + 1];
low_state.imu_state().quaternion()[2] = mj_data->sensordata[dim_motor_sensor + 2];
low_state.imu_state().quaternion()[3] = mj_data->sensordata[dim_motor_sensor + 3];
low_state.imu_state().gyroscope()[0] = mj_data->sensordata[dim_motor_sensor + 4];
low_state.imu_state().gyroscope()[1] = mj_data->sensordata[dim_motor_sensor + 5];
low_state.imu_state().gyroscope()[2] = mj_data->sensordata[dim_motor_sensor + 6];
low_state.imu_state().accelerometer()[0] = mj_data->sensordata[dim_motor_sensor + 7];
low_state.imu_state().accelerometer()[1] = mj_data->sensordata[dim_motor_sensor + 8];
}
```
