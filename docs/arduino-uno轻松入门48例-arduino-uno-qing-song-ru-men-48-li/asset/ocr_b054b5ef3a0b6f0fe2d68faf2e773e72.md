```
mod=30;// 显示分钟数每 0.5s 自动加 1。
scan(); // 逐位显示程序。
}
if(flag==2)// 如果运行模式为 2，调整小时数。

hours=(hours+1)%24;// 小时数加 1。
h1=hours%10;//h1= 小时数的个位。
h2=hours/10;//h2= 小时数的十位。
mod=30;// 显示小时数每 0.5s 自动加 1。
scan(); // 逐位显示程序。
}
}
void disp(unsigned char value)// 显示子程序 disp。
for(int i=0;i<7;i++)
digitalWrite(ledpin[i],bitRead(value,i));
// 将变量 value 的第 i 位数据传输给数组 ledpin 对应的第 i 个数字端口。
// 共阳极数码管使用 !bitRead(value,i)。
}
void disp0()// 显示子程序 disp0，清除数码管余辉效应。
for(int i=0;i<7;i++)
digitalWrite(ledpin[i],0);
}
void scan(){
for(int j=0;j<mod;++j+// 循环执行 49 次。
digitalWrite(0,1);// 数字端口 0 输出高电平。
if(digitalRead(0)==0)// 如果数字端口 0 为低电平，
delay(250);// 延时 250ms，避免切换速度过快。
flag=(flag+1)%3;// 运行模式加 1。
break; // 退出循环。
}
contro(0,0,0,0,0,1);// 设置数字端口 6 为高电平，显示秒数的个位。
disp(num[s1]);// 显示秒数的个位。
delay(3);// 延时 3ms。
disp0();
contro(0,0,0,0,1,0);// 设置数字端口 5 为高电平，显示秒数的十位。
```