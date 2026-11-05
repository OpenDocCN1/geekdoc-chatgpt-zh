
![](images/txt012_28.jpg "pagenumber_ebook=151,pagenumber_book=139")

而当`R1=Rf`时，

![](images/txt012_29.jpg "pagenumber_ebook=151,pagenumber_book=139")

# 第四篇　智能感知

## 第10章　Arduino简介

### 10.1　初识Arduino

Arduino是一款源自意大利的开放源代码硬件项目平台，该平台是一块USB接口Simple I/O接口板，并且使用类似Java、C语言的集成开发环境（IDE）。

Arduino的核心是基于AVR指令集的单片机，但它简化了单片机工作的流程，对AVR库进行了二次编译封装，将复杂的单片机底层代码封装成简单实用的函数，使用者无须关心单片机编程烦琐的细节，如寄存器、地址指针等，从而大大降低了单片机系统开发难度，特别适合老师、学生和业余爱好者使用。

Arduino系列控制器具有如下特色：

（1）具有开放源代码的电路图设计，程序开发接口免费下载，也可根据需求自行修改。

（2）可以采用USB接口供电，也可以外部供电。

（3）支持ISP在线烧写，可以将新的bootloader固件烧入Arduino的CPU芯片。有了bootloader之后，可以通过USB更新程序。

（4）可依据官方提供的PCB和SCH电路图（Eagle格式）简化Arduino模组，完成独立运作的微处理控制。可简单地与传感器、电子元件等连接，如红外线、光敏电阻、热敏电阻、超声波、舵机等。

（5）支持多种互动程序，如Flash、Max/Msp、C、Processing等。

（6）在应用方面，突破了以往只能使用鼠标、键盘、CCD等输入方式获取互动内容的方式，可以更简单地达成单人或多人游戏互动。

### 10.2　Arduino硬件

Arduino的出现大大降低了互动设计的门槛，越来越多没有学过电子知识的人，如艺术家、设计师等，开始使用Arduino制作各种充满创意的作品。为了满足不同应用领域的要求，Arduino设计了多款不同型号的开发板，如Arduino Duemilanove、Arduino Nano、Arduino mini、Arduino BT、Arduino Fio、Arduino Uno等。本书针对后面章节的需要，介绍Arduino Uno与Arduino Mega 2560两种开发板。

#### 10.2.1　Arduino Uno开发板

Arduino Uno具有14个数字I/O口、6个模拟I/O口、一个复位开关、一个ICSP下载口，支持USB接口。其中，可通过USB接口直接供电，也可以使用单独的7～12 V电源供电，具体的资源分配如图10 - 1所示。

Arduino Uno开发板的各引脚定义如下：

（1）数字引脚：0～13。

（2）串行通信：0作为RX，接收数据；1作为TX，发送数据。

（3）外部中断：2、3。

（4）PWM输出：～3、～5、～6、～9、～10、～11。

（5）SPI通信：10作为SS，11作为MOSI，12作为MISO，13作为SCK。

（6）板上LED：13。

（7）模拟引脚：A0～A5（在引脚号前加A，以区分数字引脚）。

（8）TWI通信：A4作为SDA，A5作为SCL。

![](images/txt014_1.jpg "pagenumber_ebook=156,pagenumber_book=144")

图10-1　Arduino Uno的资源分配

#### 10.2.2　Arduino Mega 2560开发板

与Arduino Uno相比，Arduino Mega 2560增加了更多资源，它具有54个数字I/O口（其中14个可提供PWM输出）、16个模拟I/O口、4对串行数据通信口（Universal Asynchronous Receiver/Transmitter，UART）、一个复位开关、一个ICSP下载口，支持USB接口和直流电源供电，具体的资源分配如图10 - 2所示。

Arduino Mega 2560开发板的各引脚定义如下：

（1）数字引脚：0～53。

（2）串行通信：提供4对串行数据通信口，0（RX）和1（TX）作为串口1，19（RX）和18（TX）作为串口2，17（RX）和16（TX）作为串口3，15（RX）和14（TX）作为串口4。

![](images/txt014_2.jpg "pagenumber_ebook=157,pagenumber_book=145")

图10-2　Arduino Mega 2560的资源分配

