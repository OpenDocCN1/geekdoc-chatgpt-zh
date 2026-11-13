
管的电路原理图，数码管a、b、c、d、e、f引脚设置高电平，数码管g引脚设置低电平，可显示数字0；其他数字显示原理照此类推。

### 2.char（字符或字符串）与unsigned char（无符号字符型）

char是有符号数据类型，占用1字节的内存，编码值为-128～127，存储一个字符（用单引号表示，如'A'，大写A的ASCII值是65）或多个字符串（用双引号表示，如“ABC”）。

unsigned char是无符号字符型，占用1字节的内存，编码值为0～255。

### 3.语句deal（num[i]）；与语句digitalWrite（Da，num[i][0]）；

语句deal（num[i]）；表示调用deal子程序。num[i]表示一维数组num的第i个元素。

语句digitalWrite（Da，num[i][0]）；表示将二维数组num中第i行第0列的数据传输给数码管引脚a。

### 4.语句digitalWrite（ledpin[j]，bitRead（value， j））；

该语句表示将变量value转换为二进制数，将二进制数的第j位传输给数组ledpin的第j个元素对应的数字端口，其中value=num[i]表示数组num的第i个元素，如i=0时，num[0]=0x3f。

语法：

```
bitRead(x,n)//x是读取的目标数据，n是目标数据转换为二进制数后从右往左数第 n位。
```

### 5.一位数码管显示的编程方法

方法一，调用显示数字子程序法，根据数码管的电路原理图，运用语句digitalWrite（）；分别设置数码管引脚为高电平或低电平，编写显示数字子程序，详见2.17.4节的代码一。

方法二，定义二维数组，运用for循环语句将数组中第i行第0列～第i行第6列的数据分别传输给数码管引脚a～g，详见2.17.4节的代码二。

方法三，定义一维数组，运用for循环语句将数组中的第i+1个元素转换为二进制数后，将二进制数的第0～6位数据分别传输给数码管引脚a～g，详见2.17.4节的代码三。

## 2.17.4 程序设计

### 1.代码一

### (1) 程序参考

```
void setup(){
pinMode(7,OUTPUT);// 设置数字端口 7 为输出模式。
pinMode(8,OUTPUT);// 设置数字端口 8 为输出模式。
pinMode(9,OUTPUT);// 设置数字端口 9 为输出模式。
pinMode(10,OUTPUT);// 设置数字端口 10 为输出模式。
pinMode(11,OUTPUT);// 设置数字端口 11 为输出模式。
pinMode(12,OUTPUT);// 设置数字端口 12 为输出模式。
pinMode(13,OUTPUT);// 设置数字端口 13 为输出模式。
}

void loop(){
mod0();// 调用显示数字 0 子程序。
delay(1000);// 延时 1000ms。
mod1();// 调用显示数字 1 子程序。
delay(1000);// 延时 1000ms。
mod2();// 调用显示数字 2 子程序。
delay(1000);// 延时 1000ms。
mod3();// 调用显示数字 3 子程序。
delay(1000);// 延时 1000ms。
mod4();// 调用显示数字 4 子程序。
delay(1000);// 延时 1000ms。
mod5();// 调用显示数字 5 子程序。
delay(1000);// 延时 1000ms。
mod6();// 调用显示数字 6 子程序。
delay(1000);// 延时 1000ms。
mod7();// 调用显示数字 7 子程序。
delay(1000);// 延时 1000ms。
mod8();// 调用显示数字 8 子程序。
delay(1000);// 延时 1000ms。
mod9();// 调用显示数字 9 子程序。
delay(1000);// 延时 1000ms。
}
void mod0() { // 显示数字 0 子程序。
digitalWrite(7,1);
digitalWrite(8,1);
digitalWrite(9,1);
digitalWrite(10,1);
digitalWrite(11,0);
digitalWrite(12,1);
digitalWrite(13,1);
}
void mod1() { // 显示数字 1 子程序。
digitalWrite(7,1);
digitalWrite(8,1);
digitalWrite(9,0);
digitalWrite(10,0);
digitalWrite(11,0);
digitalWrite(12,0);
digitalWrite(13,0);
}
void mod2() { // 显示数字 2 子程序。
digitalWrite(7,0);
digitalWrite(8,1);
digitalWrite(9,1);
digitalWrite(10,0);
digitalWrite(11,1);
digitalWrite(12,1);
digitalWrite(13,1);
}
void mod3() { // 显示数字 3 子程序。
digitalWrite(7,1);
digitalWrite(8,1);
digitalWrite(9,1);
digitalWrite(10,0);
digitalWrite(11,1);
digitalWrite(12,1);
digitalWrite(13,0);
}
void mod4() { // 显示数字 4 子程序。
digitalWrite(7,1);
digitalWrite(8,1);
digitalWrite(9,0);
digitalWrite(10,1);
digitalWrite(11,1);
digitalWrite(12,0);
digitalWrite(13,0);
}
void mod5() { // 显示数字 5 子程序。
digitalWrite(7,1);
digitalWrite(8,0);
digitalWrite(9,1);
digitalWrite(10,1);
digitalWrite(11,1);
digitalWrite(12,1);
digitalWrite(13,0);
}
void mod6() { // 显示数字 6 子程序。
digitalWrite(7,1);
digitalWrite(8,0);
digitalWrite(9,1);
digitalWrite(10,1);
digitalWrite(11,1);
digitalWrite(12,1);
digitalWrite(13,1);
}
void mod7() { // 显示数字 7 子程序。
    digitalWrite(7,1);
    digitalWrite(8,1);
    digitalWrite(9,1);
    digitalWrite(10,0);
    digitalWrite(11,0);
    digitalWrite(12,0);
    digitalWrite(13,0);
}
void mod8() { // 显示数字 8 子程序。
    digitalWrite(7,1);
    digitalWrite(8,1);
    digitalWrite(9,1);
    digitalWrite(10,1);
    digitalWrite(11,1);
    digitalWrite(12,1);
    digitalWrite(13,1);
}
void mod9() { // 显示数字 9 子程序。
    digitalWrite(7,1);
    digitalWrite(8,1);
    digitalWrite(9,1);
    digitalWrite(10,1);
    digitalWrite(11,1);
    digitalWrite(12,1);
    digitalWrite(13,0);
}
```

