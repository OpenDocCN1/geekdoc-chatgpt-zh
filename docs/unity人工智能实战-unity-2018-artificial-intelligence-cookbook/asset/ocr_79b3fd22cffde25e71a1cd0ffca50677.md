```
bool[] counts,
float m,
float n
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

# 5. 实现用于预测的函数：

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

# 运行原理

UpdateClassifier 函数取得样本的输入值并保存。这是第一个被调用的函数。 NaiveProbabilities 函数是负责计算预测函数工作的概率的函数。最后，Predict 函数 是第二个被我们调用的函数，目的是取得分类结果。

# 8.5 实现强化学习

假设我们需要设计出一个敌人，随着玩家在游戏中的剧情进展以及他们的模式发生变化时，这个敌人需要采取不同的行动，或者需要设计一款游戏，游戏中训练不同类型的有自由意志的宠物。 对于这些类型的任务，有一系列基于经验的建模学习技术。其中一个算法是 Q-learning，在本节中将会实现它。

# 准备工作

在深入算法之前，需要实现几个数据结构，一个用于游戏状态的结构，另一个用于游