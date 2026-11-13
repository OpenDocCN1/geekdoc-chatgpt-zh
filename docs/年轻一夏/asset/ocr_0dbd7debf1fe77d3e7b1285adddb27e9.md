```
item_num: int=8):

"""模拟物件的到达"""
for i in range(item_num):
    print(f{round(env.now, 2)} - item: item_{i} - created')
    last_q.put(fitem_{i}')
    t = random.expovariate(1 / MEAN_TIME)
    yield env.timeout(round(t, 1))

if __name__ == '__main__':

    # 实例环境
    env = simpy.Environment()
    # 设备前的物件队列
    last_q = simpy.Store(env)
    next_q = simpy.Store(env)

    env.process(generate_item(env, last_q))
    for i in range(MACHINE_NUM):
        env.process(machine(env, last_q, next_q, machine_id=fm_{i}'))
    env.run()
```

机器数为 2 时的工序结果如图 4-12 所示。

![](bbox=[0.363, 0.49, 0.68, 0.792])

图 4-12 机器数为 2 时的工序结果

图 4-12 所示的程序结果是两台机器处理 8 个物件的工序模拟。读者也可以自行调节机器数量和物件数量，如将机器数设置为 3，即相关代码修改如下。

```
MACHINE_NUM = 3
```

机器数为 3 时的工序结果如图 4-13 所示。