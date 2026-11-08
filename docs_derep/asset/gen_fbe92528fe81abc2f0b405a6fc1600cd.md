.alignSelf(ItemAlign.Center);

      Button('随机颜色')

        .onClick(() => this.changeBackgroundColor())

        .fontSize(20)

        .backgroundColor(Color.Gray)

        .fontColor(Color.White)

        .width('60%')

        .alignSelf(ItemAlign.Center);

    }

    .padding(20)

    .width('100%')

    .height('100%')

    .backgroundColor(this.backgroundColor1)

    .alignItems(HorizontalAlign.Center);

  }

  private async changeBackgroundColor() {

    // const randomColor = this.generateLightColor();

    const randomColor = this.generateStandardColor();

    this.backgroundColor1 = randomColor; // 更新页面背景颜色

    // 创建 HardwareService 实例，传入配置中的硬件控制 API 地址

    const hardwareService = new HardwareService(config.hardwareApiUrl);

    try {

      //调用硬件服务，将随机颜色发送给硬件设备

      const success = await hardwareService.controlHardware(randomColor);

      console.log(`Hardware control success: ${success}`);

    } catch (error) {

      console.error('Error while sending random color to hardware:', error);

    }

  }

  private generateLightColor(): string {

    const r = Math.floor(Math.random() * 128 + 128);

    const g = Math.floor(Math.random() * 128 + 128);

    const b = Math.floor(Math.random() * 128 + 128);

    return `#${r.toString(16).padStart(2, '0')}${g.toString(16).padStart(2, '0')}${b.toString(16).padStart(2, '0')}`;

  }

  /**

   * 该函数用于随机生成标准的七种颜色中的一种颜色的十六进制编码

   * @returns {string} 随机选中的标准颜色的十六进制编码字符串

   */

  private generateStandardColor(): string {

    // 定义一个数组，存储标准的七种颜色的十六进制编码

    // 红色，象征热情、活力、爱情和危险

    // 橙色，结合了红色的热情和黄色的活力，给人温暖、欢快的感觉

    // 黄色，亮度高，代表智慧、快乐、希望和活力

    // 绿色，与大自然相关，代表生命、健康、和平与成长

    // 蓝色，给人冷静、专业、信任和深邃的感觉

    // 靛色，深沉神秘，介于蓝色和紫色之间，具有精神性和内省性

    // 紫色，富有贵族气息和浪漫情怀，与皇室、神秘和创造力相关

    const colors = ['#FF0000', '#FFA500', '#FFFF00', '#008000', '#0000FF', '#4B0082', '#EE82EE'];

    // 生成一个 0 到 colors 数组长度（不包含数组长度）之间的随机整数索引

    const index = Math.floor(Math.random() * colors.length);

    // 根据生成的随机索引，从 colors 数组中取出对应的颜色十六进制编码并返回

    return colors[index];

  }

}

Index.ets 维持原样不作改动。升级后的版本在模拟器中启动，点击“随机颜色”按钮会先借助 generateLightColor 产出新色值，随后刷新页面背景，并调动 HardwareService 把该色值推送给硬件侧，从而做到 App 与设备颜色的即时一致，如图5-18 所示。

若只用 generateLightColor 生成的浅色系，在 LED 上容易看不清，建议切换到 generateStandardColor 来做效果验证。

[PRE0]

图5-18

图5-19 展示的是采用随机标准七彩色进行硬件联动控制时的运行界面。

[PRE1]

图5-19

### 5.4 硬件状态联动与ArkTS事件驱动
本部分围绕如何借助数据库同步来管理硬件状态展开，并说明 ArkTS 如何自动感知硬件变化并转化为应用事件，实现软硬件的紧密配合。

#### 5.4.1 硬件状态采集与数据库同步方案
本小节说明通过树莓派实时抓取硬件状态诸如传感器数据，并借助 Python 脚本与 PHP 脚本把数据写入数据库的完整路径。整体流程涵盖在 phpMyAdmin 中建库建表、编写采集脚本、提供写入与查询的 PHP 接口，最终形成一套可为智能家居提供实时数据的管理体系。部署时请注意：

1. SSH 登录默认用户 “pi” 需按实际环境修改；
2. 文中出现的 “192.168.1.13” 仅作示例，上线时请换成真实服务器地址；
3. 数据库账号密码如 $db_username='root'; 和 $db_password='yourpassword'; 请依据实际配置调整。

##### 1.创建数据库和表
下面说明在 phpMyAdmin 中新建用于存放硬件状态的库与表的做法。

1. 用浏览器打开 “http://192.168.1.13/phpmyadmin/” 并使用管理员账号登录，实际部署请替换为真实 IP。
2. 进入主界面后点顶部【数据库】，在创建数据库一栏输入 smart_home，点击创建，效果见图5-20。

[PRE2]

图5-20

进入 smart_home 库后，打开左侧管理页，切换到【SQL】标签，执行如下语句新建表 smart，字段包含 id、temp、info 与 datetime，用来记录各硬件状态，随后点击执行，如图5-21。

[PRE3]

[PRE4]

图5-21

##### 2.在树莓派上创建并编辑smart_home.py文件
下面介绍如何通过 SSH 连接树莓派并创建用于采集数据并上报的 Python 脚本。

（1）在终端执行连接命令，注意把 “pi” 和 “192.168.1.13” 替换为实际用户名与设备 IP：

[PRE5]

（2）新建并打开文件 smart_home.py：

[PRE6]

(3) 填入如下 Python 代码。脚本可实时读取 GPIO 与 I2C 传感器数据，并在状态发生变化时通过 HTTP 请求把数据推送给服务器端的 PHP 接口：

[PRE7]

保存后退出编辑器。

##### 3.编写PHP脚本实现数据写入与查询
为完成数据与数据库的同步，需准备两个 PHP 脚本，一个负责写入 sql.php，一个负责查询 smart_home.php。

- 新建并编辑 sql.php：

[PRE8]

把以下 PHP 代码写入文件：

[PRE9]

- 新建并编辑 smart_home.php：

[PRE10]

写入以下 PHP 代码。该脚本连接数据库、查询硬件状态并以 JSON 格式返回结果：

[PRE11]

##### 4. 运行Python脚本同步硬件状态数据
(1) 在树莓派终端运行启动命令：

[PRE12]

(2) 观察终端日志，确认数据已通过 HTTP 请求成功送达 PHP 接口并入库。
(3) 在浏览器中核验数据：可通过 phpMyAdmin 查看，地址格式为 http://192.168.1.13/phpmyadmin，或直接访问 http://192.168.1.13/smart_home.php 获取 JSON。

图5-22 与图5-23 展示了数据库中硬件状态同步的效果截图。

[PRE13]

[PRE14]

图5-23

完成上述步骤后，即可搭建起一套完整的硬件状态管理与数据库同步体系：

- 树莓派采集的传感器数据可经 Python 脚本实时上传至服务器；
- PHP 脚本负责数据的写入与查询；
- 系统可通过 HTTP 接口实现数据的实时监控。