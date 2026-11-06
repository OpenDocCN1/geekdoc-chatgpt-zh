

![](Images/1a25788a2370682442a33910f374c822.png)

图 2：最后一枚硬币站立式游戏的最终输出

如我们所见，计算机赢得了游戏，因为用户拿起了最后一个硬币。

让我们来看看为另一个游戏`tf-tac-toe`构建机器人。

# 建立一个玩井字棋的机器人

井字棋（Nights and Crosss）是，也许是世界上最著名的游戏之一。让我们看看如何构建一个可以与用户对战的游戏。这是`easyAI`库中给出的井字棋秘籍的一个较小变体。

创建一个新的 Python 文件并导入以下包：

```
from easyAI import TwoPlayersGame, AI_Player, Negamax
from easyAI.Player import Human_Player
```

定义一个包含所有玩游戏方法的类。首先定义玩家和谁开始游戏：

```
class GameController(TwoPlayersGame):
    def __init__(self, players):
        # Define the players
        self.players = players
```

```
# Define who starts the game
        self.nplayer = 1
```

我们将使用 3×3 的木板，编号从 1 到 9：

```
# Define the board
    self.board = [0] * 9
```

定义一种方法来计算所有可能的移动：

```
# Define possible moves
    def possible_moves(self):
        return [a + 1 for a, b in enumerate(self.board) if b == 0]
```

定义一种在移动后更新板的方法：

```
# Make a move
    def make_move(self, move):
        self.board[int(move) - 1] = self.nplayer
```

定义一种方法来查看是否有人输了游戏。我们将检查某人是否连续三个：

```
# Does the opponent have three in a line?
    def loss_condition(self):
        possible_combinations = [[1,2,3], [4,5,6], [7,8,9],
            [1,4,7], [2,5,8], [3,6,9], [1,5,9], [3,5,7]]
```

```
return any([all([(self.board[i-1] == self.nopponent)
                for i in combination]) for combination in possible_combinations])
```

使用`loss_condition`方法检查游戏是否完成：

```
# Check if the game is over
    def is_over(self):
        return (self.possible_moves() == []) or self.loss_condition()
```

定义一种方法来显示当前进度：

```
# Show current position
    def show(self):
        print('\n'+'\n'.join([' '.join([['.', 'O', 'X'][self.board[3*j + i]]
                for i in range(3)]) for j in range(3)]))
```

使用`loss_condition`方法计算分数：

```
# Compute the score
    def scoring(self):
        return -100 if self.loss_condition() else 0
```

定义`main`函数并从定义算法开始。`Negamax`将用作此游戏的 AI 算法。可以预先指定算法应预先考虑的步骤数。在这种情况下，让我们选择`7`：

```
if __name__ == "__main__":
    # Define the algorithm
    algorithm = Negamax(7)
```

开始游戏：

```
# Start the game
  GameController([Human_Player(), AI_Player(algorithm)]).play()
```

完整代码在文件`tic_tac_toe.py`中给出。这是一个交互式游戏，您可以在计算机上玩。如果运行代码，最初将获得以下输出：

![](Images/d086f5efccdf1af80910a13b8f92aa16.png)

图 3：井字棋初始输出

如果向下滚动，将看到以下输出：

![](Images/d728b12a93990d0eea63354fea158393.png)

图 4：井字棋最终输出

如我们所见，游戏以平局结束。我们已经研究了可以与用户对抗的机器人。现在让我们建立两个机器人来对抗。这次在 Connect Four™ 中。

# 建立两个机器人来互相玩 Connect Four™

Connect Four™ 是，这是一款流行的两人游戏，以 Milton Bradley 商标出售。它也以其他名称（例如“连续四个”或“四个向上”）而闻名。在此游戏中，玩家轮流将光盘放入由六行七列组成的垂直网格中。目标是连续获取四张光盘。这是`easyAI`库中提供的“连接四个”秘籍的变体。让我们看看如何构建它。在此秘籍中，我们将创建两个可以相互对抗的机器人，而不是与计算机对抗。每个人将使用不同的算法来查看哪个获胜。

创建一个新的 Python 文件并导入以下包：

```
import numpy as np
from easyAI import TwoPlayersGame, Human_Player, AI_Player, \
        Negamax, SSS
```

定义一个类，其中包含玩游戏所需的所有方法：

```
class GameController(TwoPlayersGame):
    def __init__(self, players, board = None):
        # Define the players
        self.players = players
```

用六行七列定义板：

```
# Define the configuration of the board
        self.board = board if (board != None) else (
            np.array([[0 for i in range(7)] for j in range(6)]))
```

定义谁将开始游戏。在这种情况下，让玩家一个开始游戏：

```
# Define who starts the game
        self.nplayer = 1
```

定义位置：

```
# Define the positions
        self.pos_dir = np.array([[[i, 0], [0, 1]] for i in range(6)] +
                 [[[0, i], [1, 0]] for i in range(7)] +
                 [[[i, 0], [1, 1]] for i in range(1, 3)] +
                 [[[0, i], [1, 1]] for i in range(4)] +
                 [[[i, 6], [1, -1]] for i in range(1, 3)] +
                 [[[0, i], [1, -1]] for i in range(3, 7)])
```

定义方法以获取所有可能的移动：

```
# Define possible moves
    def possible_moves(self):
        return [i for i in range(7) if (self.board[:, i].min() == 0)]
```

定义一种方法来控制移动：

