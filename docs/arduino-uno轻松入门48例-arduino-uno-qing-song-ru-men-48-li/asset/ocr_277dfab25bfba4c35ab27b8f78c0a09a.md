```
lcd.print("Hello!Friend!");//输出字符串。
lcd.setCursor(0,1);// 设置光标位置为 (0,1)，即第 0 列第 1 行。
lcd.print("How are you?");//输出字符串。
delay(3000);// 延时 3000ms。
lcd.clear();// 清除屏幕。
lcd.setCursor(0,0);// 设置光标位置为 (0,0)，即第 0 列第 0 行。
lcd.print("Fine,Wonderful!");//输出字符串。
lcd.setCursor(0,1);// 设置光标位置为 (0,1)，即第 0 列第 1 行。
lcd.print("Pretty good!");//输出字符串。
delay(3000);// 延时 3000ms。
lcd.clear();// 清除屏幕。
}
```

# (2) 实验结果

接通电源，液晶显示屏上显示“Hello，Friend！” “How are you? ”，延时3s后，显示“Fine, Wonderful！” “Pretty good！”。如此循环。

# 2.代码二