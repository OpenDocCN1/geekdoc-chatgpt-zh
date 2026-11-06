
## Hexapawn 代码示例

### 定义如何进行移动并基于此更新棋子

```
 # Define how to make a move
    def make_move(self, move):
        move = list(map(self.to_tuple, move.split(' ')))
        ind = self.player.pawns.index(move[0])
        self.player.pawns[ind] = move[1] 
```

```
 if move[1] in self.opponent.pawns:
            self.opponent.pawns.remove(move[1]) 
```

### 定义损失条件

定义损失条件。如果一名玩家在一行中获得 4，则对手输了：

```
 # Define what a loss looks like
    def loss_condition(self):
        return (any([i == self.opponent.goal_line
                for i, j in self.opponent.pawns])
                or (self.possible_moves() == []) ) 
```

### 使用 `loss_condition` 方法检查游戏是否完成

使用 `loss_condition` 方法检查游戏是否完成：

```
 # Check if the game is over
    def is_over(self):
        return self.loss_condition() 
```

### 打印当前状态

打印当前状态：

```
 # Show the current status 
    def show(self):
        f = lambda x: '1' if x in self.players[0].pawns else (
                '2' if x in self.players[1].pawns else '.') 
```

```
 print("\n".join([" ".join([f((i, j)) for j in 
          range(self.size[1])]) for i in range(self.size[0])])) 
```

### 定义 `main` 函数并从定义 `scoring` lambda 函数开始

定义 `main` 函数并从定义 `scoring` lambda 函数开始：

```
if __name__=='__main__':
    # Compute the score
    scoring = lambda game: -100 if game.loss_condition() else 0 
```

### 定义要使用的算法

定义要使用的算法。在这种情况下，我们将使用 `Negamax`，它可以预先计算 `12` 移动并为策略使用 `scoring` Lambda 函数：

```
 # Define the algorithm
    algorithm = Negamax(12, scoring) 
```

### 开始玩游戏

开始玩游戏：

```
 # Start the game
    game = GameController([AI_Player(algorithm), 
            AI_Player(algorithm)])
    game.play()
    print('\nPlayer', game.nopponent, 'wins after', game.nmove, 'turns') 
```

完整代码为文件 `hexapawn.py` 中提供的。这不是互动游戏。创建了两个机器人并使其相互对接。如果运行代码，最初将获得以下输出：

![](Images/51ddb5e58bd805afef4470921aa84ace.png)

图 7：Hexapawn 游戏的初始输出

如果您向下滚动，则会在结尾处看到以下内容：

![](Images/307b84b0d826648c3b17a43539ed3b8d.png)

图 8：Hexapawn 游戏最终输出

如我们所见，一名玩家赢得了比赛。

# 总结

在本章中，我们讨论了如何使用一种称为组合搜索的特殊类型的人工智能技术来构建游戏。我们学习了如何使用这些类型的搜索算法来有效地提出赢得比赛的策略。这些算法可用于为更复杂的游戏构建游戏机，并解决各种问题。我们讨论了组合搜索以及如何使用组合搜索来加快搜索过程。我们了解了 `Minimax` 和 `Alpha-Beta` 修剪。我们了解了 `Negamax` 算法是如何在实践中使用的。然后，我们使用这些算法来构建用于玩“最后的硬币站立”和“井字棋”的机器人。

我们学习了如何在 `Connect Four` 和 `Hexapawn` 中构建两个相互竞争的机器人。在下一章中，我们将学习语音识别并构建一个自动识别语音的系统。

# 14 构建语音识别器

