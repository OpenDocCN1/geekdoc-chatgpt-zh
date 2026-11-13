```
#include "IRremote.h"// 定义头文件 IRremote.h。
IRsend irsend;// 新建一个 IRsend 对象。
void setup(){
pinMode(2,OUTPUT);// 设置数字端口 2 为输出模式。
pinMode(2,INPUT);// 设置数字端口 2 为输入模式。
digitalWrite(2,1);// 设置数字端口 2 为高电平。
pinMode(4,OUTPUT);// 设置数字端口 4 为输出模式。
digitalWrite(4,0);// 设置数字端口 4 输出低电平。
}
void loop(){
digitalWrite(2,1);// 设置数字端口 2 为高电平。
if(digitalRead(2)==0)// 如果数字端口 2 为低电平，
irsend.sendNEC(0xFF30CF,32);// 发射 0xFF30CF 信号。
delay(500);// 延时 500ms。
}
}
```