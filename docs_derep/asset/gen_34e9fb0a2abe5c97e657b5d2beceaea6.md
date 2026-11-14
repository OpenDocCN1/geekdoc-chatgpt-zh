#### 步骤 01 安装依赖库

[PRE0]

#### 步骤 02 从百度云控制台获取密钥

访问地址：`https://login.bce.baidu.com/?account=&redirect=http%3A%2F%2Fconsole.bce.baidu.com%2Fai%2F%3Ffromai%3D1#/ai/speech/overview/index`

#### 步骤 03 下载源码 Smart_Fan.py，链接：`https://raw.githubusercontent.com/SeeedDocument/MIC_HATv1.0_for_raspberrypi/master/src/baidu_STT/Smart_fan.py`。

部分代码展示如下：

[PRE1][PRE2][PRE3]

命令行操作如下：

[PRE4]

**温馨提示**

运行 `Smart_Fan.py` 前，请在第 36、37、38 行填入百度密钥。你也可以通过运行 `synthesis_wav.py` 生成自定义语音。同样需要在第 6、7、8 行添加密钥，并修改字符串为想要合成的内容。

#### 步骤 04 对着设备说“打开灯”，LED 灯就会亮起，效果见图 3-46。

[PRE5]

**图 3-46**

### 3.4 常见问题排查

**Q1：严格按教程操作，驱动安装依然失败怎么办？**

**A1：** 若上述方法均无效，请访问固件下载链接：`https://v2.fangcloud.com/share/7395fd138a1cab496fd4792fe5?folder_id=188000207913`。操作步骤：首先刷入下载的固件；刷机后别忘了更换软件源（apt 源）；其次，使用交互功能前，在终端执行 `alexa-auth` 或 `dueros-auth` 申请授权。授权成功后，`/home/pi` 目录下会生成 `.avs.json` 文件，此时方可使用交互功能；最后，`/home/pi` 目录下会有 `respeaker` 示例文件夹，可根据麦克风型号选择对应示例。刷机后建议更新 `respeaker` 目录下的示例（在该目录执行 `git pull origin master`）。

**Q2：`aplay` 播放有声，但运行 alexa/dueros 却无声？**

**A2：** 可用的播放器有三种：`mpv`、`mpg123`、`gstreamer`。其中 `SpeechSynthesizer` 和 `Alerts` 更适配 `mpg123`；`AudioPlayer` 优先级为 `gstreamer` > `mpv` > `mpg123`。`Gstreamer` 支持的音频格式最多，在树莓派上运行也最稳定。也可通过环境变量 `PLAYER` 指定 `AudioPlayer` 使用的播放器。尝试以下命令启用语音：

[PRE6]

**Q3：运行 DOA，喊“snowboy”无反应？**

**A3：** 用 `audacity` 检查 4 个麦克风是否全有数据。只要有一个通道无数据，就会无响应。

**Q4：安装 snowboy 时提示平台不兼容？**

**A4：** 目前 `snowboy` 仅支持 `Python2`，创建虚拟环境时请务必指定 Python2。

**Q5：用 `sudo python file.py` 运行时出现依赖缺失？**

**A5：** 测试发现 `sudo` 默认调用系统环境，而教程安装的依赖位于 `~/env`。解决方法：改用 `sudo ~/env/bin/python file.py` 运行。

**Q6：运行 `kws_doa.py` 时喊“snowboy”没反应？**

**A6：** 用 `audacity` 确认 4 个声道均正常。只要有一个声道无数据，喊 `snowboy` 就不会有回应。

# 第 4 章 智能语音（二）Google AIY Voice Kit

## 4.1 简介与组装

### 4.1.1 Voice Kit 简介

Google 推出了 AIY Voice Kit。该套件可用于构建自然语言处理器。接入 Google Assistant 或在线语音转文字服务后，你可以用语音指令控制程序，或直接提问。所有组件都塞进一个小纸盒，由树莓派供电。

购买套装后无需额外采购，甚至树莓派主板都已包含在内。

### 4.1.2 动手前的准备

Google AIY Voice Kit 外观如图 4-1 所示。

[PRE7]

**图 4-1**

现在开始组装你的套件！

下方手册详细说明了组装方法、连接方式及 Google Assistant 演示的运行流程。完成演示后，你的套件能回答问题、执行语音指令。此外还可使用 Google Cloud Speech-to-Text 服务，将语音指令转为程序可理解的文本。

#### 核对套件版本

当前手册适用于 Voice Kit v2.0。产品包装盒左下角标有版本信息，见图 4-2。

[PRE8]

**图 4-2**

v2.0 版本可直接按手册操作。若包装上无版本号，请参考旧版手册组装。

除套件外，还需准备以下工具辅助组装：

*   2mm 一字螺丝刀。

*   Micro USB 电源。推荐：带 micro-USB B 接口、输出 2.1A 的 USB 电源。次选：手机充电器（常标榜快充），也能提供 2.1A。**切勿用电脑 USB 口给树莓派供电**，不仅电流不足，还可能烧毁 SD 卡，导致无法启动或报错。

*   Wi-Fi 连接。有两种方式让套件联网：

    [PRE9]

    使用 AIY Projects App。需一台 Android 手机，外加一台 Windows、Mac 或 Linux 电脑。

    [PRE10]

    使用显示器、鼠标、键盘。无 Android 手机时可选此法。需一台 Windows、Mac 或 Linux 电脑、一个鼠标、一个键盘、一台带 HDMI 输入的显示器或电视、一根标准 HDMI 线和一个 mini HDMI 转接头，以及一个集线器把鼠标键盘接到套件上。集线器可选 USB OTG 转接头，将树莓派的 Micro USB 口转为标准 USB 口，再接入[无线键鼠套装](https://www.adafruit.com/product/1738)。也可用 Micro USB Hub 多口连接传统键鼠。

打开套件核对配件。如有缺失，请邮件联系 `support-aiyprojects@google.com`，我们会协助补发。

### 4.1.3 配件清单

AIY 配件示意图见图 4-3。各套件具体说明见表 4-1。

[PRE11]

**图 4-3**

**表 4-1 Google AIY Kit 配件说明**

[PRE12]

### 4.1.4 组装 Voice Kit

开始组装吧。

#### 步骤 01 确定朝向。

搭建内部框架，所有电子元件将置于其中。按图 4-4 所示方向把纸板平放桌面；左侧卡槽应更靠近纸板边缘。

[PRE13]

**图 4-4**

#### 步骤 02 折叠长翻盖。

将远离你的长翻盖向下折叠，如图 4-5 所示。

[PRE14]

**图 4-5**

#### 步骤 03 折叠左右翻盖。

将左右两侧翻盖向自己这边折叠，如图 4-6 所示。

[PRE15]

**图 4-6**

#### 步骤 04 折叠底部翻盖。

将底部翻盖向上朝自己折叠，如图 4-7 所示。

[PRE16]

**图 4-7**

#### 步骤 05 对齐底部。

将底边的卡槽与左右两侧的插片对齐，如图 4-8 所示。

[PRE17]

**图 4-8**

#### 步骤 06 插入插片。

将插片插入卡槽，如图 4-9 所示。

[PRE18]

**图 4-9**

#### 步骤 07 检查对齐情况。