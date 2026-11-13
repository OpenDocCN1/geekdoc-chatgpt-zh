
## 3. 实现把一组行动转换成字符串键值的静态函数：

```
public static string ArrToStrKey(ref T[] actions)
{
    StringBuilder builder = new StringBuilder();
    foreach (T a in actions)
    {
        builder.Append(a.ToString());
    }
    return builder.ToString();
}
```

## 4. 定义用于记录一个序列的集合的函数：

```
public void RegisterSequence(T[] actions)
{
    string key = ArrToStrKey(ref actions);
    T val = actions[nValue - 1];
    if (!data.ContainsKey(key))
    {
        data[key] = new KeyDataRecord<T>();
    }
    KeyDataRecord<T> kdr = data[key];
    if (kdr.counts.ContainsKey(val))
    {
        kdr.counts[val] = 0;
    }
    kdr.counts[val]++;
    kdr.total++;
}
```

## 5. 实现用于计算预测要采取的最佳行动的函数：

```
public T GetMostLikely(T[] actions)
{
    string key = ArrToStrKey(ref actions);
    KeyDataRecord<T> kdr = data[key];
    int highestVal = 0;
    T bestAction = default(T);
    foreach (KeyValuePair<T,int> kvp in kdr.counts)
    {
        if (kvp.Value > highestVal)
        {
            bestAction = kvp.Key;
            highestVal = kvp.Value;
        }
    }
    return bestAction;
}
```

## 运行原理

预测器根据搜索窗口的大小记录了一组行动（记录这些行动是为了做预测）并给它们分配一个结果值。例如，有一个大小是 3 的搜索窗口，前 3 个就会作为一个键值保存，以预测接下来的第 4 个行动。

根据之前的行动，预测函数计算出某个行动有多大可能会是接下来的行动。记录的行动越多，则预测会越准确（有一些限制）。

## 延伸阅读

要注意考虑类型 T 的对象必须以一种可接受的方式重写 ToString 函数和 Equals 函数，以便让它作为内部字典中的索引正确运行。

## 8.3 改进预测器：分层的 N 元语法

N 元语法预测器可以通过一个管理器进行改进，这个管理器还带有几个范围从 1 到 n 的预测器，然后通过比较几个预测器中的最佳猜想后获得最有可能的行动。

### 准备工作

我们需要在实现分层 N 元语法预测器之前先做一些调整。把下面的成员函数添加到 NGramPredictor 类中：

```
public int GetActionsNum(ref T[] actions)
{
    string key = ArrToStrKey(ref actions);
    if (!data.ContainsKey(key))
        return 0;
    return data[key].total;
}
```

### 操作步骤

与 N 元语法预测器一样，构建分层版本的 N 元语法预测器也需要几个步骤：

- 1. 创建新类：

```
using System;
using System.Collections;
using System.Text;

public class HierarchicalNGramP<T>
{
    public int threshold;
    public NGramPredictor<T>[] predictors;
    private int nValue;
}
```

- 2. 实现用于初始化成员值的构造函数：

```
public HierarchicalNGramP(int windowSize)
{
    nValue = windowSize + 1;
    predictors = new NGramPredictor<T>[nValue];
    int i;
    for (i = 0; i < nValue; i++)
        predictors[i] = new NGramPredictor<T>(i + 1);
}
```

- 3. 定义用于记录一个序列的函数，就像 8.2 节中的前身那样：

```
public void RegisterSequence(T[] actions)
{
    int i;
    for (i = 0; i < nValue; i++)
    {
        T[] subactions = new T[i+1];
        Array.Copy(actions, nValue - i - 1, subactions, 0, i+1);
        predictors[i].RegisterSequence(subactions);
    }
}
```

- 4. 实现用于计算预测值的函数：

```
public T GetMostLikely(T[] actions)
{
    int i;
    T bestAction = default(T);
    for (i = 0; i < nValue; i++)
    {
        NGramPredictor<T> p;
        p = predictors[nValue - i - 1];
        T[] subactions = new T[i + 1];
        Array.Copy(actions, nValue - i - 1, subactions, 0, i + 1);
        int numActions = p.GetActionsNum(ref actions);
        if (numActions > threshold)
            bestAction = p.GetMostLikely(actions);
    }
    return bestAction;
}
```

