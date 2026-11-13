# 3.代码三

# (1) 程序参考

```
#include <Servo.h>// 定义头文件 Servo.h。
Servo servo4;// 定义舵机变量名 servo4。
int val=0;// 定义整型变量 val，初始化赋值为 0。
void setup(){
servo4.attach(4);// 定义舵机接口为数字端口 4。
}
void loop(){
val=analogRead(0);// 读出模拟端口 A0 的值（0 ~ 1023），赋给变量 val。
servo4.write(val/6);// 设置舵机旋转的角度为 val/6。
delay(100);// 延时 100ms。
}
```

# (2) 实验结果

调节电位器旋钮，舵机4旋转的角度随电位器阻值的变化而变化。

# 2.20.5 拓展和挑战

通过AU20电路板上的光敏模块控制舵机转动的角度。