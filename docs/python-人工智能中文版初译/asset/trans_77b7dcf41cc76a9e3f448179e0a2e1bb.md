

## 符号回归

```
 toolbox.decorate("mate", gp.staticLimit(key=operator.attrgetter("height"), max_value=17))
    toolbox.decorate("mutate", gp.staticLimit(key=operator.attrgetter("height"), max_value=17))
```

```
 return toolbox
```

定义`main`函数并从播种随机数生成器开始：

```
if __name__ == "__main__":
    random.seed(7)
```

创建`toolbox`对象：

```
 toolbox = create_toolbox()
```

使用`450`个体使用`toolbox`对象中可用的方法定义初始种群。人数可以更改。随时尝试。还定义`hall_of_fame`对象：

```
 population = toolbox.population(n=450)
    hall_of_fame = tools.HallOfFame(1)
```

建立 GA 时，统计数据非常有用。定义统计对象：

```
 stats_fit = tools.Statistics(lambda x: x.fitness.values)
    stats_size = tools.Statistics(len)
```

使用先前定义的对象注册统计信息：

```
 mstats = tools.MultiStatistics(fitness=stats_fit, size=stats_size)
    mstats.register("avg", np.mean)
    mstats.register("std", np.std)
    mstats.register("min", np.min)
    mstats.register("max", np.max)
```

定义交叉概率，突变概率和世代数：

```
 probab_crossover = 0.4
    probab_mutate = 0.2
    num_generations = 60
```

使用以上参数运行进化算法：

```
 population, log = algorithms.eaSimple(population, toolbox,
            probab_crossover, probab_mutate, num_generations,
            stats=mstats, halloffame=hall_of_fame, verbose=True)
```

完整代码在文件`symbol_regression.py`中给出。如果运行代码，则在演化开始时将看到以下内容：

![](Images/c9b2e2e6a82147e1ffd72b1d52512fed.png)

图 9：演化过程的初始输出

最后，您将看到以下内容：

![](Images/8d655b717a3cf47adddad6c72f1fc75e.png)

图 10：演进进度最终输出

我们可以看到`min`列中的值越来越小，这表明方程的近似解的误差越来越小。

# 构建智能机器人控制器

让我们看看如何使用 GA 构建机器人控制器。我们得到了一张地图，上面洒满了目标。

地图看起来像这样。哈希表示机器人要击中所需的目标：

![](Images/4f872431c2ded469269aa6310629b07a.png)

图 11：带有 AI 机器人需要击中的目标的地图，目标以哈希表示

前面的地图中有 124 个目标。机器人控制器的目标是自动遍历地图并消耗所有这些目标。该程序是`deap`库中提供的人工蚂蚁程序的变体。

创建一个新的 Python 文件并导入以下内容：

```
import copy
import random
from functools import partial
import numpy as np
from deap import algorithms, base, creator, tools, gp
```

创建用于控制机器人的类：

```
class RobotController(object):
    def __init__(self, max_moves):
        self.max_moves = max_moves
        self.moves = 0
        self.consumed = 0
        self.routine = None
```

定义方向和运动：

```
 self.direction = ["north", "east", "south", "west"]
    self.direction_row = [1, 0, -1, 0]
    self.direction_col = [0, 1, 0, -1]
```

定义重置函数：

```
 def _reset(self):
        self.row = self.row_start
        self.col = self.col_start
        self.direction = 1
        self.moves = 0
        self.consumed = 0
        self.matrix_exc = copy.deepcopy(self.matrix)
```

定义条件控制器：

```
 def _conditional(self, condition, out1, out2):
        out1() if condition() else out2()
```

定义左转弯控制器：

```
 def turn_left(self):
        if self.moves < self.max_moves:
            self.moves += 1
            self.direction = (self.direction - 1) % 4
```

定义正确的右转控制器：

```
 def turn_right(self):
        if self.moves < self.max_moves:
            self.moves += 1
            self.direction = (self.direction + 1) % 4
```

定义控制机器人前进方式的方法：

```
 def move_forward(self):
        if self.moves < self.max_moves:
            self.moves += 1
            self.row = (self.row + self.direction_row[self.direction]) % self.matrix_row
           self.col = (self.col + self.direction_col[self.direction]) % self.matrix_col
            if self.matrix_exc[self.row][self.col] == "target":
                self.consumed += 1
```

```
 self.matrix_exc[self.row][self.col] = "passed"
```

定义一种检测目标的方法。如果您看到前方的目标，请相应地更新矩阵：

```
 def sense_target(self):
        ahead_row = (self.row + self.direction_row[self.direction]) % self.matrix_row
        ahead_col = (self.col + self.direction_col[self.direction]) % self.matrix_col
        return self.matrix_exc[ahead_row][ahead_col] == "target"
```

如果看到前面的目标，则创建相关函数并返回它：

```
 def if_target_ahead(self, out1, out2):
        return partial(self._conditional, self.sense_target, out1, out2)
```

