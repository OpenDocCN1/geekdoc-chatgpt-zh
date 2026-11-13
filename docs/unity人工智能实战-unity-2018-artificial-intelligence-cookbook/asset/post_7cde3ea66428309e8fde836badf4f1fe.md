
GameAction[] actions;
actions = problem.GetAvailableActions(state);
GameAction action;

- 14. 根据探测出的随机值，获取一个行动:
- if (Random.value < rho)
    action = GetRandomAction(actions);
- else
    action = store.GetBestAction(state);

- 15. 计算新的状态，用于执行基于当前状态和作为结果的奖励值上选择的行动:
- float reward = 0f;
- GameState newState;
- newState = problem.TakeAction(state, action, ref reward);

- 16. 根据当前游戏状态取得 q 值，然后执行之前计算出新状态的最佳行动:
- float q = store.GetQValue(state, action);
- GameAction bestAction = store.GetBestAction(newState);
- float maxQ = store.GetQValue(newState, bestAction);

- 17. 应用 Q-learning 公式:
- q = (1f - alpha) * q + alpha * (reward + gamma * maxQ);

- 18. 保存计算出的 q 值，将其父值作为索引:
- store.StoreQValue(state, action, q);
- state = newState;

### 运行原理

在 Q-learning 算法中，游戏世界被看作一个状态机，要特别注意这些参数的意义：

- alpha: 学习率
- gamma: 打折率
- rho: 探测的随机性
- nu: 运行的时间

## 8.6 实现人工神经网络

假设我们设计一种可以模拟大脑思考方式的敌人或游戏系统，这就是神经网络的运作方式，它们基于神经元（我们称之为感知器（Perceptron）），是几个神经元的总合，它的输入和输出构成了一个神经网络。

在本书中，我们要学习如何构建一个神经系统，从 Perceptron 开始，连接这些感知器，创建一个神经网络。

### 准备工作

我们需要一个用于处理原始输入的数据类型，命名为 InputPerceptron：

```
public class InputPerceptron
{
    public float input;
    public float weight;
}
```

### 操作步骤

实现两个大类：第一个类是 Perceptron 数据类型的实现；第二个类是管理神经网络的数据类型。按照下面的步骤实现这两个类：

- 1. 实现 Perceptron 类，该类继承之前定义的 InputPerceptron 类：

```
public class Perceptron : InputPerceptron
{
    public InputPerceptron[] inputList;
    public delegate float Threshold(float x);
    public Threshold threshold;
    public float state;
    public float error;
}
```

- 2. 实现构造函数，用于初始化输入的数量值：

```
public Perceptron(int inputSize)
{
    inputList = new InputPerceptron[inputSize];
}
```

- 3. 定义用于处理输入值的函数：

```
public void FeedForward()
{
    float sum = 0f;
    foreach (InputPerceptron i in inputList)
    {
        sum += i.input * i.weight;
    }
    state = threshold(sum);
}
```

- 4. 实现用于调整权重的函数：

```
public void AdjustWeights(float currentError)
{
    int i;
    for (i = 0; i < inputList.Length; i++)
    {
        float deltaWeight;
        deltaWeight = currentError * inputList[i].weight * state;
        inputList[i].weight = deltaWeight;
    }
    error = currentError;
}
```

- 5. 定义函数，用于根据输入值的类型返回权重：

```
public float GetIncomingWeight()
{
    foreach (InputPerceptron i in inputList)
    {
        if (i.GetType() == typeof(Perceptron))
        return i.weight;
    }
    return 0f;
}
```

- 6. 创建将 Perceptron 元素的集合作为一个网络来管理的类：

```
using UnityEngine;
using System.Collections;
public class MLPNetwork : MonoBehaviour
{
    public Perceptron[] inputPer;
    public Perceptron[] hiddenPer;
    public Perceptron[] outputPer;
}
```

- 7. 实现函数，用于把输入值从神经网络的一端传递到另一端：

```
public void GenerateOutput(Perceptron[] inputs)
{
    int i;
    for (i = 0; i < inputs.Length; i++)
        inputPer[i].state = inputs[i].input;
    for (i = 0; i < hiddenPer.Length; i++)
        hiddenPer[i].FeedForward();
    for (i = 0; i < outputPer.Length; i++)
        outputPer[i].FeedForward();
}
```

