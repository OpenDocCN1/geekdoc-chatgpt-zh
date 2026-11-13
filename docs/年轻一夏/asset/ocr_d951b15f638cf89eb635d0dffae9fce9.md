```
count_similar += 1
else:
    count_different += 1
if x > 0 and (x - 1, y) not in self.empty_houses:
    if self.agents[(x - 1, y)] == race:
        count_similar += 1
    else:
        count_different += 1
if x < (self.width - 1) and (x + 1, y) not in self.empty_houses:
    if self.agents[(x + 1, y)] == race:
        count_similar += 1
    else:
        count_different += 1
if x > 0 and y < (self.height - 1) and (x - 1, y + 1) not in self.empty_houses:
    if self.agents[(x - 1, y + 1)] == race:
        count_similar += 1
    else:
        count_different += 1
if x > 0 and y < (self.height - 1) and (x, y + 1) not in self.empty_houses:
    if self.agents[(x, y + 1)] == race:
        count_similar += 1
    else:
        count_different += 1
if x < (self.width - 1) and y < (self.height - 1) and (x + 1, y + 1) not in self.empty_houses:
    if self.agents[(x + 1, y + 1)] == race:
        count_similar += 1
    else:
        count_different += 1
if (count_similar + count_different) == 0:
    return False
else:
    return float(count_similar) / (count_similar + count_different) < self.similarity_threshold
```

③ update()方法。这种方法查看网格上的居民是否满意，如果尚未满意，则将随机把此人分配到空房子中，并模拟 n_iterations 次。代码如下。

```
def update(self):
    for i in range(self.n_iterations):
        self.old_agents = copy.deepcopy(self.agents)
        n_changes = 0
        for agent in self.old_agents:
            if self.is_unsatisfied(agent[0], agent[1]):
                agent_race = self.agents[agent]
                empty_house = random.choice(self.empty_houses)
                self.agents[empty_house] = agent_race
                del self.agents[agent]
```