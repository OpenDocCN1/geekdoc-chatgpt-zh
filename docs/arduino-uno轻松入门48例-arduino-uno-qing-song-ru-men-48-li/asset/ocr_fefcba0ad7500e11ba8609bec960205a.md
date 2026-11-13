```
void setup()(
    pinMode(5,OUTPUT);// 设置数字端口 5 为输出模式。
    pinMode(6,OUTPUT);// 设置数字端口 6 为输出模式。
    pinMode(8,OUTPUT);// 设置数字端口 8 为输出模式。
    pinMode(9,OUTPUT);// 设置数字端口 9 为输出模式。
    digitalWrite(5,HIGH);// 设置数字端口 5 输出高电平。
    digitalWrite(6,HIGH);// 设置数字端口 6 输出高电平。
    digitalWrite(8,HIGH);// 设置数字端口 8 输出高电平。
    digitalWrite(9,HIGH);// 设置数字端口 9 输出高电平。
}

void loop()(
    for(int i=255;i>-1;i-=5)(
        analogWrite(9,i);// 数字端口 9 输出的电压值为变量值。
        delay(100);// 延时 100ms。
    }
    for(int i=0;i<256;i+=5)(
        analogWrite(9,i);// 数字端口 9 输出的电压值为变量值。
        delay(100);// 延时 100ms。
    }
    for(int i=255;i>-1;i-=5)(
        analogWrite(6,i);// 数字端口 6 输出的电压值为变量值。
```