### (2) 实验结果

接通电源，数码管按顺序循环显示0~9，间隔时间为1s。

### 2.代码二

### (1) 程序参考

```
#define Da 9// 定义变量 Da=9( 数码管引脚 a 接数字端口 9)。
#define Db 8// 定义变量 Db=8( 数码管引脚 b 接数字端口 8)。
#define Dc 7// 定义变量 Dc=7( 数码管引脚 c 接数字端口 7)。
#define Dd 12// 定义变量 Dd=12( 数码管引脚 d 接数字端口 12)。
#define De 13// 定义变量 De=13( 数码管引脚 e 接数字端口 13)。
#define Df 10// 定义变量 Df=10( 数码管引脚 f 接数字端口 10)。
#define Dg 11// 定义变量 Dg=11( 数码管引脚 g 接数字端口 11)。
unsigned char num[10][7]={// 定义无符号字符型的 10 行 7 列数组。
//1 为点亮， 0 为熄灭， a 表示数码管引脚 a， 其他类推。
//abcdefg
{1,1,1,1,1,1,0,}//0
{0,1,1,0,0,0,0,}//1
{1,1,0,1,1,0,1,}//2
{1,1,1,1,0,0,1,}//3
{0,1,1,0,0,1,1,}//4
{1,0,1,1,0,1,1,}//5
{1,0,1,1,1,1,1,}//6
{1,1,1,0,0,0,0,}//7
{1,1,1,1,1,1,1,}//8
{1,1,1,1,0,1,1,}//9
};
void setup(){
for(int i=7;i<14;i++ ){// 循环执行， 从 i=7 开始， 到 i=13 结束。
pinMode(i,OUTPUT);// 设置数字端口 7 ~ 13 为输出模式。
}
}
void loop(){
for(int i=0;i<10;i++ ){// 循环显示 0 ~ 9。
digitalWrite(Da,num[i][0]);// 将数组 num 中第 i 行第 0 列的数据传输给数码管引脚 a。
digitalWrite(Db,num[i][1]);// 将数组 num 中第 i 行第 1 列的数据传输给数码管引脚 b。
digitalWrite(Dc,num[i][2]);// 将数组 num 中第 i 行第 2 列的数据传输给数码管引脚 c。
digitalWrite(Dd,num[i][3]);// 将数组 num 中第 i 行第 3 列的数据传输给数码管引脚 d。
digitalWrite(De,num[i][4]);// 将数组 num 中第 i 行第 4 列的数据传输给数码管引脚 e。
digitalWrite(Df,num[i][5]);// 将数组 num 中第 i 行第 5 列的数据传输给数码管引脚 f。
digitalWrite(Dg,num[i][6]);// 将数组 num 中第 i 行第 6 列的数据传输给数码管引脚 g。
delay(1000);// 延时 1000ms。
}
}
```

### (2) 实验结果

接通电源，数码管按顺序循环显示0~9，间隔时间为1s。

### 3.代码三

### (1) 程序参考

```
char ledpin[]={9,8,7,12,13,10,11};// 设置数码管引脚对应的数字端口。
unsigned char num[10] = {
0x3f,0x06,0x5b,0x4f,0x66,0x6d,0x7d,0x07,0x7f,0x6f
};
void setup(){
for(int i=0;i<7;i++){
pinMode(ledpin[i],OUTPUT);// 设置数码管引脚对应的端口为输出模式。
}
}
void loop(){
for(int i=0;i<10;i++){
deal(num[i]);// 调用 deal 子程序， num[i] 对应第 i 个段码值。
delay(1000);// 延时 1000ms。
}
}
void deal(unsigned char value) // 子程序 deal。
{
for(int j=0;j<7;j++)
digitalWrite(ledpin[j],bitRead(value,j));
// 将变量 value 的值转换为二进制数， 将二进制数的第 j 位数据传输给数组 ledpin 对应的
第 j 个数字端口。
// 共阳极数码管使用 !bitRead(value,j)。
}
```

