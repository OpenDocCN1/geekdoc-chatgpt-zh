def scoring(self):

        return -100 if self.loss_condition() else 0

[PRE0]

if __name__ == '__main__':

    # 初始化使用的搜索算法
    algo_neg = Negamax(5)

    algo_sss = SSS(5)

[PRE1]

# 进入对局
    game = GameController([AI_Player(algo_neg), AI_Player(algo_sss)])

    game.play()

[PRE2]

# 输出最终结果
    if game.loss_condition():

        print('\nPlayer', game.nopponent, 'wins.')

    else:

        print("\nIt's a draw.")

[PRE3]

from easyAI import TwoPlayersGame, AI_Player, \
        Human_Player, Negamax

[PRE4]

class GameController(TwoPlayersGame):

    def __init__(self, players, size = (4, 4)):

        self.size = size

        num_pawns, len_board = size

        p = [[(i, j) for j in range(len_board)] \
                for i in [0, num_pawns - 1]]

[PRE5]

for i, d, goal, pawns in [(0, 1, num_pawns - 1,
                p[0]), (1, -1, 0, p[1])]:

            players[i].direction = d

            players[i].goal_line = goal

            players[i].pawns = pawns

[PRE6]

# 绑定玩家实例
        self.players = players

[PRE7]

# 指定先手编号
        self.nplayer = 1

[PRE8]

# 设置坐标字母表
        self.alphabets = 'ABCDEFGHIJ'

[PRE9]

# 将棋盘坐标转为行列索引
        self.to_tuple = lambda s: (self.alphabets.index(s[0]),
                int(s[1:]) - 1)

[PRE10]

# 将行列索引转为棋盘坐标
        self.to_string = lambda move: ' '.join([self.alphabets[
                move[i][0]] + str(move[i][1] + 1)
                for i in (0, 1)])

[PRE11]

# 生成当前可走步
    def possible_moves(self):

        moves = []

        opponent_pawns = self.opponent.pawns

        d = self.player.direction

[PRE12]

for i, j in self.player.pawns:

            if (i + d, j) not in opponent_pawns:

                moves.append(((i, j), (i + d, j)))

[PRE13]

if (i + d, j + 1) in opponent_pawns:

                moves.append(((i, j), (i + d, j + 1)))

[PRE14]

if (i + d, j - 1) in opponent_pawns:

                moves.append(((i, j), (i + d, j - 1)))

[PRE15]

return list(map(self.to_string, [(i, j) for i, j in moves]))

[PRE16]

 # 执行落子操作
    def make_move(self, move):

        move = list(map(self.to_tuple, move.split(' ')))

        ind = self.player.pawns.index(move[0])

        self.player.pawns[ind] = move[1] 

[PRE17]

 if move[1] in self.opponent.pawns:

            self.opponent.pawns.remove(move[1]) 

[PRE18]

 # 判断失败条件
    def loss_condition(self):

        return (any([i == self.opponent.goal_line

                for i, j in self.opponent.pawns])

                or (self.possible_moves() == []) ) 

[PRE19]

 # 检查对局是否结束
    def is_over(self):

        return self.loss_condition() 

[PRE20]

 # 渲染当前棋面
    def show(self):

        f = lambda x: '1' if x in self.players[0].pawns else (

                '2' if x in self.players[1].pawns else '.') 

[PRE21]

 print("\n".join([" ".join([f((i, j)) for j in 

          range(self.size[1])]) for i in range(self.size[0])])) 

[PRE22]

if __name__=='__main__':

    # 设定评分函数
    scoring = lambda game: -100 if game.loss_condition() else 0 

[PRE23]

 # 配置搜索策略
    algorithm = Negamax(12, scoring) 

[PRE24]

 # 启动对局
    game = GameController([AI_Player(algorithm), 

            AI_Player(algorithm)])

    game.play()

    print('\nPlayer', game.nopponent, 'wins after', game.nmove, 'turns') 

[PRE25]

import numpy as np

import matplotlib.pyplot as plt

from scipy.io import wavfile 

[PRE26]

# 加载音频文件
sampling_freq, signal = wavfile.read('random_sound.wav') 

[PRE27]

# 打印信号基本信息
print('\nSignal shape:', signal.shape)

print('Datatype:', signal.dtype)

print('Signal duration:', round(signal.shape[0] / float(sampling_freq), 2), 'seconds') 

[PRE28]

# 将信号归一化
signal = signal / np.power(2, 15) 

[PRE29]

# 截取前 50 个采样点
signal = signal[:50] 

[PRE30]

# 构建毫秒级时间轴
time_axis = 1000 * np.arange(0, len(signal), 1) / float(sampling_freq) 

[PRE31]

# 绘制波形图
plt.plot(time_axis, signal, color='black')

plt.xlabel('Time (milliseconds)')

plt.ylabel('Amplitude')

plt.title('Input audio signal')

plt.show() 

[PRE32]