```
# Define how to make the move
    def make_move(self, column):
        line = np.argmin(self.board[:, column] != 0)
        self.board[line, column] = self.nplayer
```

定义一种显示当前状态的方法：

```
# Show the current status
    def show(self):
        print('\n' + '\n'.join(
                ['0 1 2 3 4 5 6', 13 * '-'] +
                [' '.join([['.', 'O', 'X'][self.board[5 - j][i]]
                for i in range(7)]) for j in range(6)]))
```

定义一种方法来计算损失的外观。每当有人连续赢得四个时，该玩家就会赢得比赛：

```
# Define what a loss_condition looks like
    def loss_condition(self):
        for pos, direction in self.pos_dir:
            streak = 0
            while (0 <= pos[0] <= 5) and (0 <= pos[1] <= 6):
                if self.board[pos[0], pos[1]] == self.nopponent:
                    streak += 1
                    if streak == 4:
                        return True
                else:
                    streak = 0
```

```
pos = pos + direction
```

```
return False
```

使用`loss_condition`方法检查游戏是否结束：

```
# Check if the game is over
    def is_over(self):
        return (self.board.min() > 0) or self.loss_condition()
```

计算分数：

```
# Compute the score
    def scoring(self):
        return -100 if self.loss_condition() else 0
```

定义`main`函数并从定义算法开始。然后，这两种算法将相互竞争。`Negamax`将用于第一个计算机玩家，`SSS*`算法将用于第二个计算机玩家。`SSS*`是一种搜索算法，它通过以最佳优先方式遍历树来进行状态空间搜索。两种方法都将事先考虑的匝数作为输入参数。在这种情况下，让我们同时使用`5`：

```
if __name__ == '__main__':
    # Define the algorithms that will be used
    algo_neg = Negamax(5)
    algo_sss = SSS(5)
```

开始玩游戏：

```
# Start the game
    game = GameController([AI_Player(algo_neg), AI_Player(algo_sss)])
    game.play()
```

打印结果：

```
# Print the result
    if game.loss_condition():
        print('\nPlayer', game.nopponent, 'wins.')
    else:
        print("\nIt's a draw.")
```

完整代码在文件`connect_four.py`中给出。这不是一个互动游戏。该代码将一种算法与另一种算法进行比较。`Negamax`算法是玩家 1，`SSS*`算法是玩家 2。

如果运行代码，最初将获得以下输出：

![](Images/c24e41b06cae9ea4abba13254c4f5f5c.png)

图 5：连接四个游戏的初始输出

如果向下滚动，则会在结尾处看到以下内容：

![](Images/0f438df56e4c481ca541ed224b74ce78.png)

图 6：连接四个游戏的最终输出

如我们所见，第二名玩家赢得了比赛。让我们再尝试一个游戏：`Hexapawn`。

# 建立两个机器人来互相对抗 Hexapawn

`Hexapawn`是一款两人游戏，大小为`N×M`。棋子存在于棋盘的两侧，目标是将棋子一直推进到棋盘的另一端。国际象棋的标准典当规则适用。这是`easyAI`库中提供的 Hexapawn 秘籍的变体。将创建两个机器人，并使其相互对峙。让我们创建代码。

创建一个新的 Python 文件并导入以下包：

```
from easyAI import TwoPlayersGame, AI_Player, \
        Human_Player, Negamax
```

定义一个类，其中包含控制游戏所需的所有方法。首先定义两侧的棋子数和棋盘的长度。创建一个包含位置的元组列表：

```
class GameController(TwoPlayersGame):
    def __init__(self, players, size = (4, 4)):
        self.size = size
        num_pawns, len_board = size
        p = [[(i, j) for j in range(len_board)] \
                for i in [0, num_pawns - 1]]
```

为每个玩家分配方向，目标和棋子：

```
for i, d, goal, pawns in [(0, 1, num_pawns - 1,
                p[0]), (1, -1, 0, p[1])]:
            players[i].direction = d
            players[i].goal_line = goal
            players[i].pawns = pawns
```

定义玩家并指定谁先开始：

```
# Define the players
        self.players = players
```

```
# Define who starts first
        self.nplayer = 1
```

定义用于识别棋盘上的位置（例如 B6 或 C7）的字母：

```
# Define the alphabets
        self.alphabets = 'ABCDEFGHIJ'
```

定义一个`lambda`函数将字符串转换为元组：

```
# Convert B4 to (1, 3)
        self.to_tuple = lambda s: (self.alphabets.index(s[0]),
                int(s[1:]) - 1)
```

定义`lambda`函数以将元组转换为字符串：

```
# Convert (1, 3) to B4
        self.to_string = lambda move: ' '.join([self.alphabets[
                move[i][0]] + str(move[i][1] + 1)
                for i in (0, 1)])
```

定义一种方法来计算可能的移动：

```
# Define the possible moves
    def possible_moves(self):
        moves = []
        opponent_pawns = self.opponent.pawns
        d = self.player.direction
```

如果在某个位置没有找到对手的棋子，那么这是一个有效的举动：

```
for i, j in self.player.pawns:
            if (i + d, j) not in opponent_pawns:
                moves.append(((i, j), (i + d, j)))
```

```
if (i + d, j + 1) in opponent_pawns:
                moves.append(((i, j), (i + d, j + 1)))
```

```
if (i + d, j - 1) in opponent_pawns:
                moves.append(((i, j), (i + d, j - 1)))
```

```
return list(map(self.to_string, [(i, j) for i, j in moves]))
```