（3）外部中断：提供6个外部中断源，分别是2（外部中断0）、3（外部中断1）、21（外部中断2）、20（外部中断3）、19（外部中断4）、18（外部中断5）。

（4）PWM输出：0～13。

（5）SPI通信：53作为SS，51作为MOSI，50作为MISO，52作为SCK。

（6）板上LED：13。

（7）模拟引脚：A0～A15（在引脚号前加A，以区分数字引脚）。

（8）TWI通信：20作为SDA，21作为SCL。

### 10.3　安装ArduinoUno驱动程序

在应用Arduino开发环境进行程序设计前，首先要安装Arduino Uno开发板的驱动程序。本节介绍Arduino Uno驱动程序的常规安装方法。

从官方网站下载驱动程序安装文件，打开解压后的文件，打开“`drivers`”文件夹，如果计算机是32位系统，运行“`dpinst - x86.exe`”文件，如果计算机是64位系统，运行“`dpinst - amd64.exe`”文件，如图10 - 3所示。在弹出的对话框中单击“下一步”按钮，如图10 - 4所示，即可完成安装，如图10 - 5所示。

![](images/txt014_3.jpg "pagenumber_ebook=157,pagenumber_book=145")

图10-3　运行相应“.exe”文件

![](images/txt014_4.jpg "pagenumber_ebook=158,pagenumber_book=146")

图10-4　单击“下一步”按钮

![](images/txt014_5.jpg "pagenumber_ebook=158,pagenumber_book=146")

图10-5　完成驱动程序的安装

### 10.4　Arduino开发环境

Arduino开发环境的主界面如图10 - 6所示，除了包含“文件”“编辑”“项目”“工具”“帮助”这5类菜单外，在菜单栏下方还提供了5个常用的快捷菜单按钮，它们依次为校验（`Verify`）、上传（`Upload`）、新建（`New`）、打开（`Open`）、保存（`Save`）。

![](images/txt014_6.jpg "pagenumber_ebook=159,pagenumber_book=147")

图10-6　Arduino开发环境的主界面

这5个快捷菜单按钮的具体功能见表10 - 1。

表10-1　Arduino开发环境的5个常用的快捷菜单按钮

![](images/txt014_7.jpg "pagenumber_ebook=159,pagenumber_book=147")

下面以Arduino Uno开发板自带的LED灯闪烁的例子，来介绍利用Arduino Uno进行单片机程序开发的具体流程。

1. 新建文件

单击“新建”按钮后，新建一个空白的Arduino程序文件。

2. 串口设置和Arduino Uno开发板型号选择

Arduino Uno开发板驱动程序安装完成后，在“我的电脑”→“设备管理器”中查看连接到电脑的Arduino Uno开发板所对应的串口号，如图10 - 7所示，当前Arduino Uno开发板对应的串口是`COM3`。

![](images/txt014_8.jpg "pagenumber_ebook=159,pagenumber_book=147")

图10-7　在“设备管理器”中查看Arduino Uno开发板对应的串口号

在“工具”下拉菜单中选择“端口："`COM3`"”选项，如图10 - 8所示，即完成当前设备的串口配置。

![](images/txt014_9.jpg "pagenumber_ebook=160,pagenumber_book=148")

图10-8　在Arduino开发环境选择Arduino Uno开发板对应的串口号

单击“工具”→“开发板："`Arduino Uno`"”选项，在其下拉菜单中选择“Arduino Uno”选项，如图10 - 9所示，即完成当前开发板的型号选择。

![](images/txt014_10.jpg "pagenumber_ebook=160,pagenumber_book=148")

图10-9　在Arduino开发环境选中Arduino Uno开发板的型号

3. 程序设计

在Arduino开发环境的程序编辑区中输入程序代码，如图10 - 10所示。

![](images/txt014_11.jpg "pagenumber_ebook=161,pagenumber_book=149")

图10-10　在Arduino开发环境的程序编辑区中输入程序代码

从图10 - 10可知，Arduino Uno开发板上LED的引脚是13，则编写使该LED循环点亮 - 熄灭1 s的程序如下：

![](images/txt014_12.jpg "pagenumber_ebook=161,pagenumber_book=149")

4. 程序保存

