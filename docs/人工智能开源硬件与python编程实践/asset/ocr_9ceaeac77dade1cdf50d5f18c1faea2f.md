```
sensor.skip_frames(time = 2000)
clock = time.clock()
```

```
#加载人脸检测模型
face_cascade = image.HaarCascade("frontalface", stages = 25 )
print(face_cascade)
```

```
#语音工作场景设置
asr.add_cmd("qie zi" , 1)     #添加关键词“茄子”，作为拍照操作命
                            令间;
asr.add_cmd(" pai zhao" , 1)  #添加关键词“拍照”，作为拍照操作命
                            令间;
asr.run()                   #启动语音识别过程
```

```
def asr_takephoto(sel):     #自定义一个拍照处理函数
    if sel == 1 :
        #保存截取到的图片
        led.red.on()          #进行补光
        led.green.on()
        led.blue.on()
        print("照片已保存")
        time.sleep(200)
        sensor.snapshot().save("example-%d.jpg" % pyb.rng())  #
```

```
保存图像帧
#pyb.rng() 返回一个硬件产生的 30 位随机数值
led.red.off()
led.green.off()
led.blue.off()
```

```
#主循环
while True:
    clock.tick()
    img = sensor.snapshot()
    objects = img.find_features(face_cascade, threshold = 0.5 , scale = 1.35 )
```