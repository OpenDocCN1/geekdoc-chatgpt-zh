```
```cpp
right();// 右转。
}
if(distance_cm_left>30 && distance_cm_right<30){
left();// 左转。
}
if(distance_cm_left<30 && distance_cm_right>30){
right();// 右转。
}
}
void forward()// 前进。
{
digitalWrite(pinleft1,0);
digitalWrite(pinleft2,1);// 左侧电机（ MB ）前进。
digitalWrite(pinright1,0);
digitalWrite(pinright2,1);// 右侧电机（ MA ）前进。
delay(200);// 延时 200ms。
digitalWrite(pinleft2,0);
digitalWrite(pinright2,0);
}
void back()// 后退。
{
digitalWrite(pinleft1,1);
digitalWrite(pinleft2,0);// 左侧电机（ MB ）后退。
digitalWrite(pinright1,1);
digitalWrite(pinright2,0);// 右侧电机（ MA ）后退。
delay(100);// 延时 100ms。
digitalWrite(pinleft2,1);
digitalWrite(pinright2,1);
}
void left()// 左转。
{
digitalWrite(pinleft1,1);
digitalWrite(pinleft2,0);// 左侧电机（ MB ）后退。
digitalWrite(pinright1,0);
digitalWrite(pinright2,1);// 右侧电机（ MA ）前进。
delay(100);// 延时 100ms。
digitalWrite(pinleft1,0);
```
```