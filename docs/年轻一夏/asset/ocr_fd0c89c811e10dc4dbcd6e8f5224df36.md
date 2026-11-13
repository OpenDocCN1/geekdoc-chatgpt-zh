# 8.5.4 实验步骤

本实验的实验环境为 Python 3.3.2 环境。编写遗传算法类，代码如下。

```
import random
from Life import Life
class GA(object):
    def __init_(self, aCrossRate, aMutationRage, aLifeCount, aGeneLenght, aMatchFun = lambda life : 1):
        self.croessRate = aCrossRate
        self.mutationRate = aMutationRage
        self.lifeCount = aLifeCount
        self.geneLenght = aGeneLenght
        self.matchFun = aMatchFun    # 适配函数
        self.lives = []    # 种群
        self.best = None    # 保存这一代中最好的个体
        self.generation = 1
        self.crossCount = 0
        self.mutationCount = 0
        self.bounds = 0.0    # 适配值之和
        self.initPopulation()
```

初始化种群的代码如下。

```
def initPopulation(self):
    self.lives = []
    for i in range(self.lifeCount):
        gene = [x for x in range(self.geneLenght)]
        random.shuffle(gene)
        life = Life(gene)
        self.lives.append(life)
```

个体评价（计算每个个体的适配度）的代码如下。

```
def judge(self):
    self.bounds = 0.0
    self.best = self.lives[0]
    for life in self.lives:
        life.score = self.matchFun(life)
        self.bounds += life.score
        if self.best.score < life.score:
            self.best = life
```

交叉运算的代码如下。

```
def cross(self, parent1, parent2):
    index1 = random.randint(0, self.geneLenght - 1)
    index2 = random.randint(index1, self.geneLenght - 1)
    tempGene = parent2.gene[index1:index2]    # 交叉的基因片段
    newGene = []
```