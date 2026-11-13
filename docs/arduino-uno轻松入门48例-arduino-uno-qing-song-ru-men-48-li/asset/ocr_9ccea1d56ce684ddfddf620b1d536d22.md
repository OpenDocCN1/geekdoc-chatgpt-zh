```
s1=second%10;//s1= 秒数的个位。
s2=second/10;//s2= 秒数的十位。
m1=minute%10;//m1= 分钟数的个位。
m2=minute/10;//m2= 分钟数的十位。
h1=hour%10;//h1= 小时数的个位。
h2=hour/10;//h2= 小时数的十位。
```

# 2.38.4 程序设计

# 1.代码一

# (1) 程序参考

```
#include <DS3231.h>// 定义头文件 DS3231.h，这是时钟模块库函数。
#include <Wire.h>// 定义头文件 Wire.h，这是 I2C 通信库函数。
DS3231 Clock;
bool Century=false;// 定义布尔型变量。
bool h12;// 定义布尔型变量。
```