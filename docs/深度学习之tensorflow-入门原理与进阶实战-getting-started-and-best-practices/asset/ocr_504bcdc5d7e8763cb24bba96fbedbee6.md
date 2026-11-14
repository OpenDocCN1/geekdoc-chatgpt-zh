工具 python_speech_features 的代码包来实现，现在一起来安装该代码包。

在计算机联网的状态下，打开“开始”菜单，在“运行”框里输入 cmd，调出控制台窗口，输入如下命令：

```
pip install python_speech_features
```

python_speech_features 工具就会自动安装了。

# 5. 提取音频数据MFCC特征

对于 WAV 音频的样本，通过 MFCC 转换之后，在函数 get_audio_and_transcriptch 中将数据存储为时间（列）和频率特征系数（行）的矩阵，其代码如下。

```
代码9-24 yuyinutils（续）
34 import numpy as np
35
36 from python_speech_features import mfcc #需要使用 pip install 命令来额外安装
37 import scipy.io.wavfile as wav
38 ......
39 def get_audio_and_transcriptch(txt_files, wav_files, n_input, n_context,word_num_map,txt_labels=None):
40
41 audio = []
42 audio_len = []
43 transcript = []
44 transcript_len = []
45 if txt_files!=None:
46 txt_labels = txt_files
47
48 for txt_obj, wav_file in zip(txt_labels, wav_files):
49 # 载入音频数据并转化为特征值
50 audio_data = audiofile_to_input_vector(wav_file, n_input, n_context)
51 audio_data = audio_data.astype('float32')
52
53 audio.append(audio_data)
54 audio_len.append(np.int32(len(audio_data)))
55
56 # 载入音频对应的文本
57 target = []
58 if txt_files!=None:#txt_obj 是文件
59 target = get_ch_lable_v(txt_obj,word_num_map)
60 else:
61 target = get_ch_lable_v(None,word_num_map,txt_obj) #txt_obj 是 labels
62
63 transcript.append(target)
64 transcript_len.append(len(target))
65
66 audio = np.asarray(audio)
67 audio_len = np.asarray(audio_len)
68 transcript = np.asarray(transcript)
```