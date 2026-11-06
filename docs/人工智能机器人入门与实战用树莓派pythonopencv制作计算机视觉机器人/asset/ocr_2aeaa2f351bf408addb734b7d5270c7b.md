```
if not player.get_busy():
    break
playaudio("/home/pi/audios/1.wav")
```

不过，若要在机器狗的程序中插入音频的播放，由于程序需要等待音频播放完毕，它将阻碍我们其他程序内容的运行。

我们知道，以 Arduino 为例的单片机通常只能在同一时间运行一个程序，但 Python 程序并没有这个限制，它可以允许在同一时间运行多个程序。Python 中可以方便地在程序中创建“子线程”，让程序的一部分在后台同步执行。这种方法被称为“多线程”。

Python 中创建多线程存在多种方法，在 Python 3 中一种较为简单的方法是引入 threading 包中的 Thread 进行创建。

为了便于理解，我们先举一个简单的例子。假设小明正在吃草莓，他每 5 秒钟吃一个草莓，则可以写一个这样的程序：

```
import time
while True:
    print('吃草莓')
    time.sleep(5)
```

但是该同学热爱学习，他在吃草莓时也在背单词，他每 20 秒背一个单词。这样，我们可以将背单词作为吃草莓的子线程同步进行。

子线程应执行的内容需要放置在一个自定义函数中。我们可以设定一个名为 word 的自定义函数，放入背单词的过程：

```
def word():
    while True:
        print('背单词')
        time.sleep(20)
```

子线程可以通过 threading 包中的 Thread 进行创建，并以刚才设定的自定义函数为它的 target 参数。其写法如下：

```
from threading import Thread
t = Thread(target = word)
t.start()
```

这里，我们创建了一个名为 t 的子线程，它的执行内容在自定义函数 word 中。当需要开启线程时，只需要对线程调用 start 函数即可。完整的程序写法如下。

```
from threading import Thread
import time
def word():
    while True:
        print('背单词')
        time.sleep(20)
t = Thread(target = word)
t.start()
```