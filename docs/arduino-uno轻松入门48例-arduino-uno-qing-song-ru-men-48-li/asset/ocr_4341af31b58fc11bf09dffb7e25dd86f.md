```
void setup(){
pinMode(10,OUTPUT); // 设置数字端口 10 ~ 13 为输出模式。
pinMode(11,OUTPUT);
pinMode(12,OUTPUT);
pinMode(13,OUTPUT);
}

void loop(){
for(int i=0;i<512;i++)// 顺时针慢速转动 360°
phases(0,0,1,1);// 设置 C、D 相位为高电平。

delay(10);// 延时 10ms。

phases(0,1,1,0);// 设置 B、C 相位为高电平。

delay(10);// 延时 10ms。

phases(1,1,0,0);// 设置 A、B 相位为高电平。

delay(10);// 延时 10ms。
}
```