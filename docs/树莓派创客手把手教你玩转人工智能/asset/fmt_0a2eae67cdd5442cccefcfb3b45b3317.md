
### 4.3.4 运行内置对话demo

**步骤01** 运行个人助手demo版。

在终端输入如下命令并按`Enter`键：

```
src/examples/voice/assistant_grpc_demo.py
```

![](EPUB/images/image_184.jpg)

**图4-86**

**步骤02** 给予许可。

你会看到一条信息，提示去一个URL认证你的应用程序。这是因为这个程序需要用到Google Assistant。为了保证谷歌账号的信息安全，需要先对屏幕上显示的这个URL给予许可，如图4-87所示。

![](EPUB/images/image_185.jpg)

**图4-87**

一般情况下，浏览器会自动打开这个网页。如果没有，可以把链接直接复制粘贴到你的浏览器上。

**步骤03** 确认账号。

在浏览器中登录你的账号，然后单击`ALLOW`按钮，让你的Kit连接到谷歌账号上，如图4-88所示。

![](EPUB/images/image_186.jpg)

**图4-88**

**步骤04** 运行demo。

一旦连接好了，就会在树莓派终端陆续显示信息，然后会显示`Press button to start conversation...`，如图4-89所示。

![](EPUB/images/image_187.jpg)

**图4-89**

这时按下按钮并开始说话，说话时长不要超过一分钟。

**步骤05** 机器学习。

按一下按钮，然后问一个问题，或者给Google Assistant一个命令。比如你可以问`what’s the weather today？`或者说，`sing me a song！`当你说的时候，终端上会显示所进行的操作，如图4-90所示。

![](EPUB/images/image_188.jpg)

**图4-90**

Google Assistant可能会花好几秒钟来做出反应。如果Voice Kit不能听清你所说的话，那么有可能是你靠得太近了，可以稍微离远一点说。

**步骤06** 测试Google Assistant（见图4-91）。

下面是我们最喜欢跟Google Assistant说的话：

```
"Beam me up Scotty!"
"How much wood could a woodchuck chuck..."
"I'm one of your engineers."
"How do you say 'this tastes delicious' in Korean?"
"How far away is the moon?"
"Beatbox!"
"Remind me to call my friend tomorrow morning."
```

![](EPUB/images/image_189.jpg)

**图4-91**

**步骤07** 停止demo测试。

当你测试结束后，再按一下按钮并说`Goodbye`，或者直接按`Ctrl+C`组合键来终止程序。

当你按了`Ctrl+C`组合键时，无论是在终端还是在Secure Shell Extension上，它都会终止你现在运行的命令。如果你看不到提示，就再按几次`Ctrl+C`组合键。

如果死机了，就按`Ctrl+C`组合键，然后按上箭头键选择`src`命令来重启。

**步骤08** 关闭demo。

不用Voice Kit时，记得把它关机，并注意不要损坏SD卡。

输入以下命令，然后按`Enter`键：

```
sudo poweroff
```

你就会看到屏幕上弹出的信息有R、C和E选项（见图4-92），同时树莓派上的绿色LED灯熄灭，这时你就可以拔掉Kit上的电源了。当你重新接上电源时，记得要等到LED灯不再闪动时才可以通过SSH重新连接到Kit。

![](EPUB/images/image_190.jpg)

**图4-92**

> **注意**
>
> 如果你关掉了Secure Shell Extension或者是拔掉了电源，demo就会停止工作。
>
> 要重新连接到你的Kit，只需要重新接回电源，然后等待4分钟让它启动，就可以通过SSH进行重连了。

> **提示**
>
> 如果IP地址变更了，就可以先使用App来确定新的IP地址。

### 4.3.5 将demo设置成开机启动

默认情况下，你的Voice Kit在启动前不会做任何事情，必须手动执行一个像Assistant demo一样的程序。创建一个systemd服务，就可以让这个项目或者别的项目在启动时自动运行。

