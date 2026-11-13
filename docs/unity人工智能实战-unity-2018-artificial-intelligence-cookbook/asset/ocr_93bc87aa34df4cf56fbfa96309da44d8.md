所以，第一件事是定义标签，分类器使用名为 NBCLabel 的枚举数据结构贴标签：

```
public enum NBCLabel
{
    POSITIVE,
    NEGATIVE
}
```

# 操作步骤

我们要构建的分类器只需要 5 步：

+   1. 创建类及其成员变量：

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

+   2. 定义 Awake 方法，用于初始化：

```
void Awake()
{
    attrCountPositive = new List<bool>();
    attrCountNegative = new List<bool>();
}
```

+   3. 实现用于更新分类器的函数：

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

+   4. 定义用于计算朴素（Naïve）概率的函数：

```
public float NaiveProbabilities(
    ref bool[] attributes,
```