```
void setup(){
servo13.attach(13);// 设置舵机接口为数字端口 13。
pinMode(7,INPUT);// 超声波 Echo 引脚连接端口 7 为输入模式。
pinMode(8,OUTPUT);// 超声波 Trig 引脚连接端口 8 为输出模式。
pinMode(pinleft1,OUTPUT);
pinMode(pinleft2,OUTPUT);
pinMode(pinright1,OUTPUT);
pinMode(pinright2,OUTPUT);
pinMode(Rledleft,OUTPUT);
pinMode(Gledright,OUTPUT);
}

void loop(){
servo13.write(60);// 设置舵机旋转的角度为 60° 。
delay(200);// 延时 200ms。
digitalWrite(8,HIGH);// 超声波 Trig 引脚输出高电平。
delayMicroseconds(50);// 延时 50 μs。
digitalWrite(8,LOW);// 超声波 Trig 引脚输出低电平。
EchoTime_us=pulseIn(7,HIGH);// 测量超声波从发射到接收所经过的时间。
distance_cm_right=EchoTime_us/58.14;// 距离 = 时间 x 声速 /2。
delay(50);// 延时 50ms。
if(distance_cm_right<30){
digitalWrite(12,HIGH);// 右侧绿色 LED 灯连接端口 12 输出高电平。
}

if(distance_cm_right>30){
digitalWrite(12,LOW);// 右侧绿色 LED 灯连接端口 12 输出低电平。
}

servo13.write(120);// 设置舵机旋转的角度为 120° 。
delay(200);// 延时 200ms。
digitalWrite(8,HIGH);// 超声波 Trig 引脚输出高电平。
delayMicroseconds(50);// 延时 50 μs。
digitalWrite(8,LOW);// 超声波 Trig 引脚输出低电平。
EchoTime_us=pulseIn(7,HIGH);// 测量超声波从发射到接收所经过的时间。
distance_cm_left=EchoTime_us/58.14;// 距离 = 时间 x 声速 /2。
delay(50);// 延时 50ms。
if(distance_cm_left<30){
digitalWrite(11,HIGH);// 左侧红色 LED 灯连接端口 11 输出高电平。
```