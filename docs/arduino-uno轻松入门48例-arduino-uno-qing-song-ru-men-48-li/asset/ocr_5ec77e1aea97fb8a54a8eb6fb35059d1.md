```
irsend.sendNEC(IRcode,numBits);// 以 NEC 协议格式发射一组红外编码。IRcode 表示十六进制的红外编码，numBits 表示编码的位数。
```

# 2.44.4 程序设计

# 1.代码—

# (1) 程序参考

```
#include "IRremote.h"// 定义头文件 IRremote.h。
IRsend irsend;// 新建一个 IRsend 对象。
void setup(){
```

```
pinMode(4,OUTPUT);// 设置数字端口 4 为输出模式。
digitalWrite(4,0);// 设置数字端口 4 输出低电平。
}

void loop(){
irsend.sendNEC(0xFF30CF,32);// 发射 0xFF30CF 信号。
delay(500);// 延时 500ms。
}
```

# (2) 实验结果