在 void setup ( ) { } 的花括号内，常用语句pinMode ( 13, OUTPUT ) ; 表示设置数字端口 13 为输出模式，用语句 pinMode ( 13, INPUT) ; 表示设置数字端口13为输入模式。OUTPUT 表示输出，INPUT表示输入，在Arduino编程语言中，输出就是开发板上的微控制器给设备或端口发出信号，输入就是开发板上的微控制器接收设备或端口发来信号。

# 2.语句digitalWrite ( 13, 1) ; 与digitalWrite ( 13, 0) ;

语句digitalWrite ( 13, 1) ; 表示数字端口13输出高电平。

语句digitalWrite ( 13, 0) ; 表示数字端口13输出低电平。

数字1表示高电平。输出高电平表示输出电压约为5V，语句 digitalWrite ( 13, 1) ; 与语句digitalWrite ( 13, HIGH ) ; 效果相同，HIGH表示高电平。

数字0表示低电平。输出低电平表示输出电压约为0V，语句 digitalWrite ( 13, 0) ; 与语句digitalWrite ( 13, LOW ) ; 效果相同，LOW表示低电平。

# 3.语句digitalRead ( 13) ; 与! digitalRead ( 13) ;

语句digitalRead ( 13) ; 表示读取数字端口13的状态（高电平或低电平）。

语句! digitalRead ( 13) ; 表示读取数字端口13的状态并取反，如果数字端口13为高电平，取反之后将变为低电平；如果为低电平，取反之后将变为高电平。

语句digitalWrite ( 13, ! digitalRead ( 13) ) ; 表示数字端口13输出状态取反。

# 4.Arduino Uno开发板数字输出端口的编程方法

第一步，在setup函数中，设置数字端口为输出模式。

```
void setup(){pinMode(13,OUTPUT);}
```