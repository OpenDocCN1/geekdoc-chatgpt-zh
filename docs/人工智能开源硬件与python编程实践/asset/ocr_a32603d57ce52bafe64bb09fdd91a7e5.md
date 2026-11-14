大范围。

Invert 是反转阈值操作,像素在已知颜色范围之外进行匹配,而非在已知颜色范围内。

roi 是感兴趣区域的矩形元组(x, y, w, h),即操作范围仅限于 roi 区域内的像素。如果未指定,roi 即整个图像的图像矩形。

bins 和其他 bin 是用于直方图通道的箱数。对于灰度图像,使用 bins,对于 RGB565 图像,使用其他每个通道。每个通道的 bin 计数必须大于 2。默认情况下,直方图将具有每个通道的最大 bin 数。

利用 get_statistics 统计的方法,可以计算出指定区域内占面积最大的颜色。对给定区域进行颜色追踪的程序代码如下:

```
import sensor, image, time
```

```
#area 为识别到的圆的区域,即圆的外接矩形框
statistics = img. get_statistics(roi = area)#像素颜色统计
print( statistics)
```

```
#(35, 72, 98, 20, 20, 72) 为红色的阈值。检测时,当区域内的众数(也就是最多的颜色),范围在这个阈值内,就说明显红色的圆。
```

```
#(53,77, -56, -3, -7,63) 为绿色阈值
```

```
#(30,82, -29,39, -85, -3) 为蓝色阈值
```

```
#l_mode(),a_mode(),b_mode() 分别是 L 通道、A 通道、B 通道的众数。
```

```
if 35 < statistics.l_mode() < 72 and 9 < statistics.a_mode() < 80 and 20 < statistics.b_mode() < 72:
```

```
#识别到的红色圆形用红色的圆框出来
img. draw_circle( c. x( ),c. y( ),c. r( ),color = (255,0, 0 ))
```

```
elif 53 < statistics.l_mode() < 77 and -56 < statistics.a_mode() <-3 and -7 < statistics.b_mode() < 63 :
```

```
#识别到的绿色圆形用绿色的圆框出来
img. draw_circle( c. x( ),c. y( ),c. r( ),color = (0,255, 0 ))
```

```
elif 30 < statistics.l_mode() < 82 and -29 < statistics.a_mode() < 39 and -96 < statistics.b_mode() < -3:
```

```
#识别到的蓝色圆形用蓝色的圆框出来
```