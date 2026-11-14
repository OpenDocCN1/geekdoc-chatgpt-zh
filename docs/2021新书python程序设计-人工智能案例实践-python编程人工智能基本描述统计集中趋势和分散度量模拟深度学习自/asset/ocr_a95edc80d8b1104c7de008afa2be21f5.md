使用time模块的sleep函数来等待指定的时间，然后再生成另一条消息。

```
print('Temperature simulator starting')
for message in range(NUMBER_OF_MESSAGES):
    # generate a random number in the range -MAX_TEMP_CHANGE
    # through MAX_TEMP_CHANGE and add it to the current temperature
    thermostat['Temperature'] += random.randrange(
        -MAX_TEMP_CHANGE, MAX_TEMP_CHANGE + 1)
    # ensure that the temperature stays within range
    if thermostat['Temperature'] < MIN_CELSIUS_TEMP:
        thermostat['Temperature'] = MIN_CELSIUS_TEMP
    if thermostat['Temperature'] > MAX_CELSIUS_TEMP:
        thermostat['Temperature'] = MAX_CELSIUS_TEMP
    # check for low temperature warning
    if thermostat['Temperature'] < 3:
        thermostat['LowTempWarning'] = True
    else:
        thermostat['LowTempWarning'] = False
    # check for high temperature warning
    if thermostat['Temperature'] > 35:
        thermostat['HighTempWarning'] = True
    else:
        thermostat['HighTempWarning'] = False
    # send the dweet to dweet.io via dweepy
    print(f'Messages sent: {message + 1}\r', end='')
    dweepy.dweet_for(dweeter, thermostat)
    time.sleep(MESSAGE_DELAY)
print('Temperature simulator finished')
```

使用此服务不需注册，首次调用dweepy的dweet_for函数发送dweet（第52行）时，dweet.io创建设备名称。该函数接收设备名称（dweeter）和代表要发送的消息的字典（恒温器）作为参数。一旦执行脚本后，可以通过在Web浏览器中转到网址https://dweet.io/follow/temperature-simulator-deitel-python，立即开始在dweet.io网站上跟踪消息。

如果使用其他设备名称，请将“temperature-simulator-deitel-python”替换为设备名称。该网页包含两个选项卡，“Visual”选项卡中显示各个数据项，并显示任何数值的迷你图；“Raw”选项卡中显示了Dweepy发送到dweet.io的实际JSON消息。

# 16.8.4 使用freeboard.io创建仪表板

站点dweet.io和freeboard.io由同一家公司运行。在上面讨论的dweet.io网页中，可以单击“Create a Custom Dashboard”按钮打开一个新的浏览器选项卡，其中已经为温度传感器实现了默认仪表板。默认情况下，freeboard.io将配置一个名为Dweet的数据源，并自动为