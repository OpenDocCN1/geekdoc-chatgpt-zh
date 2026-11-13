```
bool val=0;// 定义布尔型变量 val，初始化赋值为 0。
void setup(){
pinMode(13,OUTPUT);// 设置数字端口 13 为输出模式。
pinMode(12,OUTPUT);// 设置数字端口 12 为输出模式。
pinMode(12,INPUT);// 设置数字端口 12 为输入模式。
digitalWrite(12,1);// 设置数字端口 12 输出高电平。
}
void loop(){
if(val==1)// 如果 val==1，
digitalWrite(13,1);// 数字端口 13 输出高电平。
}//else// 否则，
digitalWrite(13,0);// 数字端口 13 输出低电平。
}
if(digitalRead(12)==0)// 如果数字端口 12 的值为 0，
delay(100);// 延时 100ms。
if(digitalRead(12)==0)// 如果数字端口 12 的值为 0，
val=!val;// 将 val 值取反，如果 val=0，!val=1。
while(digitalRead(12)==0);// 当数字端口 12 的值为 0 时执行循环语句。
}
}
```