
## 2. 语句 runtime = millis();

该语句用于记录当前机器的运行时间，millis 函数用于返回从 Arduino Uno 开发板开始运行到当前的毫秒数。

```cpp
if (digitalRead(13) == 0 and (millis() - runtime) > 100 and buttonpress) {
  // 语句 1；如果曾经被按下的按键已抬起的时间距离上次按键时间大于 100ms，那么执行语句 1。
}
```

## 2.33.4 程序设计

### 1. 代码一

### （1）程序参考

```cpp
int secs = 0; // 定义整型变量 secs（秒数）。
int s1 = 0; // 定义整型变量 s1（秒数的个位）。
int s2 = 0; // 定义整型变量 s2（秒数的十位）。
int mins = 0; // 定义整型变量 mins（分钟数）。
int m1 = 0; // 定义整型变量 m1（分钟数的个位）。
int m2 = 0; // 定义整型变量 m2（分钟数的十位）。
bool flag; // 定义布尔变量 flag（模式数）。
char ledpin[] = {2, 4, 8, 7, 6, 3, 9}; // 设置数码管引脚对应的数字端口。
unsigned char num[10] = {0x3f, 0x06, 0x5b, 0x4f, 0x66, 0x6d, 0x7d, 0x07, 0x7f, 0x6f}; // 设置数字 0 ~ 9 对应的段码值。

void setup() {
  for (int i = 0; i < 14; i++) // 循环执行，从 i=0 开始，到 i=13 结束。
    pinMode(i, OUTPUT); // 设置数字端口 0 ~ 13 为输出模式。
  pinMode(13, INPUT); // 设置数字端口 13 为输入模式。
  digitalWrite(5, 1); // 点亮分隔符。
}

void loop() {
  scan(); // 逐位显示程序。
  if (flag == 1) // 如果布尔变量 flag==1，开始计时。
  {
    secs = (secs + 1) % 60; // 秒数加 1。
    s1 = secs % 10; // s1 = 秒数的个位。
    s2 = secs / 10; // s2 = 秒数的十位。
    m1 = mins % 10; // m1 = 分钟数的个位。
    m2 = mins / 10; // m2 = 分钟数的十位。
    if (secs == 59) // 如果秒数到 59，
      mins = (mins + 1) % 100; // 分钟数加 1。
  }
}

void disp(unsigned char value) {
  for (int i = 0; i < 7; i++)
    digitalWrite(ledpin[i], bitRead(value, i));
  // 将变量 value 的第 i 位数据传输给数组 ledpin 对应的第 i 个数字端口。
  // 共阳极数码管使用 !bitRead(value,i)。
}

void disp0() // 显示子程序 disp0，清除数码管余辉效应。
{
  for (int i = 0; i < 7; i++)
    digitalWrite(ledpin[i], 0);
}

void scan() // 逐位显示程序。
{
  for (int j = 0; j < 49; j++) // 循环执行 49 次。
  {
    digitalWrite(13, 1); // 数字端口 13 输出高电平。
    if (digitalRead(13) == 0) // 如果数字端口 13 为低电平，
      flag = !flag; // 那么布尔变量取反。
    delay(250); // 延时 250ms。
  }

  digitalWrite(12, 1); // 设置数字端口 12 为高电平，打开秒数的个位。
  digitalWrite(11, 0); // 设置数字端口 11 为低电平。
  digitalWrite(1, 0); // 设置数字端口 1 为低电平。
  digitalWrite(0, 0); // 设置数字端口 0 为低电平。
  disp(num[s1]); // 显示秒数的个位。
  delay(5); // 延时 5ms。
  disp0(); // 清除数码管余辉效应。
  digitalWrite(12, 0); // 设置数字端口 12 为低电平。

  digitalWrite(11, 1); // 设置数字端口 11 为高电平，打开秒数的十位。
  digitalWrite(1, 0); // 设置数字端口 1 为低电平。
  digitalWrite(0, 0); // 设置数字端口 0 为低电平。
  disp(num[s2]); // 显示秒数的十位。
  delay(5); // 延时 5ms。
  disp(0); // 清除数码管余辉效应。
  digitalWrite(12, 0); // 设置数字端口 12 为低电平。
  digitalWrite(11, 0); // 设置数字端口 11 为低电平。
  digitalWrite(1, 1); // 设置数字端口 1 为高电平，打开分钟数的个位。
  digitalWrite(0, 0); // 设置数字端口 0 为低电平。
  disp(num[m1]); // 显示分钟数的个位。
  delay(5); // 延时 5ms。
  disp(0); // 清除数码管余辉效应。
  digitalWrite(12, 0); // 设置数字端口 12 为低电平。
  digitalWrite(11, 0); // 设置数字端口 11 为低电平。
  digitalWrite(1, 0); // 设置数字端口 1 为低电平。
  digitalWrite(0, 1); // 设置数字端口 0 为高电平，打开分钟数的十位。
  disp(num[m2]); // 显示分钟数的十位。
  delay(5); // 延时 5ms。
  disp(0); // 清除数码管余辉效应。
}
```

