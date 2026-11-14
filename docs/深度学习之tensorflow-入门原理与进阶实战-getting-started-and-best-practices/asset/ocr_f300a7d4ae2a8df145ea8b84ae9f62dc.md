量转成文字；ndarray_to_text_ch 函数，将密集矩阵的字向量转成文字。两个函数都需要传入字表，然后会按照字表对应的索引将字转化回来。

# 代码9-24 yuyinitils (续)

```
200 ......
201 # 常量
202 SPACE_TOKEN = '<space>'
203 SPACE_INDEX = 0
204 FIRST_INDEX = ord('a') - 1
205
206 def sparse_tuple_to_texts_ch(tuple, words):
207     indices = tuple[0]
208     values = tuple[1]
209     results = [""] * tuple[2][0]
210     for i in range(len(indices)):
211         index = indices[i][0]
212         c = values[i]
213
214         c = '' if c == SPACE_INDEX else words[c]
215         results[index] = results[index] + c
216     # 返回 strings 的 List
217     return results
218
219 def ndarray_to_text_ch(value, words):
220     results = ""
221     for i in range(len(value)):
222         results += words[value[i]]
223     return results.replace("'''", ' ')
```

# 9.5.3 训练模型

样本准备好后，就开始模型的搭建了。

# 1. 定义占位符

定义 3 个占位符，具体说明如下。

+   • input_tensor: 为输入的音频数据[none,none,Mfcc_features]，第一个是 batch_size 用 none 来表示；第二个是时序数也用 none 来表示，因为每一批次的时序都是不同的；第三个是 MFCC 的特征，是取当前特征 n_input 和前后 n_context 个特征的组合，即 2× n_context+1 个序列，每个序列特征数为 n_input，于是得出 n_input + (2 × n_input * n_context)。

+   • targets: 音频数据所对应的文本，是一个稀疏矩阵的占位符。

+   • seq_length: 当前 batch 数据的序列长度。

+   • keep_dropout:dropout 的参数。