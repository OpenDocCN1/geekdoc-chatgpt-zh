```
delay(1000);// 延时 1000ms。
}
void left()// 左转。
    digitalWrite(pinleft1,1);
    digitalWrite(pinleft2,0);// 左侧电机（ MB ）后退。
    digitalWrite(pinright1,0);
    digitalWrite(pinright2,1);// 右侧电机（ MA ）前进。
    delay(1000);// 延时 1000ms。
    digitalWrite(pinleft1,0);
    digitalWrite(pinright2,0);
    delay(1000);// 延时 1000ms。
}
void right()// 右转。
    digitalWrite(pinleft1,0);
    digitalWrite(pinleft2,1);// 左侧电机（ MB ）前进。
    digitalWrite(pinright1,1);
    digitalWrite(pinright2,0);// 右侧电机（ MA ）后退。
    delay(1000);// 延时 1000ms。
    digitalWrite(pinleft2,0);
    digitalWrite(pinright1,0);
    delay(1000);// 延时 1000ms。
}
```