### （2）实验结果

此代码运行结果为 99 分 59 秒计时器。接通电源，数码管显示 00:00，按一下计时键，开始计时，前两位为分钟数，后两位为秒数，再按一下计时键，停止计时，再按一下计时按键，继续计时，如果按一下复位键，数码管显示 00:00，最多可计时 99 分 59 秒。

### 2. 代码二

### （1）程序参考

```cpp
#include "SevSeg.h" // 定义头文件 SevSeg.h。

SevSeg sevseg;

byte numDigits = 4; // 数码管位数为 4 位。

byte digitPins[] = {0, 1, 11, 12}; // 数码管公共极引脚连接数字端口。

byte segmentPins[] = {2, 4, 8, 7, 6, 3, 9};
// 数码管引脚 a、b、c、d、e、f、g 连接数字端口。

byte hardwareConfig = 2; // 共阴极数码管公共极接高电平时导通，数码管点亮。
int val = 0; // 定义整型变量 val=0，用于记录计数值。
bool buttonpress = false; // 定义布尔变量 buttonpress=false，记录按键按下否。
unsigned long runtime; // 定义长整型变量 runtime，记录机器运行时间。

void setup() {
  sevseg.begin(hardwareConfig, numDigits, digitPins, segmentPins);
  // 数码管初始化。
  pinMode(13, OUTPUT); // 设置数字端口 13 为输出模式。
  digitalWrite(13, 1); // 数字端口 13 输出高电平。
}

void loop() {
  sevseg.setNumber(val, -1); // 设置要显示的数据不显示小数点。
  sevseg.refreshDisplay(); // 数码管刷新显示数据，程序中如有延时将影响显示。
  if (digitalRead(13) == 0) { // 如果数字端口 13 为低电平，表示按键状态为已按下。
    buttonpress = true; // 按键状态为已按下。
    runtime = millis(); // 记录当前机器的运行时间。
    // millis 函数用于返回从 Arduino Uno 开发板开始运行到当前的毫秒数。
  }
  if (digitalRead(13) != 0 and (millis() - runtime) > 100 and buttonpress) {
    // 如果曾经被按下的按键已抬起的时间距离上次按键时间大于 100ms，
    val = (val + 1) % 10000; // 整型变量 val 加 1，除以 10000 取余数。
    buttonpress = false; // 恢复按键状态为抬起。
  }
}
```

### (2) 实验结果

代码运行结果为 9999 计数器。接通电源，数码管显示 0000，按一下按键，计数器示数增加 1，再按一下按键，计数器示数又增加 1。此计数器最多可计数到 9999。

### 3. 代码三

### (1) 程序参考

