```
int hours=0;// 定义整型变量 hours ( 小时数 )。
int h1=0;// 定义整型变量 h1( 小时数的个位 )。
int h2=0;// 定义整型变量 h2( 小时数的十位 )。
int flag=0;// 定义整型变量 flag ( 模式数 )，初始化赋值为 0。
int mod;// 定义整型变量 mod ( 扫描显示屏次数 )。
unsigned long runtime;// 定义长整型变量 runtime，记录机器运行时间。
char ledpin[]={2,4,8,7,6,3,9};// 设置数码管引脚对应的数字端口。
unsigned char num[10]=// 设置数字 0 ~ 9 对应的段码值。
0x3f,0x06,0x5b,0x4f,0x66,0x6d,0x7d,0x07,0x7f,0x6f
};
```

```
void setup(){
for(int i=0;i<14;i++)// 循环执行，从 i=0 开始，到 i=13 结束。
pinMode(i,OUTPUT);// 设置数字端口 0 ~ 13 为输出模式。
}

pinMode(13,INPUT);// 设置数字端口 13 为输入模式。
digitalWrite(5,1);// 点亮分隔符。
Timer1.initialize(1000000);// 设置定时器中断时间为 1000000 μs 即 1s。
Timer1.attachInterrupt(callback);

// 设置定时器中断服务函数，每发生一次定时器中断，都会执行一次该函数。
}
```

```
void callback()// 中断函数每秒执行一次。
secs=(secs+1)%60;// 秒数加 1。
if(secs==00)// 如果秒数到 00，
mins=(mins+1)%60;// 分钟数加 1。
if(mins==00)// 如果分钟数到 00，
hours=(hours+1)%24;// 小时数加 1。
}
}
```

```
void loop(){
if(flag==0)// 如果运行模式为 0，正常计时。
mod=50;// 显示小时数和分钟数。
scan();// 逐位显示程序。
m1=mins%10;//m1= 分钟数的个位。
m2=mins/10;//m2= 分钟数的十位。
```