```
img.draw_circle(c.x(), c.y(), c.r(), color = (0, 0,
255))
else:
#将非红绿蓝色的圆用白色的矩形框出来
img.draw_rectangle(area, color = (255,255,255))
```

# 4 ) Python 编程及实现

集成以上各阶段的编程过程，编写出完整的目标追踪 Python 程序，写入 AIE 控制板中，观察目标追踪效果。同时实现红绿蓝三种颜色追踪的完整程序参考如下：

```
import sensor, image, time

sensor.reset()
sensor.set_pixformat(sensor.RGB565)
sensor.set_framesize(sensor.QQVGA)
sensor.skip_frames(time = 2000)
sensor.set_auto_gain(False)     # 颜色及形状目标追踪时必须
                                 # 关闭自动增益
sensor.set_auto_whitebal(False)  # 颜色及形状目标追踪时必须
                                 # 关闭白平衡

clock = time.clock()

while(True):
    clock.tick()
    img = sensor.snapshot().lens_corr(1.3)    #进行镜头畸变校正，去
                                              #除图像中的鱼眼效应；
    for c in img.find_circles(threshold = 2500, x_margin = 10, y_margin =
            10, r_margin = 10,
            r_min = 2, r_max = 100, r_step = 2):    #找出所有圆形；
        area = (c.x() - c.r(), c.y() - c.r(), 2 * c.r(), 2 * c.r())
        #area 为识别到的圆的区域，即圆的外接矩形框；
        statistics = img.get_statistics(roi = area)   #像素颜色统计
```