程序输入完成后，在Arduino开发环境中选择“文件”→“保存”命令，在当前路径保存文件，或选择“文件”→“另存为”命令将该文件另存在其他路径。

5. 程序编译

单击“校验”按钮实现当前程序的编译，完成编译工作后，Arduino开发环境的状态栏会提示“编译完成”，同时信息提示栏会显示该程序编译后的大小，如图10 - 11所示。

![](images/txt014_13.jpg "pagenumber_ebook=162,pagenumber_book=150")

图10-11　对Arduino程序进行编译

6. 程序上传

单击“上传”按钮将编译成功的程序上传到Arduino Uno开发板，在程序上传过程中，控制板的串口指示灯（RX和TX）会不停闪烁。程序上传完成后，Arduino开发环境的状态栏会提示“上传完成”，同时Arduino Uno开发板上的LED灯会不停闪烁。

到此为止，通过一个简单的例子，读者已经了解Arduino的使用方法。

## 第11章　Arduino的常用函数

Arduino提供大量基础函数，包括I/O控制函数、时间函数、中断函数、数学函数、串口通信函数等，使用者可以方便地对开发板上的资源进行控制。另外，Arduino还提供了相关示例程序，可以在Arduino开发环境的“文件”→“示例”菜单中找到。本章介绍Arduino提供的常用基础函数。

### 11.1　模拟I/O口的操作函数

#### 11.1.1 `analogReference(type)`

`analogReference`函数的作用是配置模拟输入引脚的基准电压（即输入范围的最大值），它是一个无返回值函数，只有一个参数`type`。`type`的选项有`DEFAULT`、`INTERNAL`、`INTERNAL1V1`、`INTERNAL2V56`、`EXTERNAL`，其具体含义如下：

（1）`DEFAULT`：默认5 V或3.3 V为基准电压（以Arduino开发板的电压为准）。

（2）`INTERNAL`：低电压模式，使用片内基准电压源（Arduino Mega无此选项）。

（3）`INTERNAL1V1`：低电压模式，以1.1 V为基准电压（此选项仅针对Arduino Mega）。

（4）`INTERNAL2V56`：低电压模式，以2.56 V为基准电压（此选项仅针对Arduino Mega）。

（5）`EXTERNAL`：扩展模式，以AREF引脚（0～5 V）的电压作为基准电压，其中AREF引脚的位置如图11 - 1所示。

![](images/txt015_1.jpg "pagenumber_ebook=163,pagenumber_book=151")

图11-1　AREF引脚的位置

设置模拟输入引脚的基准电压为默认值的语句如下：

![](images/txt015_2.jpg "pagenumber_ebook=164,pagenumber_book=152")

#### 11.1.2 `analogRead(pin)`

`analogRead`函数的作用是从指定的模拟引脚读取值，读取周期为100 μs，即最大读取速度可达每秒10 000次。参数`pin`表示读取的模拟输入引脚号，返回值为`int`型（范围为0～1 023）。

Arduino Uno主板有6个通道（Arduino Mega有16个）10位模/数（A/D）转换器，即精度为10位，返回值是0～1 023。也就是说输入电压为5 V时读取精度为5 V/1 024个单位，约等于每个单位0.049 V（4.9 mV）。输入范围和进度可通过`analogReference`函数进行修改。

如输入电压为`a`，那么获取模拟输入引脚4的电压值的示例程序代码如下：

![](images/txt015_3.jpg "pagenumber_ebook=164,pagenumber_book=152")

注意：对Arduino Uno而言，函数参数的`pin`范围是0～5，对应开发板上的模拟口为A0～A5。其他型号的Arduino开发板以此类推。

#### 11.1.3 `analogWrite(pin, value)`

`analogWrite`函数的作用是通过PWM的方式将模拟值输出到引脚，即调用`analogWrite`函数后，相应引脚将产生一个指定占空比的稳定方波（频率大约为490 Hz），直到下一次调用该函数。该函数可应用在LED灯亮度调节、电动机速度控制等方面。

`analogWrite`函数是无返回值函数，有两个参数`pin`和`value`。参数`pin`表示将输出PWM的引脚，这里只能选择函数支持的引脚。对于大多数Arduino开发板（板载ATmega168或ATmega328），这个函数支持引脚3、5、6、9、10和11，对于ArduinoMega，它适用于2～13号引脚。参数`value`表示PWM输出的占空比，因为PWM输出位数为8，所以其范围为0（常闭）～255（常开），对应占空比为0～100%。带PWM功能的引脚均标有波浪号“～”。