```cpp
#include "TimerOne.h" // 定义头文件 TimerOne.h。
int secs = 0; // 定义整型变量 secs（秒数）。
int mins = 0; // 定义整型变量 mins（分钟数）。
int m1 = 0; // 定义整型变量 m1（分钟数的个位）。
int m2 = 0; // 定义整型变量 m2（分钟数的十位）。

int hours = 0; // 定义整型变量 hours（小时数）。
int h1 = 0; // 定义整型变量 h1（小时数的个位）。
int h2 = 0; // 定义整型变量 h2（小时数的十位）。
int flag = 0; // 定义整型变量 flag（模式数），初始化赋值为 0。
int mod; // 定义整型变量 mod（扫描显示屏次数）。
unsigned long runtime; // 定义长整型变量 runtime，记录机器运行时间。
char ledpin[] = {2, 4, 8, 7, 6, 3, 9}; // 设置数码管引脚对应的数字端口。
unsigned char num[10] = {0x3f, 0x06, 0x5b, 0x4f, 0x66, 0x6d, 0x7d, 0x07, 0x7f, 0x6f}; // 设置数字 0 ~ 9 对应的段码值。

void setup() {
  for (int i = 0; i < 14; i++) // 循环执行，从 i=0 开始，到 i=13 结束。
    pinMode(i, OUTPUT); // 设置数字端口 0 ~ 13 为输出模式。

  pinMode(13, INPUT); // 设置数字端口 13 为输入模式。
  digitalWrite(5, 1); // 点亮分隔符。
  Timer1.initialize(1000000); // 设置定时器中断时间为 1000000 μs 即 1s。
  Timer1.attachInterrupt(callback);

  // 设置定时器中断服务函数，每发生一次定时器中断，都会执行一次该函数。
}

void callback() // 中断函数每秒执行一次。
{
  secs = (secs + 1) % 60; // 秒数加 1。
  if (secs == 0) // 如果秒数到 00，
    mins = (mins + 1) % 60; // 分钟数加 1。
  if (mins == 0) // 如果分钟数到 00，
    hours = (hours + 1) % 24; // 小时数加 1。
}

void loop() {
  if (flag == 0) // 如果运行模式为 0，正常计时。
  {
    mod = 50; // 显示小时数和分钟数。
    scan(); // 逐位显示程序。
    m1 = mins % 10; // m1 = 分钟数的个位。
    m2 = mins / 10; // m2 = 分钟数的十位。

    h1 = hours % 10; // h1 = 小时数的个位。
    h2 = hours / 10; // h2 = 小时数的十位。
  }

  if (flag == 1) // 如果运行模式为 1，调整分钟数。
  {
    mins = (mins + 1) % 60; // 分钟数加 1。
    m1 = mins % 10; // m1 = 分钟数的个位。
    m2 = mins / 10; // m2 = 分钟数的十位。
    mod = 30; // 显示分钟数每 0.5s 自动加 1。
    scan(); // 逐位显示程序。
  }

  if (flag == 2) // 如果运行模式为 2，调整小时数。
  {
    hours = (hours + 1) % 24; // 小时数加 1。
    h1 = hours % 10; // h1 = 小时数的个位。
    h2 = hours / 10; // h2 = 小时数的十位。
    mod = 30; // 显示小时数每 0.5s 自动加 1。
    scan(); // 逐位显示程序。
  }
}

void disp(unsigned char value) // 显示子程序 disp。
{
  for (int i = 0; i < 7; i++)
    digitalWrite(ledpin[i], bitRead(value, i));
  // 将变量 value 的第 i 位数据传输给数组 ledpin 对应的第 i 个数字端口。
  // 共阳极数码管使用 !bitRead(value,i)。
}

void disp0() // 显示子程序 disp0。
{
  for (int i = 0; i < 7; i++)
    digitalWrite(ledpin[i], 0);
}

void scan() // 逐位显示程序。
{
  for (int j = 0; j < mod; j++) // 循环执行 mod 次。
  {
    digitalWrite(13, 1); // 数字端口 13 输出高电平。
    if (digitalRead(13) == 0) // 如果数字端口 13 为低电平，
    {
      delay(250); // 延时 250ms。
      flag = (flag + 1) % 3; // 运行模式加 1。
      break; // 退出循环。
    }
  }

  control(0, 0, 0, 1); // 设置数字端口 12 为高电平，显示分钟数的个位。
  disp(num[m1]); // 显示分钟数的个位。
  delay(5); // 延时 5ms。
  disp(0);

  control(0, 0, 1, 0); // 设置数字端口 11 为高电平，显示分钟数的十位。
  disp(num[m2]); // 显示分钟数的十位。
  delay(5); // 延时 5ms。
  disp(0);

  control(0, 1, 0, 0); // 设置数字端口 1 为高电平，显示小时数的个位。
  disp(num[h1]); // 显示小时数的个位。
  delay(5); // 延时 5ms。
  disp(0);

  control(1, 0, 0, 0); // 设置数字端口 0 为高电平，显示小时数的十位。
  disp(num[h2]); // 显示小时数的十位。
  delay(5); // 延时 5ms。
  disp(0);
}

void control(int pin0, int pin1, int pin11, int pin12) {
  // 定义控制位引脚函数。
  digitalWrite(0, pin0);
  digitalWrite(1, pin1);
  digitalWrite(11, pin11);
  digitalWrite(12, pin12);
}
```

