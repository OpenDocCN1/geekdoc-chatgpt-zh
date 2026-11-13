这种方法使用一个国际象棋棋盘（8 x 8 大小），以及对应的棋子种类的数量（12 种）。然而，可以简单地通过参数化来改变这些值以便让我们可以有不同大小的棋盘。

# 准备工作

首先，我们为这种特殊情况创建一种新的移动类型，称为 MoveDraughts:

```
using UnityEngine;
using System.Collections;

public class MoveDraughts : Move
{
    public PieceDraughts piece;
    public int x;
    public int y;
    public bool success;
    public int removeX;
    public int removeY;
}
```

这个数据结构存储要移动的棋子，如果走子是一次成功的杀敌则储存新的 x 和 y 坐标，以及要被移除的棋子的位置。

# 操作步骤

我们要分别实现两个核心类，用于分别模拟棋子和棋盘。这是一个较长的过程，所以请仔细阅读：

+   1. 创建一个新文件，命名为 PieceDraughts.cs，并添加以下代码：

```
using UnityEngine;
using System.Collections;
using System.Collections.Generic;
```

+   2. 添加 PieceColor 数据类型：

```
public enum PieceColor
{
    WHITE,
    BLACK
};
```

+   3. 添加 PieceType 数据 enum:

```
public enum PieceType
{
    MAN,
    KING
};
```

+   4. 开始构建 PieceDraughts 类：

```
public class PieceDraughts : MonoBehaviour
{
```