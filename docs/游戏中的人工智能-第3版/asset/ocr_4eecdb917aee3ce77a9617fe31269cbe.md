bound applied to Trees, UCT）权重，每个可能着法的 UCT 权重（记为 \(u_i\)）可按以下公式计算:

\(u_i = \frac{w_i}{n_i} + k \sqrt{\frac{\ln N}{n_i}}\)

其中，\(w_i\) 是该节点的获胜数，\(p_i\) 是推演（Playout）的次数，\(N\) 是所有候选节点的总推演次数，\(k\) 是控制探索量的常数。

在遍历的每个步骤中，将选择具有最大 \(u_i\) 值的节点。

# 3. 伪代码

以下是用于执行蒙特卡洛树搜索迭代的伪代码：

```
function mcts(board: Board):
    # 1. 选择
    current: Board = board
    moveSequence: Board[] = [current]
    while current.fullyExplored():
        move: Move = current.selectMove()
        current = current.makeMove(move)
        moveSequence.push(current)

    # 2. 扩展
    move = current.chooseUnexploredMove()
    current = current.makeMove(move)
    moveSequence.push(current)

    # 3. 模拟
    winner: id = playout(current)

    # 4. 反向传播
    for current in moveSequence:
        if winner == current.currentPlayer:
            current.wins += 1
            current.playouts += 1
```

函数 playout 从给定的棋盘局面开始对游戏执行纯粹的随机推演，并返回结果。
如果使用蒙特卡洛树搜索来返回 AI 所要采取的着法（移动），则可以使用以下简单
的驱动程序：

```
function mctsDriver(board: Board, thinkingTime: float) -> Move:
    deadline = time() + thinkingTime
```