### (2) 实验结果

代码运行结果为电子时钟。接通电源，数码管显示 00:00，第 1 次按下按键，进入分钟数自动加 1 模式，第 2 次按下按键，进入小时数自动加 1 模式，第 3 次按下按键，进入正常时钟模式。如此循环。比如，设置时间 20:35，第 1 次按下按键，让分钟数自动加到 35，第 2 次按下按键，让小时数自动加到 20，第 3 次按下按键，进入正常时钟状态，即当前时间是 20:35。

## 2.33.5 拓展和挑战

将光电传感器（型号为 E18-D80NK）的 3 条引线连接到 Arduino Uno 开发板上，光电传感器的 VCC 引线连接 Arduino Uno 开发板的端口 5V，光电传感器的 OUT 引线连接 Arduino Uno 开发板的端口 13，光电传感器的 GND 引线连接 Arduino Uno 开发板的端口 GND，如图 2.39 所示。

![](img/06859067f3402277041d47b4a4e48a1b_332_0.png)

输入 2.33.4 节的代码二，可用于非接触类计数场合。

## 2.34 六位数字显示时钟

我们学习了四位数字显示计时器、计数器、电子时钟的编程方法，下面学习六位数字显示时钟的编程方法。

### 2.34.1 实验描述

运用六位数码管显示时、分、秒，通过按键设置并校准时间。

AU34 的电路原理图、电路板图和实物图如图 2.40 所示。

### 六位共阴极数码管

R1~R8 = 390Ω

FJS361AH

ULN2003

(a) 电路原理图

### 图 2.40 AU34 的电路原理图、电路板图和实物图

![](img/06859067f3402277041d47b4a4e48a1b_336_0.png)

### (b) 电路板图

![](img/06859067f3402277041d47b4a4e48a1b_336_1.png)

### (c) 实物图

图 2.40 AU34 的电路原理图、电路板图和实物图（续）

### 2.34.2 知识要点

六位数字显示时钟即运用六位数码管显示时、分、秒，用于显示当前时间信息，可通过按键设置并校准时间。

### 2.34.3 编程要点

### 1. 模式切换

模式切换即多种运行模式按键切换技术，是学习 Arduino 编程应知、应会的知识。

多种运行模式按键切换的编程方法如下。

首先，设置按键端口为高电平，用语句 `digitalWrite(13, 1);` 。

其次，判断按键是否被按下，如果按键端口为低电平，表示按键已被按下，那么运行模式加 1，为避免切换速度过快，延时 250ms，最后退出循环，用语句 `if (digitalRead(0) == 0) { delay(250); flag = (flag + 1) % 3; break; }` 。

本实验为六位数字显示时钟，运行模式 1 为分钟数自动加 1 模式，运行模式 2 为小时数自动加 1 模式，运行模式 3 为正常时钟模式。

### 2. 消除共阴极数码管余辉效应

![](img/06859067f3402277041d47b4a4e48a1b_337_0.png)

### 3. 定时器与中断函数

Arduino 定时器包含定时器/计数器 0（8 位，计数范围为 0～255）、定时器/计数器 1（16 位，计数范围为 0～65535）、定时器/计数器 2（8 位）共 3 个。中断即暂时停止当前事件 1，去执行或处理事件 2（中断响应和中断服务），将事件 2 处理完毕，再回到原来的事件 1 继续执行（中断返回）。定时器中断是指运用定时器来让中断定时发生，用于精确控制时间。
