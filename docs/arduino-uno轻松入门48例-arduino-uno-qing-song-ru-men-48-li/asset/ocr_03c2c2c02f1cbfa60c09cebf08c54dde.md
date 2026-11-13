```
#include <LiquidCrystal.h>// 定义头文件 LiquidCrystal.h。
LiquidCrystal lcd(3,4,5,8,9,10,11);
// 创建 LiquidCrystal(rs,rw,enable,d4,d5,d6,d7) 类实例 lcd。
void setup(){
lcd.begin(16,2);// 设定显示屏尺寸。
lcd.setCursor(0,0);// 设置光标位置为 (0,0)，即第 0 列第 0 行。
lcd.print("Hello,Friend!");//输出字符串 "Hello,Friend!"。
lcd.setCursor(0,1);// 设置光标位置为 (0,1)，即第 0 列第 1 行。
lcd.print("How are you?");//输出字符串 "How are you?"。
}
void loop(){}
```

(2) 实验结果

接通电源，LCD1602A液晶显示模块将显示“Hello， Friend ! ” “How are you? ”。

# 2.22.5 拓展和挑战

让液晶显示屏上显示“Fine, Wonderful! ”“Pretty good! ”。

# 2.23 LCD动态显示文字