```
#include <Servo.h> // 定义头文件 Servo.h。
#include <LiquidCrystal.h>// 定义头文件 LiquidCrystal.h。
byte digitPins[]={0,1,11,12};// 数码管公共极引脚连接数字端口。
byte hardwareConfig=2;// 共阴极数码管公共极接高电平时导通，引脚接高电平时点亮。
byte numDigits=4;// 数码管位数为 4 位。
byte segmentPins[]={2,4,8,7,6,3,9};// 数码管引脚 a、b、c、d、e、f、g 连接数字端口。
char ledpin[]={9,8,7,12,13,10,11};// 设置数码管引脚对应的数字端口。
decode_results results;// 一个 decode_results 类的对象。
digitalWrite(13,1);if(digitalRead(13)==0){delay(250);flag=(flag+1)%3;break;} // 按键切换运行模式，用于设置 3 种运行模式；延时 250ms，目的是避免切换速度过快； break 用于退出 do、for、while 循环和 switch 语句。
digitalWrite(Da,num[i][0]);// 将数组 num 中第 i 行第 0 列的数据赋给数码管引脚 a。
float duration[]={};// 定义单精度浮点变量数组 duration，排列音符的音长。
int flag=0;// 定义整型变量 flag（模式数），初始化赋值为 0。
int index=0;// 定义整型变量 index，初始化赋值为 0。
int tune[]={};// 定义整型变量数组 tune，排列各音符的音调。
int val=0;// 定义整型变量 val，初始化赋值为 0。
int val;// 定义整型变量 val，有符号整型变量的取值范围为 -32768 ~ 32767，即 -2^15 ~ 2^15 -1。
IRrecv irrecv(2);// IRrecv 类构造函数，红外接收头输出引脚连接数字端口 2。
LiquidCrystal lcd(3,4,5,8,9,10,11);// 创 建 LiquidCrystal(rs, rw, enable, d4, d5, d6, d7) 类实例 lcd。
long Seco=0;// 定义长整型变量 Seco（秒数），有符号长整型变量的取值范围为 -2147483648 ~ 2147483647，即 -2^31 ~ 2^31 -1。
Servo servo13;// 定义舵机变量名为 servo13。
Unsigned char num[10][7]={};// 定义 10 行 7 列的无符号字符型数组。
```