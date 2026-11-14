```
led.green.on()
led.blue.off()
elif sel == 3 : #关键词“蓝”
    led.red.off()
    led.green.off()
    led.blue.on()
elif sel == 4 : #关键词“开灯”
    led.red.on()
    led.green.on()
    led.blue.on()
elif sel == 5 : #关键词“关灯”
    led.red.off()
    led.green.off()
    led.blue.off()
```

```
while True:
    asr_test(asr.get_res()) #提取语音识别的结果
    time.sleep(100)
```

# (5) 调试、验证及完善

完成以上 Python 程序的编写后，上传到 AIE 控制板。运行程序时，如果提示有语法错误，需要逐一进行修改。程序运行过程中，参考以下经验对遇到的问题进行改进：

①如果发现控制板上 LED 灯没有被正确点亮或熄灭，则先检查语音识别模块有误正确输出识别结果。可以先屏蔽 asr_test() 函数，直接调用 asr.get_res() 方法，将识别结果输出到串口监视器中，观察输出的值是否与说出的语音关键词的编码一致。通过这样的方法有助于发现一些编程错误，然后可以修改程序，重新进行系统检测，调试及性能优化过程。

②调试语音识别模块过程中，也要注意工作环境中是否存在比较大的噪声。噪声的存在会影响识别结果的准确性，严重时会导致没有识别结果出现。调试过程在安静的环境中进行，以排除干扰。