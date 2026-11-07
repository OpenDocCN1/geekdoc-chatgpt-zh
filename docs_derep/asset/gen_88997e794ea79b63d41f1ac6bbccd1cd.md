明白了，Word2Vec 本质上是一个把词映射成数值向量的工具，而它产生的词向量正是该算法的输出，能够用数字来刻画词义。小冰接着追问，Word2Vec 具体是怎么实现的。

咖哥解释，Word2Vec 的思路是先搭一个神经网络，让网络学会词的分布式表示。训练的目标可以是已知周围词猜中心词，也可以反过来已知中心词猜周围词。

目前常见的落地形式有两种：CBOW 也就是连续词袋，和 Skip-Gram 也就是跳字模型。CBOW 给定一串上下文词去预测中间的中心词；Skip-Gram 则反过来，用中心词去预测上下文词。两者都在训练过程中不断调整网络参数，最小化预测结果与真实值之间的误差。训练收敛后，词向量就可以从网络的权重里提取出来。

小冰感叹这看起来像个很强的语言模型，既能根据邻居词推断空缺，也能从一个词推断它常出现的邻居。咖哥补充，预测词只是训练手段，真正的目的并非做预测，而是让网络在调参过程中学会词嵌入，从而捕捉词汇间的语义和语法相似性，为后续的 NLP 任务提供高质量表示。尽管示意图里展示了预测流程，Word2Vec 的核心价值在于学到的向量。

## 2.3 Skip-Gram 模型代码实战
> ### 咖哥发言

下面按步骤用 PyTorch 来实现一个简易的 Word2Vec，在这里选用 Skip-Gram 形式。目标是让模型学习到词的向量表示，从而反映词语间的语义和语法关系。

PyTorch 是广泛使用的开源机器学习库，覆盖计算机视觉与自然语言处理等方向。它提供两大能力：

- 张量运算：类似 NumPy，支持高效的 GPU 计算，适合大规模梯度运算。
- 深度学习框架：支持动态计算图，搭建模型和尝试新想法都很灵活。

生态也很丰富，有 TorchText、TorchVision、TorchAudio 等扩展。PyTorch 因易用和灵活，在研究和工业界都很流行。

开始前请按官网指引安装好 PyTorch，安装方式会随 CPU/GPU 版本和操作系统有所不同。

程序结构如下。

### 第1步 构建实验语料库

先准备一个简小的语料作为实验数据，并整理出词汇表。

[PRE0]
创建一个字典，将每个词映射到一个唯一的索引：
[PRE1]python
**In**  # 生成 Skip-Gram 训练数据

def create_skipgram_dataset(sentences, window_size=2):

    data = [] # 初始化数据

    for sentence in sentences: # 遍历句子

        sentence = sentence.split() #将句子分割成单词列表

        for idx, word in enumerate(sentence):# 遍历单词及其索引

            # 获取相邻的单词，将当前单词前后各N个单词作为相邻单词

            for neighbor in sentence[max(idx - window_size, 0):

                min(idx + window_size +1,len(sentences))]:

                    if neighbor != word:# 排除当前单词本身

                        # 将相邻单词与当前单词作为一组训练数据

                            data.append((neighbor, word)

    return data

#使用函数创建Skip-Gram 训练数据

skipgram_data = create_skipgram_dataset(sentences)

#打印未编码的 Skip-Gram 数据样例（前3个）

print("Skip-Gram 数据样例（未编码）:", skipgram_data[:3])
[PRE2]python
**In**  #定义 One-Hot编码函数
import torch#导入torch库
def one_hot_encoding(word, word_to_idx):
    tensor = torch.zeros(len(word_to_idx))#创建一个长度与词汇表相同的全0张量
    tensor[word_to_idx[word]] = 1 #将对应索引位置上的值设为1
return tensor #返回生成的 One-Hot 编码后的向量
[PRE3]python
word_example = "Teacher"

print("One-Hot编码前的单词:", word_example)

print("One-Hot编码后的向量:", one_hot_encoding(word_example, word_to_idx))

####### 展示编码后的 Skip-Gram 训练数据样例

print("Skip-Gram 数据样例 (已编码):(one-hot_encoding(context, word_to_idx),
      word_to_idx[target]] for context, target in skipgram_data[:3])")
[PRE4]python
####### 定义 Skip-Gram 类
import torch.nn as nn #导入 neural network
class SkipGram(nn.Module):
    def __init__(self, voc_size, embedding_size):
        super(SkipGram, self).__init__()
    # 从词汇表大小到嵌入层大小 (维度) 的线性层(权重矩阵)
    self.input_to_hidden = nn.Linear(voc_size,embedding_size, bias=False)
    # 从嵌入层大小 (维度) 到词汇表大小的线性层 (权重矩阵)
    self.hidden_to_output = nn.Linear(embedding_size, voc_size, bias=False)
    def forward(self, X):#前向传播的方式,X 形状为(batch_size,voc_size)
        #通过隐藏层，hidden 形状为(batch_size, embedding_size)
        hidden =self.input_to_hidden(X)
        #通过输出层，output_layer 形状为 (batch_size,voc_size)
        output = self.hidden_to_output(hidden)
    return output

embedding_size=2#设定嵌入层的大小，这里选择2是为了方便展示
skipgram_model=SkipGram(voc_size,embedding_size)#实例化 Skip-Gram模型
print("Skip-Gram类:", skipgram_model)
[PRE5]python
####### 训练 Skip-Gram 类
learning_rate = 0.001 #设置学习速率
epochs = 1000 #设置训练轮次
criterion = nn.CrossEntropyLoss() #定义交叉熵损失函数
import torch.optim as optim #导入随机梯度下降优化器
optimizer = optim.SGD(skipgram_model.parameters(), lr=learning_rate)
#开始训练循环
loss_values = [] #用于存储每轮的平均损失值
for epoch in range(epochs):
    loss_sum = 0 #初始化损失值
    for context, target in skipgram_data:
        X = one_hot_encoding(target, word_to_idx).float().unsqueeze(0) #将中心词转换为向量
        y_true = torch.tensor([word_to_idx[context]], dtype=torch.long) #将周围词转换为索引值
        y_pred = skipgram_model(X) #计算预测值
        loss = criterion(y_pred, y_true) #计算损失
        loss_sum += loss.item() #累积损失
        optimizer.zero_grad() #清空梯度
        loss.backward() #反向传播
        optimizer.step() #更新参数
    if (epoch+1) % 100 == 0: #输出每100轮的损失，并记录损失
        print(f"Epoch:{epoch+1}, Loss:{loss_sum/len(skipgram_data)}")
    loss_values.append(loss_sum / len(skipgram_data))
#绘制训练损失曲线
import matplotlib.pyplot as plt #导入matplotlib
#绘制二维词向量图