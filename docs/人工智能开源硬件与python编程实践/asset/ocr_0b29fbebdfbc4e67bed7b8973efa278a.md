```
led. red. off()      #先关闭红灯；
while(True) :
    led. red. on()     #红灯亮；
```

在图 1.5 所示的 IDE 工具中输入以上简单的 4 行程序,写入 AIE 控制板中,就会发现 LED 红灯亮了。可以进一步扩展程序,让 LED 灯亮成蓝色或绿色;也可以利用延时,让 LED 灯闪烁起来。参考例程如下:

```
from openai import led      #从 AIE 控制板固化的 openai 库中导入 led 模块；
import time
while(True) :
    led. green. on()      #开启绿灯
    time.sleep(150)      #延时 150 ms
    led. green. off()      #关闭绿灯
    time.sleep(150)      #延时 150 ms
```

# 4) 进一步实用化编程

经过以上上阶段的 Python 程序设计,可以利用 AIE 控制板上 LED 灯,编写一个有实际用处的 LED 灯光控制程序。控制板上固化有 pyb、time、led 等编程库,提供了 LED、USB 等对象管理,用来控制板上 LED 灯的工作。LED 对象有 on 和 off 两种方法,分别控制 LED 的点亮和熄灭,可以根据 USB 设备的连接状况设置 LED 发光颜色的改变。完整的示例代码如下:

```
from openai import led   #从 AIE 控制板固化的 openai 库中导入 led 模块；
import time,pyb
#将蓝色赋值给变量 led
led = pyb.LED(3)      # Red LED = 1 ,Green LED = 2 ,Blue LED = 3
usb = pyb. USB_VCP( )    #USB 转串口对象
如:AIE 控制板未连接到计算机,红灯亮 150 ms,延时 100 ms,亮 150 ms,延时 600 ms,循环。
while( not usb. isconnected( ) ) :    #检查 OpenAIE 与计算机的连接情况；
    led. red. on()          #亮红灯
    time.sleep(150)         #延时 150 ms
    led. red. off()         #熄灭红灯
    time.sleep(100)         #延时 100 ms
    led. red. on()
```