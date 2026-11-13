```
servo4.attach(4);// 定义舵机 4 的接口为数字端口 4。
}
void loop()// 将 vx、vy、sw 端口输入的模拟值 0 ~ 1023 转换为 0 ~ 255。
outvx=map(analogRead(vx),0,1023,0,180);
outvy=map(analogRead(vy),1023,0,0,180);
outsw=map(analogRead(sw),0,1023,0,255);
servo3.write(outvx);// 设置舵机 3 旋转的角度为 outvx。
servo4.write(outvy);// 设置舵机 4 旋转的角度为 outvy。
if(val==1)// 如果 val 的值为 1，
digitalWrite(10,1);// 数字端口 10 输出高电平。
}else// 否则，
digitalWrite(10,0);// 数字端口 10 输出低电平。
}
if(outsw==0)// 如果 outsw 的值为 0，
delay(100);// 延时 100ms。
if(outsw==0)// 如果 outsw 的值为 0，
val=!val;// 将 val 值取反，val=0，!val=1。
}
}
delay(100);// 延时 100ms。
}
```