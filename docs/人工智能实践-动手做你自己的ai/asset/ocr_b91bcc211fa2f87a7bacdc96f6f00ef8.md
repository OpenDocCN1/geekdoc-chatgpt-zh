![](bbox=[0.28, 0.07, 0.68, 0.3])

少不会小于4，因此，u1，1 的取值不会对 u0，0 造成任何影响。在此情况下，直接放弃 u1，1 及其子结点是非常合理的选择：对于剩下的 u2，4 子树，我们完全不必再费时去搜索它了。

现在再来把这个例子进行一定的抽象，从而得出Alpha-Beta 剪枝的具体算法。对于每一个圆形节点（极大玩家节点），保存当前已知子树的最大值，记为α；同理，对于每一个方形节点，保存当前已知子树的最小值，记作β。当出现 α≥β 的时候，由以上的论证可知，可以完全放弃当前节点，不再对剩下的子树进行搜索。此处我们先看看其代码，之后再讨论Alpha-Beta 剪枝的运作方式。

```
def alphabeta(board, depth, alpha, beta, player_id):
    # 达到最大深度，或已经是终局局面，直接估值
    if depth == 0 or is_terminated(board):
        return evaluate(board, player_id)

    # 双方的id分别是0和1
    opponent_id = 1 - player_id

    # 极大玩家部分
    if is_maximizing_player(player_id):
        score = -float('inf')
        for move in get_valid_moves(board, player_id):
            new_board = drop(board, move)
            score = max(score, alphabeta(new_board, depth - 1, alpha, beta, opponent_id))

        # 有得分更高的子树，更新alpha
        if alpha < score:
            alpha = score
        # 出现alpha>=beta，直接剪枝
        if alpha >= beta:
```