### 运行原理

分层 N 元语法预测器的原理几乎与 8.2 节中的一样，不同之处在于它有一组预测器，并且使用其子预测器计算出每个主函数的值。每个预测器通过分解集合中可用的行动，然后记录序列或者找出最可能的潜在行动，再把这些行动填充到子预测器中。

## 8.4 学习使用朴素贝叶斯分类器

人类都不一定能很容易地发现两个集合之间的关系。解决这个问题的一种方式是把值的一个集合进行分类后再试一次，而这就是分类算法派上用场的地方。

朴素贝叶斯分类器是用于给问题实例设定标签的预测算法，分类器运用概率和贝叶斯理论，在要进行分析的变量之间有强独立性假设。贝叶斯分类器的一个重要优点是可扩展性。

### 准备工作

因为构建一个通用的分类器很难，所以我们假设输入值是用正和负做标签的样本数据。

所以，第一件事是定义标签，分类器使用名为 NBCLabel 的枚举数据结构贴标签：

```
public enum NBCLabel
{
    POSITIVE,
    NEGATIVE
}
```

### 操作步骤

我们要构建的分类器只需要 5 步：

- 1. 创建类及其成员变量：

```
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public class NaiveBayesClassifier : MonoBehaviour
{
    public int numAttributes;
    public int numExamplesPositive;
    public int numExamplesNegative;

    public List<bool> attrCountPositive;
    public List<bool> attrCountNegative;
}
```

- 2. 定义 Awake 方法，用于初始化：

```
void Awake()
{
    attrCountPositive = new List<bool>();
    attrCountNegative = new List<bool>();
}
```

- 3. 实现用于更新分类器的函数：

```
public void UpdateClassifier(bool[] attributes, NBCLabel label)
{
    if (label == NBCLabel.POSITIVE)
    {
        numExamplesPositive++;
        attrCountPositive.AddRange(attributes);
    }
    else
    {
        numExamplesNegative++;
        attrCountNegative.AddRange(attributes);
    }
}
```

- 4. 定义用于计算朴素（Naïve）概率的函数：

```
public float NaiveProbabilities(
    ref bool[] attributes,
    bool[] counts,
    float m,
    float n)
{
    float prior = m / (m + n);
    float p = 1f;
    int i = 0;
    for (i = 0; i < numAttributes; i++)
    {
        p /= m;
        if (attributes[i] == true)
            p *= counts[i].GetHashCode();
        else
            p *= m - counts[i].GetHashCode();
    }
    return prior * p;
}
```

- 5. 实现用于预测的函数：

```
public bool Predict(bool[] attributes)
{
    float nep = numExamplesPositive;
    float nen = numExamplesNegative;
    float x = NaiveProbabilities(ref attributes,
    attrCountPositive.ToArray(), nep, nen);
    float y = NaiveProbabilities(ref attributes,
    attrCountNegative.ToArray(), nen, nep);
    if (x >= y)
        return true;
    return false;
}
```

### 运行原理

UpdateClassifier 函数取得样本的输入值并保存。这是第一个被调用的函数。 NaiveProbabilities 函数是负责计算预测函数工作的概率的函数。最后，Predict 函数 是第二个被我们调用的函数，目的是取得分类结果。

## 8.5 实现强化学习

假设我们需要设计出一个敌人，随着玩家在游戏中的剧情进展以及他们的模式发生变化时，这个敌人需要采取不同的行动，或者需要设计一款游戏，游戏中训练不同类型的有自由意志的宠物。 对于这些类型的任务，有一系列基于经验的建模学习技术。其中一个算法是 Q-learning，在本节中将会实现它。

### 准备工作

在深入算法之前，需要实现几个数据结构，一个用于游戏状态的结构，另一个用于游戏行动，还有一个类用于定义问题的实例。可以把这几个数据结构和类放在同一个文件中。

