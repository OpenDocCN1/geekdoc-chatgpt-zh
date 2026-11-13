# 5.写在void loop（）之后的语句

在主程序之后一般有以下语句。

```
void contro(int pin9,int pin10,int pin11,int pin12)(
digitalWrite(9,pin9);
digitalWrite(10,pin10);
digitalWrite(11,pin11);
digitalWrite(12,pin12);
// 定义控制位引脚函数。
control(0,1,1,1);// 设置数字端口 9 为低电平，数字端口 10、11、12 为高电平。
void disp0()// 显示子程序 disp0，消除数码管余辉效应。
void scan()// 逐位扫描子程序。
```

# 1.4 常用电子元件

# 1.电阻器

电阻器简称电阻，是电子制作中常用的电子元件。

电阻的作用是可控制电流大小，电压相同时，电阻值越大，通过的电流越小。

电阻的特点是可以导电，对电流有阻碍作用，没有正负极。