你只需要一个`.service`配置文件就可以了。这个文件可以接受一长串的配置选项，所以我们提供了一个参考。你可以在`~/AIY-projects-python/src/examples/voice/assistant_grpc_demo.service`下找到这个文件，内容如下：

![](EPUB/images/image_191.jpg)

把这个文件放到`/lib/systemd/system/`目录下，就可以正常工作了。你不用真的把它拖到上述目录下，可以创建一个超链接来链接到这个文件，具体操作如下：

![](EPUB/images/image_192.jpg)

现在让系统在启动时运行这个服务：

```
sudo systemctl enable assistant_grpc_demo.service
```

这样就设置完成了！你可以重启一下，看看是否正常工作。

或者输入下列命令手动启动它：

```
sudo service assistant_grpc_demo start
```

> **注意**
>
> 即使出现了提示对话框，也需要等一会程序才能开始运行。当Assistant Demo准备好的时候，按钮会亮起。

如果你想要停止在启动时运行该服务，可以使用下列命令：

```
sudo systemctl disable assistant_grpc_demo.service
```

如果它正在运行中，你可以用下列命令来停止：

```
sudo service assistant_grpc_demo stop
```

你可以用下面的命令来检查这个服务的状态：

```
sudo service assistant_grpc_demo status
```

你可以把上面的配置复制给另一个新文件，比如`my_program.service`，文件名必须以`.service`结尾。对于大多数程序而言，你只需要改变`ExecStart`，让它指向程序所在的Python文件即可。然后把`Description`改成对程序的描述。接下来只需要重复上述命令，把文件名换成你的文件名就可以啦。

如果你希望了解更多的服务配置文件，可以单击`.service config manual`。

### 4.3.6 查看日志

如果想查看更多的日志来帮助你排错或者纯粹出于好奇想看看日志，你可以用`journalctl`工具来查看系统日志和具体的项目日志。

默认条件下，这个工具会输出许多对你无用的系统信息，所以先把你的程序设置为一项服务，然后告诉`journalctl`只打印这个服务下的日志，这样会好很多。

比如，把Assistant Demo当成一个服务来启动，你可以输入下列命令来输入该服务的所有日志：

```
sudo journalctl -u assistant_grpc_demo -f
```

`-f`选项会不断地输入新产生的日志信息。可以按`Ctrl+C`组合键来停止输出。

### 4.3.7 更新和支持信息

**步骤01** 从GitHub上的发布页下载最新的`.img.xz`文件。要了解更多发布细节，可阅读更新日志。

**步骤02** 用转换头把MicroSD卡连到电脑上。

**步骤03** 用Etcher这样的程序把`.img.xz`闪存到你的MicroSD卡上。（Etcher是一个免费软件，并且可以在Windows、Mac OS和Linux系统下运行。）

闪存好之后，你就可以把MicroSD卡插回Kit上，这样项目就完成啦！

无论你是第一次搭建项目，还是经验非常丰富，都希望这个项目能对你有所启发。

## 4.4 更多玩法

现在，你已经搭建起自己的智能扬声器了。

从上面的测试中，你差不多就能够了解Voice Kit可以做什么了，现在开始自由探索它的功能。在下面的内容中，你可以找到关于Python APIs、硬件和其他程序搭建工具的说明。

你可以用`#aiyprojects`把自己的新创作分享给制作者社区。

> **注意**
>
> 下一部分的技术难度会比之前高得多，如果你是一个编程新手，可能需要加把劲了。

### 4.4.1 Python api library

为了更好地实现Voice Kit的各种功能，谷歌发布了一个Python库。你可以直接用库里的功能，不需要自己进行编程。其中包括了与Google Assistant和Cloud Speech-to-Text连接，以及用一些Kit的周边设备，比如按钮、LED灯和其他GPIO引脚等。

这些API被打包在一个叫`aiy`的程序包中，并提前安装在Kit的系统图片中。一定要确保你已经安装了最新的系统图片。

