[PRE0]

## Index.ets 页面交互设计

在 Index.ets 里新增了蓝灯、绿灯两个快捷按键。按下后会触发 fetchData，内部会先实例化 HardwareService，再通过 controlHardware 把对应的颜色指令发往服务端。界面走极简路线，操作直观，同时所有返回信息都会打印到控制台，调试时能第一时间看到硬件是否正常响应。

[PRE1]

##### 3. 效果演示与截图

打开应用后，首页就呈现蓝灯与绿灯两枚按钮。任选其一，点击即向硬件控制接口发起请求，依据服务器返回结果判断执行是否成功，硬件灯的颜色会即时跟进，如图5-16所示。

[PRE2]

图5-16

#### 5.3.3 ArkTS随机变色器与硬件联动实战

本部分以随机颜色变化器作为案例，演示怎样在轻量级应用里叠加硬件联动，让 App 背景随机的同时，物理设备灯色也同步变化。内容涵盖目录重构、基础变色实现、硬件能力的迭代升级，以及最终的运行演示。

##### 1. 目录与模块调整

项目保持原有的随机色生成与界面展示不变，额外接入硬件控制服务层。新增文件主要负责两件事：

- HardwareService.ets：把所有硬件交互逻辑收拢封装；
- HardwareTypes.ets：统一硬件请求与返回的数据结构。

为了提升视觉趣味，猫咪素材被单独存放：
[PRE3]

整体目录示例如下：
[PRE4]

##### 2. 基础版随机背景变色

先做一个只负责随机变色的基础页。RandomColorPage.ets 采用 Column 垂直排列，顶部显示色值，中间放猫咪图，底部放按钮。点下“随机颜色”后会调用 changeBackgroundColor，随机生成一种浅色系并刷新背景。

图片通过 $r('app.media.cat') 引入，提升观感。入口 Index.ets 负责加载该组件，完成最简交互。

原始代码示例：
[PRE5]

入口调用示例如下：
[PRE6]

在模拟器里运行，能看到当前色值、猫咪图和随机颜色按钮。按下按钮，背景即刻换成新的淡色，如图5-17所示。
[PRE7]

图5-17

##### 3. 进阶版接入硬件控制

升级后 RandomColorPage.ets 在变色基础上增加了硬件同步。changeBackgroundColor 现在会先调用 generateStandardColor，从七种标准色中挑选一个，保证 LED 显示足够分明。色值确定后背景立刻更新，同时 new HardwareService().controlHardware 把该色发送给设备，让 App 与灯的状态保持一致。

HardwareService.ets 充当通信桥梁，用 HTTP 请求把颜色数据推送到指定控制接口，驱动设备端实时刷新。同步成功后还会弹出轻提示，使用户能明显感受到变化。整体上既美化了视觉，也让交互从纯页面延伸到实体硬件，形成更深度的联动。

升级代码如下：

typescript

// src/main/ets/pages/RandomColorPage.ets

import { config } from '../configs/Config'; // 导入配置文件，包含硬件控制 API 地址

import { HardwareService } from '../services/HardwareService'; // 导入硬件控制服务

@Component

export struct RandomColorPage {

  @State backgroundColor1: string = '#FFFFFF'; // 默认背景颜色为白色

  build() {

    Column({ space: 20 }) {

      Text(`当前背景颜色代码: ${this.backgroundColor1}`)

        .fontSize(18)

        .fontWeight(FontWeight.Bold)

        .fontColor(Color.Black)

        .alignSelf(ItemAlign.Center);

      Image($r('app.media.cat'))

        .width(85)

        .height(100)

        .borderRadius(5)