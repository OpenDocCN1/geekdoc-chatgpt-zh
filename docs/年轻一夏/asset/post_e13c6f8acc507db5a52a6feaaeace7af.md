
## 8.4.3 细胞系统建模

下面将介绍如何实际构建细胞真实世界的模型并使用模型研究其属性。可以根据下列步骤来定义和运行一个细胞模型。

- (1) 指定细胞空间。
- (2) 指定时间变量。
- (3) 指定邻域。
- (4) 分配状态集。
- (5) 指定迁移规则。
- (6) 指定边界条件。
- (7) 指定初始条件。
- (8) 指定停止条件。
- (9) 一直更新细胞状态，直到满足停止条件。

为了说明上面列出的建模步骤的应用，下面将介绍如何定义一个简单的交通 CA 模型。要模拟有限的单向延伸及单车道路 [见图 8-16 (a)]，可通过将一段道路离散化成有限长度的单元格来实现，由此产生的细胞空间是一个一维有限的细胞网格，如图 8-16 (b) 所示。在 CA 中，时间变量是离散的，车辆被模拟为在离散时间间隔中移动。因此，现实交通流量仅可通过平均越过若干时间步或越过若干细胞来衡量。假设每个细胞的状态仅受其相邻细胞影响，即 CA 邻域由 3 个细胞组成，如图 8-16 (c) 所示。假定每个单元格包含一辆汽车或为空，这意味着状态集只包含两个状态，如图 8-16 (d) 所示。当然，这个假设与在一段离散化的道路上每个单元格赋予的长度有关，这段道路必须足够大，可以包含一个车辆，但也不能太大，最多一个汽车放在其上。迁移规则则模拟车辆从左至右的移动，并且指定当目标单元格为空时，车辆可以而且必须前进。这种规则可以用迁移表来表示，如图 8-16 (e) 所示。指定周期型边界条件，使车辆从右边离开细胞空间后从左边再进入，如图 8-16 (f) 所示。要运行这个模拟过程，现在必须分配初始条件。使用一个随机的汽车分布密度 ρ 作为初始条件，该密度从 0（空路）到 1（每个单元格被一辆车占用）变动。注意，迁移规则确保车辆既不能创造也不能消灭，即车辆数目守恒，因此指派的密度在整个 CA 演化过程中保持不变。

运行 CA 可以看到，初始转变后，车流量达到一个稳定状态，该状态循环重现。图 8-17 所示为不同汽车分布密度下的 CA 时空图。黑色细胞对应单元格被汽车占用，白色细胞对应空单元格。注意这两个图在质量上的差别。对于低密度 ρ=0.3，一旦初始瞬态结束，时空图中只保留了对角线上的白色细胞，在模型中对应于在相同交通方向沿着这条路前的空延伸。对于高密度 ρ=0.7，在瞬态结束后，只保留了对角线上的黑色细胞，表示沿着这条路的方向流通堵塞。

![](img/5b6827eaabeea08141bb17cb00914cdf_230_0.png)

### 图 8-16 细胞自动机流通模型元素

![](img/5b6827eaabeea08141bb17cb00914cdf_230_1.png)

### 图 8-17 不同汽车分布密度下的 CA 时空图

图 8-17（a）所示的时空图说明 30%的道路被车辆占据；图 8-17（b）所示的时空图说明 70%的道路被车辆占据。

## 8.4.4 康威生命游戏

康威生命游戏（Conway's Game of Life），又称康威生命棋，是英国数学家约翰·霍顿·康威在 1970 年发明的细胞自动机。

康威生命游戏包括一个二维矩形世界，其中的每个方格居住着一个活着的或死了的细胞，即每个细胞具有两个状态。一个细胞在下一个时刻的生死取决于相邻 8 个方格中活着的或死了的细胞数量，即采用全同型规则。如果相邻方格活着的细胞数量过多，那么这个细胞会因为资源匮乏而在下一个时刻死去；相反，如果周围活着的细胞过少，那么这个细胞会因太孤单而死去。在实际中，玩家可以设定周围活细胞的数目怎样时才适宜该细胞的生存。如果这个数目设定过高，那么世界中的大部分细胞会因为找不到太多的活的邻居而死去，直到整个世界都没有生命为止；如果这个数目设定过低，那么世界中又会被生命充满而没有什么变化。

在实际中，这个数目一般选为 2 或 3，这样整个生命世界才不至于太过荒凉或拥挤，而是达到一种动态的平衡。如果这样，游戏的规则就是：当一个方格周围有 2 或 3 个活细胞时，方格中的活细胞在下一个时刻继续存活；即使这个时刻方格中没有活细胞，在下一个时刻也会“诞生”活细胞。