你可以阅读API参考来了解更多API相关的内容。下面这些API，你可能会在使用Voice Kit过程中用到：

*   `aiy.assistant`：用来简化与Google Assistant API的连接操作。
*   `aiy.cloudspeech`：用来简化与Google Cloud Speech-to-Text服务的连接。
*   `aiy.voice.audio`：用来记录和播放音频文件。
*   `aiy.voice.tts`：用来将文本转化成语音。
*   `aiy.board`：用来控制连接在Voice Bonnet上的按钮。
*   `aiy.leds`：用来控制按钮上的LED灯。
*   `aiy.pins`：用来定义在用`gpiozero`时可能会用到的bonnet上的额外的GPIO引脚。

#### 例子：录音机

如果你已经有一个demo作为模板，那么学起aiy Python API来可能会容易一点。

这些文件都预先存到了你Kit上的`~/AIY-projects-python/src/examples/`路径下。你也可以在GitHub上找到所有例子的源代码。

比如，你可以运行`voice_recorder.py`文件来进一步了解`aiy.voice.audio` API。

```
cd ~/AIY-projects-python/src/examples/voice
./voice_recorder.py
```

这时如果你按下按钮，设备就会开始记录下你所说的话。说完后，松开按钮，它就会播放刚刚的录音。

你可以在树莓派上打开这个文件夹，或者在这里看它的源代码，然后进行调试。

如果你对硬件比较感兴趣，比如按钮和伺服系统的使用方法，那么可以阅读下文中关于GPIO引脚扩展的内容，其中包含了相关的例子，以供大家参考。

### 4.4.2 使用Cloud Speech API

如果你想要尝试一下别的API，可以遵循下面的指南体验Google Cloud Speech-to-Text服务。这项功能可以把你的口头命令转换成文字，然后用这些文字来触发你的程序。这个API支持80种语言和长音频片段，还能够进行模糊识别。

更换API，首先需要用到`src/examples/voice/cloudspeech_demo.py`代码。

#### 1. 打开付费功能

Google Cloud Speech-to-Text服务是一个云平台服务，如果你一个月内的使用时间小于60分钟，那么这部分是免费的；如果超过了60分钟，那么需要按照每15秒0.006美元的价格来计费。在即将超过免费额度时，该平台会向你发送警告信息。

**步骤01** 在谷歌云控制台上打开导航菜单。

**步骤02** 单击“结算”。

**步骤03** 如果你没有结算账户，就单击“添加结算账号”，并按指示完成整个添加过程。

**步骤04** 回到结算主页，然后单击“我的项目”。

**步骤05** 找到你的新项目名，确保该项目已经与你的结算账号连接。

**步骤06** 你可以单击三点符号，然后单击“更换结算账号”来连接或者是更换项目的结算账号。

#### 2. 激活API

**步骤01** 在控制台上打开导航菜单，然后单击“API和服务”。

**步骤02** 单击“启动API和服务”。

**步骤03** 在搜索栏中输入“Cloud Speech API”，然后单击对应的卡片。

**步骤04** 单击“启用”来打开API。

#### 3. 创建服务账号和凭据

**步骤01** 打开左边的导航菜单，单击“API和服务”，然后单击“凭据”。

**步骤02** 单击“创建凭据”，然后单击下拉栏中的“服务账号密钥”。

**步骤03** 从“服务账号”下拉列表中选择新的服务账号。

**步骤04** 输入服务账号名称，比如“Voice凭据”（你的语音识别器对应的服务账号）。

**步骤05** 选择Project下的查看者角色。

**步骤06** 选择JSON密钥类型。

**步骤07** 单击“创建”。

**步骤08** 你的凭据就会自动下载（文件名由项目名以及一些额外的数字组成），将其重命名为`cloud_speech.json`。

**步骤09** 打开工作站的终端，然后输入下面的命令，把`credentials.json`文件移动到对应的文件夹中。

```
(using the local file system)
```
