![](bbox=[0.29, 0.06, 0.66, 0.29])

# ## 2. 掷 1 个骰子是 1 点概率的大数法则：Ch11_1_3a.py

骰子点数是 1～6 点，掷 1 个“公正”骰子是 1 点的实际概率是 1/6 ≈ 0.167（16.7%）。可以使用随机数模拟掷 1 个骰子从 1 次至 10000 次，然后计算每一次试验出现 1 点的概率，如下所示：

```
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

results = []
for num_throws in range(1, 10001):
    throws = np.random.randint(low=1, high=7, size=num_throws)
    mask = (throws == 1)
    probability_of_throws = len(throws[mask])/num_throws
    results.append(probability_of_throws)
```

上述程序代码中，for 循环是 1～10000 次，num_throws 是掷 1 个骰子的总次数。调用 random.randint() 函数产生 num_throws 次数的 1～6，代表 1～6 点。因为需要从 NumPy 数组搜索值是 1 的有几个，所以建立条件屏蔽 mask 数组条件是 throws 等于 1。 len(throws[mask]) 函数计算 1 点的次数，除以 num_throws 总次数就是掷骰子出现 1 点的概率。最后使用 results 列表建立 DataFrame 对象和绘制折线图，如下所示：

```
df = pd.DataFrame({"throws" : results})

df.plot(color="x")
plt.title("Law of Large Numbers")
plt.xlabel("Number of throws in sample")
plt.ylabel("Average Of Sample")
```

上述程序代码可以绘制红色线条的折线图，其运行结果如图 11-3 所示。