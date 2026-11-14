法用于查找图像中的矩形，返回一个 image. rect 对象的列表。该方法适用于检测与背景形成鲜明对比的矩形。

其中，roi 是一个用以搜索的矩形区域(x, y, w, h)，图像操作范围仅限于 roi 区域内的像素。如果未指定，默认的 roi 即整幅图像。

Threshold 参数的取值要根据应用场景的情况进行调节。在矩形边缘处所有像素上滑动索贝尔算子并进行累加，边界值小于 threshold 的矩形会进入返回列表。

利用find_rects方法检测矩形物体的例程如下：

```
#找矩形
import sensor,image,time
sensor.reset()
sensor.set_pixformat(sensor.RGB565)
sensor.set_framesize(sensor.QQVGA)
sensor.skip_frames(time = 2000)
clock = time.clock()
```

```
while(True):
clock.tick()
img = sensor.snapshot()
# find_rects 中'threshold'应设置为足够高的值，以滤除在图像中检测到的具有低边缘幅度的噪声矩形，最适用于与背景形成鲜明对比的矩形。
for r in img.find_rects(threshold = 25000) : #查找矩形，阈值 = 10000
img.draw_rectangle(r.rect(), color = (255,0,0)) #画出矩形，颜色为红色
for p in r.corners()： #找出矩形的 4 个角
img.draw_circle([p[0],p[1],5],color = (0,255,0))# 4 个角顺时针画出绿色的半径为 5 像素的圆形
print(r)
print(" FPS %f " % clock.fps())
```

程序调试通过后，运行情况如图 4.3 所示。