- 8. 定义函数，用于推进实际模拟学习的计算过程：

```
public void BackProp(Perceptron[] outputs)
{
    // 下面的步骤
}
```

- 9. 遍历输出层，用于计算值：

```
int i;
for (i = 0; i < outputPer.Length; i++)
{
    Perceptron p = outputPer[i];
    float state = p.state;
    float error = state * (1f - state);
    error *= outputs[i].state - state;
    p.AdjustWeights(error);
}
```

- 10. 遍历内部的 Perceptron 层:

```
for (i = 0; i < hiddenPer.Length; i++)
{
    Perceptron p = outputPer[i];
    float state = p.state;
    float sum = 0f;
    for (i = 0; i < outputs.Length; i++)
    {
        float incomingW = outputs[i].GetIncomingWeight();
        sum += incomingW * outputs[i].error;
        float error = state * (1f - state) * sum;
        p.AdjustWeights(error);
    }
}
```

- 11. 实现一个易用的上层函数:

```
public void Learn(
    Perceptron[] inputs,
    Perceptron[] outputs)
{
    GenerateOutput(inputs);
    BackProp(outputs);
}
```

### 运行原理

我们实现了两种类型的 Perceptron，用于处理外部输入的感知器，以及在内部互相连接的感知器。这就是为什么基础 Perceptron 类继承自后者。FeedForward 函数处理输入并顺着神经网络冲洗这些输入值。最后，用于反向传播的函数负责调整权重，权重调整是对学习的模拟。

## Chapter 9 第 9 章

### 程序化内容生成

本章中，我们将学习用于程序化内容生成的技术，包括以下内容：

- 用深度优先搜索创建迷宫
- 为地下城和群岛实现可构造算法
- 生成风景
- 使用 N 元语法生成内容
- 用进化算法生成敌人

### 9.1 简介

我们可以用算法在游戏中定义程序化内容生成（PCG）作为内容的生成器，人为参与与否均可。这项技术对于学术界和工业界都是热门话题，而工业界的范围从大型的工作室跨度到小型的独立开发者。PCG 可以用于生成植被、生成高级的细节、创建完整的迷宫和游戏世界供玩家探索，还可以增加游戏的可重玩性和游玩时间。

本章中涵盖的几项不同技术可以作为初探，引导我们在正确的方向上继续深入。

### 9.2 用深度优先搜索创建迷宫

我们将用创建迷宫的搜索算法作为起点开启程序化内容生成的旅程。我们要使用在第 2 章中学习的基础创建迷宫。

### 准备工作

我们将使用网格表示图结构，对应地用布尔值表示单元格是否是墙体。

### 操作步骤

我们将把所有内容包含在一个类中，这个类管理抽象表示，命名为 DFSDungeon：

- 1. 定义 DFSDungeon 组件及其成员变量:

```
using UnityEngine;
using System.Collections.Generic;

public class DFSDungeon : MonoBehaviour
{
    public int width;
    public int height;
    public bool[,] dungeon;
    public bool[,] visited;
    private Stack<Vector2> stack;
    private Vector2 current;
    private int size;

    // next steps
}
```

- 2. 定义初始化函数

```
private void Init()
{
    // next steps
}
```

- 3. 初始化要用到的变量和随机初始位置:

```
stack = new Stack<Vector2>();
size = width * height;
dungeon = new bool[height, width];
visited = new bool[height, width];
current.x = Random.Range(0, width - 1);
current.y = Random.Range(0, height - 1);
```

- 4. 把墙体赋值给地下城的每个单元:

```
int i, j;
for (j = 0; j < height; j++)
    for (i = 0; i < width; i++)
        dungeon[j, i] = true;
```

- 5. 把初始位置插入栈中:

```
stack.Push(current);
i = (int)current.x;
j = (int)current.y;
```

- 6. 把单元格标记为已访问，然后减小剩余单元格的数量:

```
visited[j, i] = true;
size--;
```

- 7. 定义成员函数，用于获取单元格周围的 8 个邻接单元：

```
private Vector2[] GetNeighbors(Vector2 node)
{
    // next steps
}
```