> 原文：[https://gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/ai-py/14.md](https://gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/ai-py/14.md)

在本章中，我们将学习语音识别。我们将讨论如何处理语音信号，并学习如何可视化各种音频信号。通过利用各种技术来处理语音信号，我们将学习如何构建语音识别系统。

在本章结束时，您将了解更多有关：

*   处理语音信号
*   可视化音频信号
*   将音频信号转换到频域
*   产生音频信号
*   合成音调
*   提取语音特征
*   识别口语

我们将从讨论如何使用语音信号开始。

## 处理语音信号

语音识别是理解人类说出的单词的过程。使用麦克风捕获语音信号，系统尝试理解正在捕获的单词。语音识别广泛用于人机交互，智能手机，语音转录，生物识别系统，安全性等。

在分析语音信号之前，了解其本质非常重要。这些信号恰好是各种信号的复杂混合。语音的许多不同方面都会导致其复杂性。它们包括情感，口音，语言和噪音。

由于这种复杂性，很难定义一套可靠的规则来分析语音信号。相反，即使语音可以有很多变化，人类在理解语音方面也很出色。人类似乎相对容易做到这一点。为了使机器做到相同，我们需要帮助他们以与人类相同的方式理解语音。

研究人员致力于语音的各个方面和应用，例如理解口语，识别说话者是谁，识别情感以及识别口音。在本章中，我们将着重于理解口语。语音识别代表了人机交互领域的重要一步。如果我们想构建可以与人类互动的认知机器人，那么他们需要以自然语言与我们对话。这就是近年来自动语音识别成为许多研究人员关注的焦点的原因。让我们继续，看看如何处理第个语音信号并构建语音识别器。

## 可视化音频信号

让我们看看如何可视化音频信号。我们将学习如何从文件中读取音频信号并进行处理。这将帮助我们了解音频信号的结构。使用麦克风录制音频文件时，它们会采样实际的音频信号并存储数字化版本。真实的音频信号是连续的值波，这意味着我们无法按原样存储它们。我们需要以一定频率对信号进行采样并将其转换为离散的数值形式。

最常见的是，语音信号以 44,100 Hz 采样。这意味着语音信号的每一秒被分解成 44,100 个部分，并且这些时间戳中每个时间戳的值都存储在输出文件中。我们每 `1/44,100` 秒保存一次音频信号的值。在这种情况下，我们说音频信号的采样频率为 44,100 Hz。通过选择高采样频率，当人们听音频信号时，它似乎是连续的。让我们继续进行可视化音频信号。

创建一个新的 Python 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt
from scipy.io import wavfile 
```

使用 `wavefile.read` 方法读取输入的音频文件。它返回两个值–采样频率和音频信号：

```
# Read the audio file
sampling_freq, signal = wavfile.read('random_sound.wav') 
```

打印信号的形状，数据类型和音频信号的持续时间：

```
# Display the params
print('\nSignal shape:', signal.shape)
print('Datatype:', signal.dtype)
print('Signal duration:', round(signal.shape[0] / float(sampling_freq), 2), 'seconds') 
```

标准化信号：

```
# Normalize the signal
signal = signal / np.power(2, 15) 
```

从 `numpy` 数组中提取第一个 `50` 值进行绘图：

```
# Extract the first 50 values
signal = signal[:50] 
```

以秒为单位构造时间轴：

```
# Construct the time axis in milliseconds
time_axis = 1000 * np.arange(0, len(signal), 1) / float(sampling_freq) 
```

绘制音频信号：

```
# Plot the audio signal
plt.plot(time_axis, signal, color='black')
plt.xlabel('Time (milliseconds)')
plt.ylabel('Amplitude')
plt.title('Input audio signal')
plt.show() 
```

完整代码在文件 `audio_plotter.py` 中给出。如果运行代码，您将看到以下屏幕截图：

![](Images/3a2d332f412035ef78eafcb527d41ec7.png)

图 1：输入音频信号的可视化

前面的屏幕截图显示了输入音频信号的前 50 个样本。您将看到以下输出：

![](Images/006430c4daf6023ed860187d314ccd8b.png)

图 2：输入音频信号输出

上图中打印的输出显示了我们从符号 al 中提取的信息。

## 将音频信号转换到频域

为了分析音频信号，我们需要了解基本的频率成分。这使我们能够洞悉如何从该信号中提取有意义的信息。音频信号由频率，相位和幅度不同的正弦波混合而成。

如果我们剖析频率分量，我们可以识别很多特征。任何给定的音频信号的特征在于其在频谱中的分布。为了将时域信号转换为频域，我们需要使用数学工具，例如 `傅里叶变换`。如果您需要快速学习傅立叶变换，[请查看以下链接](http://www.thefouriertransform.com)。让我们看看如何将音频信号从时域转换到频域。

创建一个新的 Python 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt 
from scipy.io import wavfile 
```

使用 `wavefile.read` 方法读取输入的音频文件。它返回两个值–采样频率和音频信号：

```
# Read the audio file
sampling_freq, signal = wavfile.read('spoken_word.wav') 
```

标准化音频信号：

```
# Normalize the values
signal = signal / np.power(2, 15) 
```

提取信号的长度和一半长度：

```
# Extract the length of the audio signal
len_signal = len(signal) 
```

```
# Extract the half length
len_half = np.ceil((len_signal + 1) / 2.0).astype(np.int) 
```

对信号应用傅立叶变换：

```
# Apply Fourier transform 
freq_signal = np.fft.fft(signal) 
```

归一化频域信号并取平方：

```
# Normalization
freq_signal = abs(freq_signal[0:len_half]) / len_signal 
```

```
# Take the square
freq_signal **= 2 
```

调整偶数和奇数情况下的傅立叶变换信号：

```
# Extract the length of the frequency transformed signal
len_fts = len(freq_signal) 
```

```
# Adjust the signal for even and odd cases
if len_signal % 2:
    freq_signal[1:len_fts] *= 2 
```

```
else:
    freq_signal[1:len_fts-1] *= 2 
```

在 `dB` 中提取电源信号：

```
# Extract the power value in dB 
signal_power = 10 * np.log10(freq_signal) 
```

构建 `X` 轴，在这种情况下，是在 `kHz` 中测量的频率：

```
# Build the X axis
x_axis = np.arange(0, len_half, 1) * (sampling_freq / len_signal) / 1000.0 
```

绘制图：

```
# Plot the figure
plt.figure()
plt.plot(x_axis, signal_power, color='black')
plt.xlabel('Frequency (kHz)')
plt.ylabel('Signal power (dB)')
plt.show() 
```

完整代码在文件 `frequency_transformer.py` 中给出。如果运行代码，您将看到以下屏幕截图：

![](Images/0de33d79138ede276bb859815578b11c.png)

图 3：音频信号转换的可视化

前面的屏幕截图显示了信号在整个频谱上的强大程度。在这种情况下，信号的功率会以较高的频率下降。

## 生成音频信号

现在我们知道音频信号是如何工作的，让我们看看如何生成一个这样的信号。我们可以使用 `NumPy` 包生成各种音频信号。由于音频信号是 `正弦波` 的混合，我们可以使用它来生成具有一些预定义参数的音频信号。

创建一个新的 Python 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt
from scipy.io.wavfile import write 
```

定义输出音频文件的名称：

```
# Output file where the audio will be saved
output_file = 'generated_audio.wav' 
```

指定音频参数，例如持续时间，采样频率，音频频率，最小值和最大值：

```
# Specify audio parameters
duration = 4  # in seconds
sampling_freq = 44100  # in Hz
tone_freq = 784
min_val = -4 * np.pi
max_val = 4 * np.pi 
```

使用定义的参数生成音频信号：

```
# Generate the audio signal
t = np.linspace(min_val, max_val, duration * sampling_freq)
signal = np.sin(2 * np.pi * tone_freq * t) 
```

给信号增加一些噪声：

```
# Add some noise to the signal
noise = 0.5 * np.random.rand(duration * sampling_freq)
signal += noise 
```

归一化并缩放信号：

```
# Scale it to 16-bit integer values 
scaling_factor = np.power(2, 15) - 1
signal_normalized = signal / np.max(np.abs(signal))
signal_scaled = np.int16(signal_normalized * scaling_factor) 
```

将生成的音频信号保存在输出文件中：

```
# Save the audio signal in the output file 
write(output_file, sampling_freq, signal_scaled) 
```

提取第一个 `200` 值进行绘图：

```
# Extract the first 200 values from the audio signal 
signal = signal[:200] 
```

构造时间轴（以毫秒为单位）：

```
# Construct the time axis in milliseconds
time_axis = 1000 * np.arange(0, len(signal), 1) / float(sampling_freq) 
```

绘制音频信号：

```
# Plot the audio signal
plt.plot(time_axis, signal, color='black')
plt.xlabel('Time (milliseconds)')
plt.ylabel('Amplitude')
plt.title('Generated audio signal')
plt.show() 
```

完整代码在文件 `audio_generator.py` 中给出。如果运行代码，您将看到以下屏幕截图：

![](Images/12956b72968108bbc76ebeeca2609e1b.png)
