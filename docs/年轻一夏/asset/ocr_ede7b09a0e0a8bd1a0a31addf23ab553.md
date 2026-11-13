```
self.empty_houses.remove(empty_house)
self.empty_houses.append(agent)
n_changes += 1
print(n_changes)
if n_changes == 0:
    break
```

④ plot()方法。可以调用这种方法来了解城市居民的居住分布。这种方法有两个传入参数：title 和 file_name。代码如下。

```
def plot(self, title, file_name):
    fig, ax = plt.subplots()
    agent_colors = {1: 'r', 2: 'g', 3: 'b', 4: 'c', 5: 'm', 6: 'y', 7: 'k'}
    for agent in self.agents:
        ax.scatter(agent[0]+0.5, agent[1] + 0.5, s = 10, color=agent_colors[self.agents[agent]])
        ax.set_title(title, fontsize=6, fontweight="bold")
        ax.set_xlim([0, self.width])
        ax.set_ylim([0, self.height])
        ax.set_xticks([])
        ax.set_yticks([])
    plt.savefig(file_name)
```

# 2. 仿真参数设置

表 9-2 所示为仿真参数。

| 房子规模 | 50×50=2500 间房子 |
| --- | --- |
| 空房子比例 | 30% |
| 种族数 | 2 (用不同颜色表示) |
| 最久迭代数 | 500 |
| 邻居种族相似比阈值 | 30%, 50%, 80% |

# 3. 按照上述参数进行 3 次仿真

(1) 创建并“填充”城市。注意，邻居种族相似比阈值在城市的初始状态不起作用。代码如下。

```
if __name__ == "__main__":
    schelling_1 = Schelling(50, 50, 0.3, 0.3, 500, 2)
    schelling_1.populate()
    schelling_2 = Schelling(50, 50, 0.3, 0.5, 500, 2)
    schelling_2.populate()
    schelling_3 = Schelling(50, 50, 0.3, 0.8, 500, 2)
    schelling_3.populate()
```

(2) 绘制初始状态的城市。代码如下。

```
schelling_1.plot(Schelling Model with 2 colors: Initial State', 'schelling_2_initial.png')
```

其结果如图 9-13 所示。