下面是一个数据结构示例，用于定义游戏状态：

```
public struct GameState
{
    // TODO
    // 在这里定义你的状态
}
```

下面是一个数据结构示例，用于定义游戏行动：

```
public struct GameAction
{
    // TODO
    // 在这里定义你的行动
}
```

最后，我们要构建用于定义一个问题实例的数据类型：

- 1. 创建文件和类：

```
public class ReinforcementProblem
{
}
```

- 2. 定义一个用于获取随机状态的虚函数。函数逻辑取决于我们正在开发的游戏类型，就游戏的当前状态而言，我们倾向于使用随机状态：

```
public virtual GameState GetRandomState()
{
    // TODO
    // 在这里定义你的行为
    return new GameState();
}
```

- 3. 定义一个虚函数，用于根据游戏状态获取所有可用的行动：

```
public virtual GameAction[] GetAvailableActions(GameState s)
{
    // TODO
    // 定义你的行为
    return new GameAction[0];
}
```

- 4. 定义一个虚函数，用于执行行动，然后获取作为结果的状态和奖励：

```
public virtual GameState TakeAction(
    GameState s,
    GameAction a,
    ref float reward)
{
    // TODO
    // 定义你的行为
    reward = 0f;
    return new GameState();
}
```

### 操作步骤

我们要实现两个类：第一个类把值保存在一个字典中，用于学习；第二个类实际上负责实现 Q-learning 算法。用下面的步骤创建这两个类：

- 1. 创建 QValueStore 类：

```
using UnityEngine;
using System.Collections.Generic;

public class QValueStore : MonoBehaviour
{
    private Dictionary<GameState, Dictionary<GameAction, float>> store;
}
```

- 2. 实现构造函数：

```
public QValueStore()
{
    store = new Dictionary<GameState, Dictionary<GameAction, float>>();
}
```

- 3. 定义用于在一个游戏状态中执行某个行动后获取结果值的函数。小心地操作这个函数，要考虑到行动不能在某个特别的状态下被执行：

```
public virtual float GetQValue(GameState s, GameAction a)
{
    // TODO: 你的行为代码放在这里
    return 0f;
}
```

- 4. 实现函数，用于获取在某个状态下要执行的最佳行动：

```
public virtual GameAction GetBestAction(GameState s)
{
    // TODO: 你的行为放在这里
    return new GameAction();
}
```

- 5. 实现函数：

```
public void StoreQValue(
    GameState s,
    GameAction a,
    float val)
{
    if (!store.ContainsKey(s))
    {
        Dictionary<GameAction, float> d;
        d = new Dictionary<GameAction, float>();
        store.Add(s, d);
    }
    if (!store[s].ContainsKey(a))
    {
        store[s].Add(a, 0f);
    }
    store[s][a] = val;
}
```

- 6. 继续创建 QLearning 类，用于运行算法：

```
using UnityEngine;
using System.Collections;

public class QLearning : MonoBehaviour
{
    public QValueStore store;
}
```

- 7. 定义函数，用于从一个集合中获取随机行动：

```
private GameAction GetRandomAction(GameAction[] actions)
{
    int n = actions.Length;
    return actions[Random.Range(0, n)];
}
```

- 8. 实现学习函数，建议分成几个步骤。从定义开始，要注意这是一个协程：

```
public IEnumerator Learn(
    ReinforcementProblem problem,
    int numIterations,
    float alpha,
    float gamma,
    float rho,
    float nu)
{
    // 下面的步骤
}
```

- 9. 验证保存列表是否已经初始化：

```
if (store == null)
    yield break;
```

- 10. 获取一个随机状态：

```
GameState state = problem.GetRandomState();
for (int i = 0; i < numIterations; i++)
{
    // 下面的步骤
}
```

- 11. 返回 null，目的是让当前帧继续运行：

```
yield return null;
```

- 12. 验证随机值的有效性：

```
if (Random.value < nu)
    state = problem.GetRandomState();
```

- 13. 从当前游戏状态获取可用的行动：
