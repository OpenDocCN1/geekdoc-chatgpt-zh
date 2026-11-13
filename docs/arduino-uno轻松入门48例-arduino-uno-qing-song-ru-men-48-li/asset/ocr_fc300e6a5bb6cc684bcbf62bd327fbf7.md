```
```cpp
digitalWrite(8,HIGH);//超声波 Trig 引脚输出高电平。
delayMicroseconds(50);//延时 50 μs。
digitalWrite(8,LOW);//超声波 Trig 引脚输出低电平。
EchoTime_us=pulseIn(7,HIGH);//测量超声波从发射到接收所经过的时间。
distance_cm_right=EchoTime_us/58.14;//距离 = 时间 x 声速 /2。
```
```

第二步，编写小车前进程序。

```
```cpp
if(distance_cm_left>30 && distance_cm_right>30)(forward();)
// 如果左侧和右侧检测距离均大于 30cm，那么小车前进。
```
```

第三步，编写小车右转程序。

```
```cpp
if(distance_cm_left<30 && distance_cm_right<30 && distance_cm_left<distance_cm_right){back();right();}
// 如果左侧和右侧检测距离均小于 30cm，而且左侧检测距离小于右侧检测距离，让小车首先后退然后右转。
```
```

第四步，编写小车左转程序。