在这个游戏中，还可以设定一些更加复杂的规则，如当前方格的状况不仅由父一代决定，而且还考虑祖父一代的情况。玩家还可以作为这个世界的“上帝”，随意设定某个方格细胞的死活以观察对世界的影响。

在游戏进行中，杂乱无序的细胞会逐渐演化出各种精致、有形的结构，这些结构往往有很好的对称性，而且每一代都在变换形状。一些形状已经锁定，不会逐代变化。有时，一些已经成形的结构会因为一些无序细胞的“入侵”而被破坏。但是形状和秩序经常能从杂乱中产生出来。

康威生命游戏的意义在于：验证了某些科学家的宇宙观，即最简单的逻辑规则能产生复杂有趣的活动。康威生命游戏在方格网上进行，有点像围棋。有填充的网格代表有生命，或者理解成一个细胞，或者按中国传统，可把填充和无填充理解成“有”和“无”。

游戏规则只有以下 4 条。

- (1) 当周围仅有一个或没有活细胞时，原来的活细胞进入死亡状态。（模拟生命数量稀少）
- (2) 当周围有两个或 3 个活细胞时，网格保持原样。
- (3) 当周围有 4 个及以上活细胞时，原来的活细胞也进入死亡状态。（模拟生命数量过多）
- (4) 当周围有 3 个活细胞时，空白网格变成活细胞。（模拟繁殖）

康威生命游戏的 4 条规则一目了然地对应着宇宙中的生命规律，“种子”长成“花朵”，“花朵”死后留下 4 个“种子”，它就是一种元胞自动机（Cellular Automaton），体现了冯·诺依曼（Von Neumann）关于机器自我进化的思想。康威生命游戏中更多的代表性细胞类型包括静物（Still Lifes）、方块（Block）、蜂窝（Beehive）、吐司（Loaf）、小船（Boat）、浴缸（Tub）。还有一类称为振荡器（Oscillator），其可从初始形态开始，在有限图形之间切换，周而复始。甚至还有会整体移动的，如太空飞船（Spaceship）类型、滑翔机（Glider）等。

康威生命游戏从原版到后来的众多变种，差别只在量变，而规则的本质没有变。它模拟的是生物体数量与环境资源的关系，反映了宇宙基本规律。过于孤独和过于拥挤，都不利于物种延续。感兴趣的读者请上网参考更多资料。

## 8.5 实验：基于遗传算法的 TSP 问题求解

### 8.5.1 实验目的

- (1) 熟悉 Python 的基本操作环境。
- (2) 了解旅行商问题（TSP）。
- (3) 了解遗传算法的基本原理。
- (4) 运行程序，看到结果。

### 8.5.2 实验要求

- (1) 对 TSP 有进一步的了解。
- (2) 了解遗传算法的基本原理。
- (3) 能用遗传算法实现 TSP 问题求解。

### 8.5.3 实验原理

遗传算法的基本运算过程如下。

- (1) 初始化：设置进化代数计数器 t=0，设置最大进化代数 T，随机生成 M 个个体作为初始群体 P(0)。
- (2) 个体评价：计算群体 P(t)中各个个体的适应度。
- (3) 选择运算：将选择算子作用于群体，选择的目的是把优化的个体直接遗传到下一代或通过配对交叉产生新的个体再遗传到下一代，选择操作是建立在群体中个体的适应度评估基础上的。
- (4) 交叉运算：将交叉算子作用于群体，遗传算法中起核心作用的就是交叉算子。
- (5) 变异运算：将变异算子作用于群体，即对群体中的个体串的某些基因座上的基因值做变动，群体 P(t)经过选择、交叉、变异运算之后得到下一代群体 P(t+1)。
- (6) 终止条件判断：若 t=T，则以进化过程中所得到的具有最大适应度个体作为最优解输出，终止计算。

### 8.5.4 实验步骤

本实验的实验环境为 Python 3.3.2 环境。编写遗传算法类，代码如下。

```
import random
from Life import Life
class GA(object):
    def __init__(self, aCrossRate, aMutationRate, aLifeCount, aGeneLength, aMatchFun = lambda life : 1):
        self.crossRate = aCrossRate
        self.mutationRate = aMutationRate
        self.lifeCount = aLifeCount
        self.geneLength = aGeneLength
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
        gene = [x for x in range(self.geneLength)]
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
    index1 = random.randint(0, self.geneLength - 1)
    index2 = random.randint(index1, self.geneLength - 1)
    tempGene = parent2.gene[index1:index2]    # 交叉的基因片段
    newGene = []
    fillen = 0
    for g in parent1.gene:
        if fillen == index1:
            newGene.extend(tempGene) # 插入基因片段
            fillen += 1
        if g not in tempGene:
            newGene.append(g)
            fillen += 1
    self.crossCount += 1
    return newGene
```

