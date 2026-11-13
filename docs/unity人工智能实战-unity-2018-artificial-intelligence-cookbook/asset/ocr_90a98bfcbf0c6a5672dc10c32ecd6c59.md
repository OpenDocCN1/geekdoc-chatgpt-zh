```
foreach (int mY in moveY)
{
    foreach (int mX in moveX)
    {
        // 这里是下面的步骤
    }
}
return moves;
```

+   28. 创建用于检测走法和前进的变量：

```
int nowX = x + mX;
int nowY = y + mY;
```

+   29. 创建一个循环，用于计算到达棋盘边界时要去的方向：

```
while (IsMoveInBounds(nowX, nowY, ref board))
{
    // 这里是下面的步骤
}
```

+   30. 获取位置的棋子的引用：

```
PieceDraughts p = board[nowY, nowX];
```

+   31. 如果是同色的棋子，则不再继续向前：

```
if (p != null && p.color == color)
    break;
```

+   32. 定义一个变量，用于创建新的可用走法：

```
MoveDraughts m = new MoveDraughts();
m.piece = this;
```

+   33. 如果这个位置可以走，则创建一个简单的走法：

```
if (p == null)
{
    m.x = nowX;
    m.y = nowY;
}
```

+   34. 否则，检查棋子是否会被杀死，并相应地修改走法：

```
else
{
    int hopX = nowX + mX;
    int hopY = nowY + mY;
    if (!IsMoveInBounds(hopX, hopY, ref board))
        break;
    m.success = true;
    m.x = hopX;
    m.y = hopY;
    m.removeX = nowX;
    m.removeY = nowY;
}
```