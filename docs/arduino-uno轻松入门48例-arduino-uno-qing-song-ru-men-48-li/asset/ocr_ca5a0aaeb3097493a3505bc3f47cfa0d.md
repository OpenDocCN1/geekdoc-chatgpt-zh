```
}
void loop(){
for(int i=0;i<250;i++)// 循环显示 250 次。
    Display(wu);// 调用显示“无”字程序。
}
for(int i=0;i<250;i++)// 循环显示 250 次。
    Display(xian);// 调用显示“线”字程序。
}
for(int i=0;i<250;i++)// 循环显示 250 次。
    Display(dian);// 调用显示“电”字程序。
}
for(int i=0;i<250;i++)// 循环显示 250 次。
    Display(ok);// 调用显示“OK”字程序。
}
}
```

```
void Display(unsigned char dat[8][8])// 显示字程序。
for(int c=0;c<8;c++){
    digitalWrite(COLS[c],LOW);// 设置第 c 列为低电平。
    for(int r=0;r<8;r++)
        digitalWrite(ROWS[r],dat[r][c];
    // 将数据 dat[r][c] 写入行 ROWS[r]。
}
    delay(1);// 延时 1ms。
    Clear();// 调用消除点阵屏余辉效应子程序。
}
}
```

```
void Clear()// 设置行引脚为低电平，列引脚为高电平时，点阵屏内的 LED 灯将不发光。这就
就是消除点阵屏余辉效应。
for(int i=0;i<8;i++){
    digitalWrite(ROWS[i],LOW);
    digitalWrite(COLS[i],HIGH);
}
}
```