变异运算的代码如下。

```
def mutation(self, gene):
    index1 = random.randint(0, self.geneLength - 1)
    index2 = random.randint(0, self.geneLength - 1)
    newGene = gene[:] # 产生一个新的基因序列，以免变异的时候影响父种群
    newGene[index1], newGene[index2] = newGene[index2], newGene[index1]
    self.mutationCount += 1
    return newGene
```

选择一个个体的代码如下。

```
def getOne(self):
    r = random.uniform(0, self.bounds)
    for life in self.lives:
        r -= life.score
        if r <= 0:
            return life
    raise Exception("选择错误", self.bounds)
```

产生新后代的代码如下。

```
def newChild(self):
    parent1 = self.getOne()
    rate = random.random()
    # 按概率交叉
    if rate < self.crossRate:
        # 交叉
        parent2 = self.getOne()
        gene = self.cross(parent1, parent2)
    else:
        gene = parent1.gene
    # 按概率突变
    rate = random.random()
    if rate < self.mutationRate:
        gene = self.mutation(gene)
    return Life(gene)
```

产生下一代的代码如下。

```
def next(self):
    self.judge()
    newLives = []
    newLives.append(self.best) #把最好的个体加入下一代
    while len(newLives) < self.lifeCount:
        newLives.append(self.newChild())
    self.lives = newLives
    self.generation += 1
```

个体类的代码如下。

```
SCORE_NONE = -1
class Life(object):
    def __init__(self, aGene = None):
        self.gene = aGene
        self.score = SCORE_NONE
```

给出中国 34 个城市的经纬度，求出最短路径长度，代码如下。

```
import random
import math
import sys
if sys.version_info.major < 3:
    import Tkinter
else:
    import tkinter as Tkinter

from GA import GA

class TSP_WIN(object):
    def __init__(self, aRoot, aLifeCount = 100, aWidth = 560, aHeight = 330):
        self.root = aRoot
        self.lifeCount = aLifeCount
        self.width = aWidth
        self.height = aHeight
        self.canvas = Tkinter.Canvas(
            self.root,
            width = self.width,
            height = self.height,
        )
        self.canvas.pack(expand = Tkinter.YES, fill = Tkinter.BOTH)
        self.bindEvents()
        self.initCitys()
        self.new()
        self.title("TSP")

    def initCitys(self):
        self.citys = []
        #中国 34 个城市的经纬度
        self.citys.append((116.46, 39.92))
        self.citys.append((117.2,39.13))
        self.citys.append((121.48, 31.22))
        self.citys.append((106.54, 29.59))
        self.citys.append((91.11, 29.97))
        self.citys.append((87.68, 43.77))
        self.citys.append((106.27, 38.47))
        self.citys.append((111.65, 40.82))
        self.citys.append((108.33, 22.84))
        self.citys.append((126.63, 45.75))
        self.citys.append((125.35, 43.88))
        self.citys.append((123.38, 41.8))
        self.citys.append((114.48, 38.03))
        self.citys.append((112.53, 37.87))
        self.citys.append((101.74, 36.56))
        self.citys.append((117.36,65))
        self.citys.append((113.6,34.76))
        self.citys.append((118.78, 32.04))
        self.citys.append((117.27, 31.86))
        self.citys.append((120.19, 30.26))
        self.citys.append((119.3, 26.08))
        self.citys.append((115.89, 28.68))
        self.citys.append((113, 28.21))
        self.citys.append((114.31, 30.52))
        self.citys.append((113.23, 23.16))
        self.citys.append((121.5, 25.05))
        self.citys.append((110.35, 20.02))
        self.citys.append((103.73, 36.03))
        self.citys.append((108.95, 34.27))
        self.citys.append((104.06, 30.67))
        self.citys.append((106.71, 26.57))
        self.citys.append((102.73, 25.04))
        self.citys.append((114.1, 22.2))
        self.citys.append((113.33, 22.13))
        #坐标变换
        minX, minY = self.citys[0][0], self.citys[0][1]
        maxX, maxY = minX, minY
        for city in self.citys[1:]:
            if minX > city[0]:
                minX = city[0]
            if minY > city[1]:
                minY = city[1]
            if maxX < city[0]:
```
