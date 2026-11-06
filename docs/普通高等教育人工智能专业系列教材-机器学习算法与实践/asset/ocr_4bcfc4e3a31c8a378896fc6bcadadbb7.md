是否为 False。如果为 False，说明所有样本都分类正确，计算并打印结果，并返回 False；否则返回 True。

# 代码 12-9 定义检验函数，用于判定训练集中是否存在误分类点

```
# 检验函数
def check():
    """
    检查函数，检查给定条件是否满足，并返回检查结果。

    参数：
    无。

    返回值：
    flag: 检查结果，为 True 表示条件不满足，为 False 表示条件满足。
    """
    # 使用全局变量 a, x, y
    global a, x, y

    # 初始化标志位 flag 为 False
    flag = False

    # 遍历训练集中的每个索引 i
    for i in range(len(training_set)):
        # 如果 cal(i) 的返回值小于等于 0，则将标志位 flag 设为 True，并执行 update(i)
        if cal(i) <= 0:
            flag = True
            update(i)

    # 如果标志位 flag 为 False，则计算并打印结果，并返回 False；否则返回 True
    if not flag:
        w = np.dot(a * y, x)
        print("RESULT: w: " + str(w) + " b: " + str(np.dot(a, y)))
        return False
    return True
```

5) 实现分类。

代码 12-10 表示如果该模块作为主程序运行，在主程序中，调用 cal_gram() 函数计算并获取 Gram 矩阵。然后，进入一个包含 1000 次迭代的循环。每次迭代，通过 for i in range (1000)；遍历循环变量 i，i 的取值范围是 0~999。在每次迭代内部，调用 check() 函数进行检查。如果 check() 函数返回 False，即条件不满足，则通过 break 语句结束循环。

# 代码 12-10 主程序实现分类

```
# 主程序
if __name__ == "__main__":
    # 计算 Gram 矩阵
    Gram = cal_gram()
    # 循环执行 1000 次
    for i in range(1000):
```