度表征的图形中的高频噪声。然后将模糊化后的图形进行二值化，梯度图中任何小于等于 255 的像素设为 0(黑色)，其余设为 255(白色)。

⑤对二值化后的图像应用数学形态学中的闭运算，消除条形码竖条之间的缝隙。

⑥连续进行若干次数学形态学中的腐蚀及膨胀运算。腐蚀操作将会腐蚀图像中白色像素，以此来消除小斑点，而膨胀操作将使剩余的白色像素扩张并重新增长回去。

⑦最后找到图像中的最大轮廓，这就是检测到的条形码区域。

# 3）摄像头工作参数初始化

OpenAI 人工智能开源控制板上固化进了 Python 计算机视觉库，其中的算法模块可以在编程中直接调用。

```
sensor.reset()
sensor.set_pixformat(sensor.GRAYSCALE)
sensor.set_framesize(sensor.VGA) # High Res!
sensor.set_windowing((640, 80)) # V Res of 80 = = less work (40 for 2X
the speed).
sensor.skip_frames(30)
sensor.set_auto_gain(False ) # must turn this off to prevent image wash-
out. ..
sensor. set_auto_whitebal ( False ) # must turn this off to prevent image
washout. ..
```

将摄像头设置成 640 x 480 分辨率，OV7725 工作模式，条码检测工作在 RGB565 模式，高分辨率和灰度模式。

# 4）快更条码的码制

在 Python 中，定义一个函数要使用 def 语句，依次写出函数名、括号、括号中的参数和冒号，然后在缩进块中编写函数体，函数的返回值用 return 语句返回。

由于 Python 语言没有 C 语言中常用的 Switch 语句。只能使用 if 条件语句来设计码制选择函数，供主程序调用。

```
def barcode_name(code):
    if(code.type() == image.EAN2):
        return "EAN2"
```