从引脚9输出PWM的示例程序代码如下：

![](images/txt015_4.jpg "pagenumber_ebook=165,pagenumber_book=153")

注意：引脚5和6的PWM输出将产生高于预期的占空比。这是因为`millis`和`delay`函数共享同一个内部计时器，使内部计时器在处理PWM输出时分心。这种情况一般出现在低占空比设置，如0～10的情况下。还有些情况是占空比为0时，引脚5和6并没有关闭输出。

### 11.2　数字I/O口的操作函数

#### 11.2.1 `pinMode(pin, mode)`

`pinMode`函数用于配置引脚为输入或输出模式，它是一个无返回值函数，一般放在`setup`里，先设置再使用。

`pinMode`函数有两个参数——`pin`和`mode`。`pin`参数表示要配置的引脚，以Arduino Uno为例，它的范围是数字引脚0～13，也可以把模拟引脚（A0～A5）作为数字引脚使用，此时编号为14（对应模拟引脚0）到19（对应模拟引脚5）。`mode`参数表示设置的模式——`INPUT`（输入）或`OUTPUT`（输出），其中`INPUT`用于读取信号，`OUTPUT`用于输出控制信号。

配置数字引脚8为输出模式的语句如下：

![](images/txt015_5.jpg "pagenumber_ebook=165,pagenumber_book=153")

#### 11.2.2 `digitalWrite(pin, value)`

`digitalWrite`函数的作用是设置引脚的输出电压为高电平或低电平，它也是一个无返回值函数，在使用该函数设置引脚之前，需要先用`pinMode`函数将引脚设置为`OUTPUT`模式。

`digitalWrite`函数有两个参数——`pin`和`value`。`pin`参数表示所要设置的引脚，`value`参数表示输出的电压——`HIGH`（高电平）或`LOW`（低电平）。

配置数字引脚8的输出电平为高电平的语句如下：

![](images/txt015_6.jpg "pagenumber_ebook=165,pagenumber_book=153")

![](images/txt015_7.jpg "pagenumber_ebook=166,pagenumber_book=154")

#### 11.2.3 `digitalRead(pin)`

`digitalRead`函数的作用是获取引脚的电压情况，该函数返回值为`int`型——`HIGH`（高电平）或者`LOW`（低电平）。在使用该函数设置引脚之前，需要先用`pinMode`函数将引脚设置为`INPUT`模式。

`digitalRead`函数只有一个参数——`pin`，它表示所要获取电压情况的引脚号，如果引脚没有连接到任何地方，那么将随机返回`HIGH`（高电平）或者`LOW`（低电平）。

获取数字引脚8的电压情况的语句如下：

![](images/txt015_8.jpg "pagenumber_ebook=166,pagenumber_book=154")

### 11.3　高级I/O函数

#### 11.3.1 `pulseIn(pin, state, timeout)`

`pulseIn`函数用于读取指定引脚的脉冲持续的时间长度，该函数返回值类型为无符号长整型（`unsigned long`），单位为ms，如果超时没有读到，则返回0。

`pulseIn`函数包含3个参数——`pin`、`state`、`timeout`。参数`pin`代表脉冲输入的引脚；参数`state`代表脉冲响应的状态，脉冲可以是`HIGH`或者`LOW`，如果是`HIGH`，则`pulseIn`函数先等引脚变为高电平，然后开始计时，一直到引脚变为低电平；参数`timeout`代表超时时间。

做一个按钮脉冲计时器，测一下按钮的时间，看谁的反应快，即看谁能按出最短的时间，其中按钮接引脚4。示例程序代码如下：

![](images/txt015_9.jpg "pagenumber_ebook=166,pagenumber_book=154")

#### 11.3.2 `shiftOut(dataPin, clockPin, bitOrder, val)`

`shiftOut`函数的作用是将一个数据的一个字节逐位移出，它是一个无返回值函数。从最高有效位（最左边）或最低有效位（最右边）开始，依次向数据脚写入每一位，之后时钟脚被拉高或拉低，指示刚才的数据有效。
