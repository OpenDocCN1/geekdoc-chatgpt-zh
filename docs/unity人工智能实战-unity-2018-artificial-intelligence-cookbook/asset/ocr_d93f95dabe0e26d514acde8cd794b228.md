```
int currentDepth,
ref Move bestMove,
float alpha,
float beta)
{
    // 这里是下面的步骤
}
```

# 2. 验证基线条件:

```
if (board.IsGameOver() || currentDepth == maxDepth)
    return board.Evaluate(player);
```

# 3. 设置初始值:

```
bestMove = null;
float bestScore = Mathf.NegativeInfinity;
```

# 4. 遍历所有可用的行动并返回最佳得分:

```
foreach (Move m in board.GetMoves())
{
    // 这里是下面的步骤
}
return bestScore;
```

# 5. 从当前行动创建一个新的博弈状态:

```
Board b = board.MakeMove(m);
```

# 6. 设置用于递归的值:

```
float recursedScore;
Move currentMove = null;
int cd = currentDepth + 1;
float max = Mathf.Max(alpha, bestScore);
```

# 7. 开始递归:

```
recursedScore = ABNegamax(b, player, maxDepth, cd, ref currentMove,
-beta, max);
```

# 8. 如有必要，设置当前得分并更新最佳得分和行动。另外，必要时停止递归:

```
float currentScore = -recursedScore;
if (currentScore > bestScore)
{
    bestScore = currentScore;
    bestMove = m;
    if (bestScore >= beta)
        return bestScore;
}
```

# 运行原理

因为我们知道 AB Negamax 算法的基本思想，所以具体说一下搜索策略。这里有两个