定义运行它的方法：

```
 def run(self,routine):
        self._reset()
        while self.moves < self.max_moves:
            routine()
```

定义一个函数以遍历输入图。符号`#`指示地图上的所有目标，符号`S`指示起点。符号`.`表示空白单元格：

```
 def traverse_map(self, matrix):
        self.matrix = list()
        for i, line in enumerate(matrix):
            self.matrix.append(list())
```

```
 for j, col in enumerate(line):
                if col == "#":
                    self.matrix[-1].append("target")
```

```
 elif col == ".":
                    self.matrix[-1].append("empty")
```

```
 elif col == "S":
                    self.matrix[-1].append("empty")
                    self.row_start = self.row = i
                    self.col_start = self.col = j
                    self.direction = 1
```

```
 self.matrix_row = len(self.matrix)
        self.matrix_col = len(self.matrix[0])
        self.matrix_exc = copy.deepcopy(self.matrix)
```

定义一个类以根据输入参数的数量生成函数：

```
class Prog(object):
    def _progn(self, *args):
        for arg in args:
            arg()
```

```
 def prog2(self, out1, out2):
        return partial(self._progn, out1, out2)
```

```
 def prog3(self, out1, out2, out3):
        return partial(self._progn, out1, out2, out3)
```

为个体定义评估函数：

```
def eval_func(individual):
    global robot, pset
```

```
 # Transform the tree expression to functional Python code
    routine = gp.compile(individual, pset)
```

运行程序：

```
 # Run the generated routine
    robot.run(routine)
    return robot.consumed,
```

定义一个函数来创建`toolbox`并添加原语：

```
def create_toolbox():
    global robot, pset
    pset = gp.PrimitiveSet("MAIN", 0)
    pset.addPrimitive(robot.if_target_ahead, 2)
    pset.addPrimitive(Prog().prog2, 2)
    pset.addPrimitive(Prog().prog3, 3)
    pset.addTerminal(robot.move_forward)
    pset.addTerminal(robot.turn_left)
    pset.addTerminal(robot.turn_right)
```

使用适应度函数创建对象类型：

```
 creator.create("FitnessMax", base.Fitness, weights=(1.0,))
    creator.create("Individual", gp.PrimitiveTree, fitness=creator.FitnessMax)
```

创建`toolbox`并注册所有运算符：

```
 toolbox = base.Toolbox()
```

```
 # Attribute generator
    toolbox.register("expr_init", gp.genFull, pset=pset, min_=1, max_=2)
```

```
 # Structure initializers
    toolbox.register("individual", tools.initIterate, creator.Individual, toolbox.expr_init)
    toolbox.register("population", tools.initRepeat, list, toolbox.individual)
```

```
 toolbox.register("evaluate", eval_func)
    toolbox.register("select", tools.selTournament, tournsize=7)
    toolbox.register("mate", gp.cxOnePoint)
```

```
 toolbox.register("expr_mut", gp.genFull, min_=0, max_=2)
    toolbox.register("mutate", gp.mutUniform, expr=toolbox.expr_mut, pset=pset)
```

```
 return toolbox
```

定义`main`函数，并从播种随机数生成器开始：

```
if __name__ == "__main__":
    global robot
```

```
 # Seed the random number generator
    random.seed(7)
```

使用初始化参数创建机械手控制器对象：

```
 # Define the maximum number of moves
    max_moves = 750
```

```
 # Create the robot object
    robot = RobotController(max_moves)
```

使用我们之前定义的函数创建`toolbox`：

```
 # Create the toolbox
    toolbox = create_toolbox()
```

从输入文件中读取地图数据：

```
 # Read the map data
    with open('target_map.txt', 'r') as f:
      robot.traverse_map(f)
```

用`400`个体定义总体并定义`hall_of_fame`对象：

```
 # Define population and hall of fame variables
    population = toolbox.population(n=400)
    hall_of_fame = tools.HallOfFame(1)
```

注册`stats`：

```
 # Register the stats
    stats = tools.Statistics(lambda x: x.fitness.values)
    stats.register("avg", np.mean)
    stats.register("std", np.std)
    stats.register("min", np.min)
    stats.register("max", np.max)
```

定义交叉概率，突变概率和世代数：

```
 # Define parameters
    probab_crossover = 0.4
    probab_mutate = 0.3
    num_generations = 50
```

使用前面定义的参数运行进化算法：

```
 # Run the algorithm to solve the problem
    algorithms.eaSimple(population, toolbox, probab_crossover,
            probab_mutate, num_generations, stats,
            halloffame=hall_of_fame)
```

完整代码在文件`robot.py`中给出。如果运行代码，您将获得以下：

![](Images/587f4d141d3f8464f76278c0320a44d6.png)

图 12：演化过程的初始输出

在结束时，您将看到以下内容：

![](Images/478ffa8f43f338f0bb828bbff1d59f07.png)

图 13：演进过程的最终输出

