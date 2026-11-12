# 1. 伪代码

test 函数可以通过以下方式实现：

```
function test(board: Board,
    maxDepth: int,
    currentDepth: int,
    gamma: float) -> (float, Move):
    if currentDepth > lowestDepth:
        lowestDepth = currentDepth

    # 从置换表中查找条目
    entry: TableEntry = table.getEntry(board.hashValue())

    if entry and entry.depth > maxDepth - currentDepth:
        # 已存储局面的先期输出
        if entry.minScore > gamma:
            return entry.minScore, entry.bestMove
        else if entry.maxScore < gamma:
            return entry.maxScore, entry.bestMove
        else:
            # 需要创建条目
            entry.hashValue = board.hashValue()
            entry.depth = maxDepth - currentDepth
            entry.minScore = -INFINITY
            entry.maxScore = INFINITY

    # 现在有了条目，就可以继续使用文本
    # 检查是否已经完成递归
    if board.isGameOver() or currentDepth == maxDepth:
        entry.minScore = entry.maxScore = board.evaluate()
        table.storeEntry(entry)
        return entry.minScore, null

    # 现在进入冒泡模式
    bestMove: Move = null
    bestScore: float = -INFINITY
    for move in board.getMoves():
        newBoard: Board = board.makeMove(move)
        # 递归
        recursedScore, currentMove = test(
            newBoard, maxDepth, currentDepth + 1, -gamma)
```