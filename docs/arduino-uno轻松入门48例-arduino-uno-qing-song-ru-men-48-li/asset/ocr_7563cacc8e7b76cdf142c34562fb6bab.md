```
};
void setup(){
for(int i=0;i<14;i++)// 循环执行， 从 i=0 开始， 到 i=13 结束。
pinMode(i,OUTPUT);// 设置数字端口 0 ~ 13 为输出模式。
}
pinMode(0,INPUT);// 设置数字端口 0 为输入模式。
Timer1.initialize(1000000);// 设置定时器中断时间为 1000000 μs 即 1s。
Timer1.attachInterrupt(callback);// 设置定时器中断服务函数， 每发生一次定时器中断， 都
会执行一次该函数。
}
void callback()// 中断函数每秒执行一次。
secs=(secs+1)%60;// 秒钟数加 1。
if(secs==0)// 如果秒数和毫秒数到 00，
mins=(mins+1)%60;// 分钟数加 1。
if(mins==0)// 如果分钟数、 秒钟数和毫秒数到 00，
hours=(hours+1)%24;// 小时数加 1。
}
}
}
void loop(){
if(flag==0)// 如果运行模式为 0， 正常计时。
mod=50;// 显示小时数和分钟数。
scan();// 逐位显示程序。
s1=secs%10;//s1 = 秒数的个位。
s2=secs/10;//s2 = 秒数的十位。
m1=mins%10;//m1 = 分钟数的个位。
m2=mins/10;//m2 = 分钟数的十位。
h1=hours%10;//h1 = 小时数的个位。
h2=hours/10;//h2 = 小时数的十位。
}
if(flag==1)// 如果运行模式为 1， 调整分钟数。
mins=(mins+1)%60;// 分钟数加 1。
m1=mins%10;//m1 = 分钟数的个位。
m2=mins/10;//m2 = 分钟数的十位。
```