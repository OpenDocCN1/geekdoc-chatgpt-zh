```
# 添加行为到列表
function addBehavior(func: function, frequency: int, phase: int):
    # 编译并添加记录
    record = new Record()
    record.functionToCall = func
    record.frequency = frequency
    record.phase = phase
    behaviors += record

# 每帧调用一次
function run(timeToRun: int):
    frame += 1
    runThese: BehaviorRecord[] = []

    # 遍历每一个行为
    for behavior in behaviors:
        # 如果已经到期，则调度它
        if behavior.frequency % (frame + behavior.phase):
            runThese.append(behavior)

    # 保存当前时间记录
    currentTime: int = time()

    # 遍历要运行的行为
    numToRun: int = runThese.length()
    for i in 0..numToRun:
        # 找到上一次迭代所用的时间
        lastTime = currentTime
        currentTime = time()
        timeUsed = currentTime - lastTime

        # 将剩余的时间分布给余下的行为
        timeToRun -= timeUsed
        availableTime = timeToRun / (numToRun - i)

        # 运行行为
        runThese[i].thingToRun(availableTime)
```

# 2. 数据结构

我们将要注册的函数现在应该采用 time 值，表示它们应该运行的最长时间。

我们已经假设要运行的函数列表有一个获取元素数量的 length 方法。