- 8. 初始化要用到的变量，用于定义要遍历的左上角和右下角：

```
List<Vector2> neighbors = new List<Vector2>();
int originX, targetX, originY, targetY;
originX = (int)node.x - 1;
originY = (int)node.y - 1;
targetX = (int)node.x + 1;
targetY = (int)node.y + 1;
int i, j;
```

- 9. 遍历所有单元格，将有效且可用的单元格添加到邻接列表中：

```
for (j = originY; j < targetY; j++)
{
    if (j < 0 || j >= height)
        continue;
    for (i = originX; i < targetX; i++)
    {
        if (i < 0 || i >= width)
        if (i == node.x && j == node.y)
            continue;
        if (visited[j,i])
            continue;
        neighbors.Add(new Vector2(i, j));
    }
}
```

- 10. 将邻接列表作为数组返回：

```
return neighbors.ToArray();
```

- 11. 定义用于构建迷宫的函数：

```
public void Build()
{
    Init();
    // next steps
}
```

- 12. 遍历剩余位置：

```
while (size > 0)
{
    // next steps
}
```

- 13. 获取当前单元格的邻接数组：

```
Vector2[] neighbors = GetNeighbors(current);
```

- 14. 验证邻接数组的数量是否大于 0：

```
if (neighbors.Length > 0)
{
    // next step
}
```

- 15. 如果大于 0，则将当前单元格添加到栈中：

```
stack.Push(current);
```

- 16. 选择一个随机邻接单元格，移除当前单元格与邻接单元格之间的墙体：

```
int rand = Random.Range(0, neighbors.Length - 1);
Vector2 n = neighbors[rand];
int i, j;
i = (int)current.y;
j = (int)current.x;
dungeon[j, i] = false;
i = (int)n.y;
j = (int)n.x;
dungeon[j, i] = false;
```

- 17. 标记邻接点为已遍历：

```
visited[j, i] = true;
current = n;
size--;
```

- 18. 如果栈中还有元素，则从栈中获取一个新的位置：

```
else if (stack.Count > 0)
    current = stack.Pop();
```

### 实现原理

我们使用深度优先搜索的原理遍历图结构。原理是这样，使用一个栈保存单元格，然后相应地添加或删除这些单元格。开始时把所有单元格定义为墙体，然后从一个随机位置推倒墙体。重复这个流程直到遍历完整个图。

### 9.3 为地下城和群岛实现可构造算法

游戏中用得最多的表示游戏世界（除了开放式的游戏世界）的数据结构之一是地下城。本节中，我们将用一种技术创建这两种结构。

### 准备工作

这项技术简单地使用一个网格图，就像基于深度优先搜索的迷宫构建器使用的网格图一样。然而，我们将尝试用另一种方式展示如何管理划分空间和存储大小。

### 操作步骤

我们需要创建两个不同的类：一个用于管理节点，另一个用于管理整个树和上层操作。
以这个节点文件开始：

- 1. 创建一个新类，命名为 DungeonNode2D:

```
using UnityEngine;
using System.Collections.Generic;

[System.Serializable]
public class DungeonNode2D
{
    // next steps
}
```

- 2. 定义成员变量：

```
public Rect area;
public Rect block;
public DungeonNode2D root;
public DungeonNode2D left;
public DungeonNode2D right;
protected int depth;
```

- 3. 实现用于初始化的构造函数：

```
public DungeonNode2D (Rect area, Dungeon2D root, int depth = 0)
{
    this.area = area;
    this.root = root;
    this.depth = depth;
    this.root.leaves.Add(this);
    if (!this.root.tree.ContainsKey(depth))
        this.root.tree.Add(depth, new List<DungeonNode2D> ());
    this.root.tree[depth].Add(this);
}
```

- 4. 实现用于分离节点的成员函数：

```
public void Split(Dungeon2D.Split splitCall)
{
    this.root.leaves.Remove(this);
    Rect[] areas = splitCall(area);
    if (areas == null)
        return;
    left = new DungeonNode2D(areas[0], root, depth + 1);
    right = new DungeonNode2D(areas[1], root, depth + 1);
}
```

- 5. 实现用于创建区块（可行走的空间或单元格）的成员函数：

```
public void CreateBlock()
{
}
```
