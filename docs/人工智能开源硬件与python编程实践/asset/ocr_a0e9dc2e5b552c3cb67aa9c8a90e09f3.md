```
a = 0#标志位,0 代表没人
for r in objects:
    img. draw_rectangle(r)
    a = 1 #标志位置 1,代表有人
if a == 1 :
    a -=1 #标志位清空 a = a - 1(a = -1) 或者 a = 0
    led. green. on()
    img.draw_string(10, 10, "Face Detected", color = (0, 0, 0), scale =
= 3, mono_space = False)
    asr_ takephoto ( asr. get_res() )
else:
    led. green. off()
    led. red. off()
    led. blue. off()
    img. draw_string(10, 10, "No Person", color = (0, 0, 0), scale =
3, mono_space = False)
```

以上例程中，不断进行人脸检测，一旦检测到人脸，先绿灯提示，然后提取语音识别结果。如果发现拍照指令，就补光并保存当前帧作为照片。如果当前图像帧中没有人脸，就会显示无人，提取语音识别的结果。

# (5) 调试、验证及完善

完成以上 Python 程序的编写后，上传到 AIE 控制板。运行程序时，如果提示有语法错误，需要逐一进行修改。程序运行过程中，参考以下经验对遇到的问题进行改进：

①视频相关功能的调试。如果视频显示区没有显示出摄像头捕捉的视频帧图像，则要先检查控制板与计算机的连接是否松动。如果确认控制板连接正常，则需要检查 Sensor 对象里摄像头工作参数的程序设置是否准确。

②语音识别相关功能的调试。可以先屏蔽 asr_test() 函数，直接调用 ASR. get_res() 方法，将识别结果输出到串口监视器中，观察输出的值是否与说出的语音关键词的编码一致。通过这样的方法有助于发现一些编程错误，然后可以修改程序，重新进行系统检测、调试及性能优化过程。