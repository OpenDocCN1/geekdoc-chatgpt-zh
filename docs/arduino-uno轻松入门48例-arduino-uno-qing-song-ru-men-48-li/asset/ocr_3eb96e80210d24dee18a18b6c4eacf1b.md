# 2.代码二

# (1) 程序参考

```
void setup(){
pinMode(13,OUTPUT);// 设置数字端口 13 为输出模式。
}

void loop(){
digitalWrite(13,1);// 数字端口 13 输出高电平。
delay(100);// 延时 100ms。
digitalWrite(13,0);// 数字端口 13 输出低电平。
delay(100);// 延时 100ms。
digitalWrite(13,1);// 数字端口 13 输出高电平。
delay(100);// 延时 100ms。
digitalWrite(13,0);// 数字端口 13 输出低电平。
delay(1700);// 延时 1700ms。
}
```