- (2) 实验结果

接通电源，数码管按顺序循环显示0～9，间隔时间为1s。

## 2.17.5 拓展和挑战

- (1) 数码管按9～0顺序循环显示，间隔时间为1s。

- (2) 数码管按顺序循环显示A～F，间隔时间为1s。

## 2.18 一位数字显示测光仪

我们还能让数码管干点什么别的呢？结合此前学习过的光敏模块、热敏模块知识，还可以让数码管显示光线亮度等级（测光仪）、温度高低等级（测温仪）。

在日常生活中，光线亮度直接影响人们的用眼卫生，尤其对于用眼强度很大的学生，光线太亮或太暗都容易导致视力受损。

## 2.18.1 实验描述

运用光敏模块和一位数字显示模块检测光线的亮度等级。

AU17、AU04B的电路原理图、电路板图和实物图如图2.23所示。

### 共阴极数码管 5611AH

R1~R7 = 390Ω
8 R1
9 R2
10 R3
11 R4
13 R5
12 R6
7 R7
GND
a b
f g
e d
dp

a9
f10 b8
g11
e13 c7
d12

光敏模块
5V R15 RG
A0
10kΩ R16
GND

(a) 电路原理图 (左图为AU17、右图为AU04B)

![](img/06859067f3402277041d47b4a4e48a1b_186_0.png)

(b) 电路板图 (左图为AU17、右图为AU04B)

图2.23 AU17、AU04B的电路原理图、电路板图和实物图

![](img/06859067f3402277041d47b4a4e48a1b_188_0.png)

共阴极数码管

![](img/06859067f3402277041d47b4a4e48a1b_188_1.png)

排针

贴片电阻（390Ω）

![](img/06859067f3402277041d47b4a4e48a1b_188_2.png)

棕黑黑红棕（10kΩ）

光敏电阻

5V GND AO

![](img/06859067f3402277041d47b4a4e48a1b_188_3.png)

排针

光敏电阻

![](img/06859067f3402277041d47b4a4e48a1b_188_4.png)

光线正常时，数码管显示数字3

![](img/06859067f3402277041d47b4a4e48a1b_188_5.png)

光线较亮时，数码管显示数字7

(c) 实物图（上图为AU17、中图为AU04B、下图为二者结合）

图2.23 AU17、AU04B的电路原理图、电路板图和实物图（续）

## 2.18.2 知识要点

根据光敏模块在2.4节中的检测结果可知，当光线很亮时，光敏模块输出引脚的电压为4.5～4.8V，对应的模拟输入值为900～980；当光线很暗时，光敏模块输出引脚的电压为0～0.48V，对应的模拟输入值为0～98。

## 2.18.3 编程要点

光线亮度等级的编程方法如下。

由于采用一位数码管显示光线亮度等级，因此可将光线亮度等级划分为0～9共10个等级，数字0表示光线亮度等级最低（最暗），数字9表示光线亮度等级最高（最亮）。

```
语句int i； 表示定义变量i为整型数据。
```

```
语句val=analogRead（0）； //表示读出模拟端口A0的值，赋给变量val，val的值为0～1023。
```

```
语句i=val/100； 表示变量val除以100后的值赋给i，i为整型数据。
```

当val=1023时，i=10，这种情况在实际检测中未发生。

当val=999时，i=9，即当光线很亮时，光敏模块输出脚的电压为4.5～4.8V，对应的val值为900～980，数码管将显示数字9。

当val=99时，i=0，即当光线很暗时，光敏模块输出脚的电压为0～0.48V，对应的val值为0～98，数码管将显示数字0。

## 2.18.4 程序设计

（1）程序参考

```
#define Da 9// 定义变量 Da=9( 数码管引脚 a 接数字端口 9)。
#define Db 8// 定义变量 Db=8( 数码管引脚 b 接数字端口 8)。
#define Dc 7// 定义变量 Dc=7( 数码管引脚 c 接数字端口 7)。
#define Dd 12// 定义变量 Dd=12( 数码管引脚 d 接数字端口 12)。
#define De 13// 定义变量 De=13( 数码管引脚 e 接数字端口 13)。
#define Df 10// 定义变量 Df=10( 数码管引脚 f 接数字端口 10)。
#define Dg 11// 定义变量 Dg=11( 数码管引脚 g 接数字端口 11)。
int val;// 定义整型变量 val。
int i;// 定义整型变量 i。
unsigned char num[10][7] = {
//1 为点亮， 0 为熄灭， a 表示数码管引脚 a, 其他类推。
//abcdefg
{1,1,1,1,1,1,0,}//0。
{0,1,1,0,0,0,0,}//1。
{1,1,0,1,1,0,1,}//2。
{1,1,1,1,0,0,1,}//3。
{0,1,1,0,0,1,1,}//4。
{1,0,1,1,0,1,1,}//5。
{1,0,1,1,1,1,1,}//6。
{1,1,1,0,0,0,0,}//7。
{1,1,1,1,1,1,1,}//8。
{1,1,1,1,0,1,1,}//9。
};
```
