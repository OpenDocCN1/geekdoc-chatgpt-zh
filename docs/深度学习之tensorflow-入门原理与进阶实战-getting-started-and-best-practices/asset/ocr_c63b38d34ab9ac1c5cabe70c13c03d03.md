```
68 transcript_len = np.asarray(transcript_len)
69 return audio, audio_len, transcript, transcript_len
```

这部分代码遍历所有音频文件及文本，将音频调用 audiofile_to_input_vector 转成 MFCC,文本调用 get_ch_lable_v 函数将文本转成向量。所以接着看 audiofile_to_input_vector 的实现。

在 audiofile_to_input_vector 中先将其转化为 MFCC 特征码，例如第一个文件会被转成 (277, 26) 数组，代表着 277 个时间序列，每个序列的特征值是 26 个。

> 注意：这里有个小技巧，因为使用了双向循环神经网络，它的输出包含正、反向的结果，相当于每一个时间序列都扩大了一倍，所以为了保证总时序不变，使用 orig_inputs = orig_inputs[::2]对 orig_inputs 每隔一行进行一次取样。这样被忽略的那个序列可以用后文中反向 RNN 生成的输出来代替，维持了总的序列长度。

接着会扩展这 26 个特征值，将其扩展成：前 9 个时间序列 MFCC+当前 MFCC+后 9 个时间序列。比如第 2 个序列的前面只有一个序列不够 9 个，这时就要为其补 0，将它凑够 9 个。同理对于取不到前 9、后 9 时序的序列都做补 0 操作。这样数据就被扩成了 (139, 494) 。最后再将其进行标准化（减去均值然后再除以方差）处理，这是为了在训练中效果更好。代码如下。

```
代码9-24 yuyinutils (续)
......
def audiofile_to_input_vector(audio_filename, numcep, numcontext):

    # 加载 wav 文件
    fs, audio = wav.read(audio_filename)

    # 获得mfcc coefficients
    orig_inputs = mfcc(audio, samplerate=fs, numcep=numcep)
    orig_inputs = orig_inputs[::2]        #(139, 26)

    train_inputs = np.array([], np.float32)
    train_inputs.resize((orig_inputs.shape[0], numcep + 2 * numcep * numcontext))

    empty_mfcc = np.array([])
    empty_mfcc.resize((numcep))

    # 准备输入数据。输入数据的格式由三部分安装顺序拼接而成，分为当前样本的前 9 个序列样本，当前样本序列、后 9 个序列样本
    time_slices = range(train_inputs.shape[0])     #139 个切片
    context_past_min = time_slices[0] + numcontext
    context_future_max = time_slices[-1] - numcontext# [9,1,2,..., 137,129]
    for time_slice in time_slices:
        # 前 9 个补 0, mfcc features
        need_empty_past = max(0, (context_past_min - time_slice))
```