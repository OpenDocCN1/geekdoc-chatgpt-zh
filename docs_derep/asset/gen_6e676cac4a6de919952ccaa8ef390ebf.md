# AG News 新闻分类：字符 CNN 与 Word2Vec CNN 实战拆解

下面用一套完整可跑的流程演示如何在 AG News 上做文本分类。整体思路是先把标题做成固定形状的矩阵，再用两种不同的卷积网络分别做字符级和词向量级建模，最后用相同的训练套路进行评估。

先看字符级的数据准备与读取。标题经过清洗后会被展开成 handle_length x 28 的矩阵，不足的部分用全零矩阵补齐，拼接后返回。数据集函数会遍历 ../dataset/ag_news数据集/dataset/train.csv，把第 0 列转成标签，第 1 列清洗后生成字符矩阵，并把标签转成 one-hot。

        pad_matrix = np.zeros([handle_length,28])

        string_matrix = np.concatenate([string_matrix,pad_matrix],axis=0)

        return string_matrix

#获取数据集

def get_dataset():

    agnews_label = []

    agnews_title = []

    agnews_train = csv.reader(open("../dataset/ag_news数据集/dataset/train.csv","r"))

    for line in agnews_train:

        agnews_label.append(np.int_(line[0]))

        agnews_title.append(text_clearTitle(line[1]))

    train_dataset = []

    for title in agnews_title:

        string_matrix = get_handle_string_matrix(title)

        train_dataset.append(string_matrix)

    train_dataset = np.array(train_dataset)

    label_dataset = get_label_one_hot(agnews_label)

    return train_dataset,label_dataset

if __name__ == '__main__':

    get_dataset()

字符级网络采用一维卷积结构，通过 einops 的 Rearrange 在通道与长度之间切换，首层把输入通道映射到 32，次层再映射到 28，中间分别加上 ReLU 与 LayerNorm，最后 Flatten 配合两层全连接输出 5 类概率。

[PRE0]python

import torch

import einops.layers.torch as elt

def char_CNN(input_dim = 28):

    model = torch.nn.Sequential(

        #第一层卷积

        elt.Rearrange("b l c -> b c l"),

        torch.nn.Conv1d(input_dim,32,kernel_size=3,padding=1),

        elt.Rearrange("b c l -> b l c"),

        torch.nn.ReLU(),

        torch.nn.LayerNorm(32),

        #第二层卷积

        elt.Rearrange("b l c -> b c l"),

        torch.nn.Conv1d(32, 28, kernel_size=3, padding=1),

        elt.Rearrange("b c l -> b l c"),

        torch.nn.ReLU(),

        torch.nn.LayerNorm(28),

        #flatten

        torch.nn.Flatten(),  #[batch_size,64 * 28]

        torch.nn.Linear(64 * 28,64),

        torch.nn.ReLU(),

        torch.nn.Linear(64,5),

        torch.nn.Softmax()

    )

    return model

if __name__ == '__main__':

    embedding = torch.rand(size=(5,64,28))

    model = char_CNN()

    print(model(embedding).shape)

训练部分统一使用 sklearn 的 train_test_split，按 0.1 比例切分，随机种子 828。设备自动选择 cuda 或 cpu，优化器选用 Adam 学习率 1e-4，手工实现交叉熵，batch 大小 128，循环 99 个 epoch 并打印 loss 与准确率。

[PRE1]python

import get_data

from sklearn.model_selection import train_test_split

train_dataset,label_dataset = get_data.get_dataset()

X_train,X_test, y_train, y_test = train_test_split(train_dataset,label_dataset,test_size=0.1, random_state=828) #将数据集划分为训练集和测试集

#获取device

device = "cuda" if torch.cuda.is_available() else "cpu"

model = char_CNN().to(device)

# 定义交叉熵损失函数

def cross_entropy(pred, label):

    res = -torch.sum(label * torch.log(pred)) / label.shape[0]

    return torch.mean(res)

optimizer = torch.optim.Adam(model.parameters(), lr=1e-4)

batch_size = 128

train_num = len(X_test)//128

for epoch in range(99):

    train_loss = 0.

    for i in range(train_num):

        start = i * batch_size

        end = (i + 1) * batch_size

        x_batch = torch.tensor(X_train[start:end]).type(torch.float32).to(device)

        y_batch = torch.tensor(y_train[start:end]).type(torch.float32).to(device)

        pred = model(x_batch)

        loss = cross_entropy(pred, y_batch)

        optimizer.zero_grad()

        loss.backward()

        optimizer.step()

        train_loss += loss.item() # 记录每个批次的损失值

    # 计算并打印损失值

    train_loss /= train_num

    accuracy = (pred.argmax(1) == y_batch.argmax(1)).type(torch.float32).sum().item() / batch_size

    print("epoch: ",epoch,"train_loss:", round(train_loss,2),"accuracy:", round(accuracy,2))

词向量路线先对标题做清洗：转小写、只保留 a-z、合并多余空格、去首尾、在末尾加上 eos 标记，最后切分成词列表。

[PRE2]python

def text_clearTitle_word2vec(text):

    text = text.lower()                    #将文本转换成小写

    text = re.sub(r"[^a-z]"," ",text)    #替换非标准字符，^是求反操作

    text = re.sub(r" +", " ", text)       #替换多重空格

    text = text.strip()                    #取出首尾空格

    text = text + " eos"                   #添加结束符，注意eos前有空格

    text = text.split(" ")                 #对文本分词转成列表存储

    return text

基于清洗后的词列表用 Gensim 的 Word2Vec 训练 64 维词向量，window 5，min_count 0。每个样本截断或补齐到 n=12，补齐使用接近零的矩阵避免 log 报错，最后在第 3 维扩展成 4D 并配合 one-hot 标签返回。

[PRE3]python

def get_word2vec_dataset(n = 12):

    agnews_label = []  #创建标签列表

    agnews_title = []  #创建标题列表

    agnews_train = csv.reader(open("../dataset/ag_news数据集/dataset/train.csv","r"))

    for line in agnews_train:  #将数据读取到对应列表中

        agnews_label.append(np.int(line[0]))

        agnews_title.append(text_clearTitle_word2vec(line[1])) #对数据进行清洗之后再读取

    from gensim.models import word2vec  # 导入Gensim包

    model = word2vec.Word2Vec(agnews_title, vector_size=64, min_count=0, window=5)#设置训练参数

    train_dataset = []  #创建训练集列表

    for line in agnews_title:  #对长度进行判定

        length = len(line)  #获取列表长度

        if length > n:  #对列表长度进行判断

            line = line[:n]  #截取需要的长度列表

            word2vec_matrix = (model.wv[line])  #获取word2vec矩阵

            train_dataset.append(word2vec_matrix)  #将word2vec矩阵添加到训练集中

        else:  #补全长度不够的操作

            word2vec_matrix = (model.wv[line])  #获取word2vec矩阵

            pad_length = n - length  #获取需要补全的长度

            pad_matrix = np.zeros([pad_length, 64]) + 1e-10  #创建补全矩阵并增加一个小数值

            word2vec_matrix = np.concatenate([word2vec_matrix, pad_matrix], axis=0) #矩阵补全

            train_dataset.append(word2vec_matrix)  #将word2vec矩阵添加到训练集中

    train_dataset = np.expand_dims(train_dataset,3)  #对三维矩阵进行扩展

    label_dataset = get_label_one_hot(agnews_label)  #转换成one-hot矩阵

    return train_dataset, label_dataset

词向量 CNN 把 b l d 1 重排为 b 1 l d，用二维卷积做局部特征提取：第一层 1->3 通道，第二层 3->5 通道，均使用 kernel 3 并接 ReLU 与 BatchNorm，最后 Flatten 后经 2400->64->5 的全连接输出。

[PRE4]python

import torch

import einops.layers.torch as elt

def word2vec_CNN(input_dim = 28):

    model = torch.nn.Sequential(

        elt.Rearrange("b l d 1 -> b 1 l d"),

        #第一层卷积

        torch.nn.Conv2d(1,3,kernel_size=3),

        torch.nn.ReLU(),

        torch.nn.BatchNorm2d(num_features=3),

        #第二层卷积

        torch.nn.Conv2d(3, 5, kernel_size=3),

        torch.nn.ReLU(),

        torch.nn.BatchNorm2d(num_features=5),

        #flatten

        torch.nn.Flatten(),  #[batch_size,64 * 28]

        torch.nn.Linear(2400,64),

        torch.nn.ReLU(),

        torch.nn.Linear(64,5),

        torch.nn.Softmax()

    )

    return model

"-------------------下面是模型训练部分-------------------"

import get_data_84 as get_data

from sklearn.model_selection import train_test_split

train_dataset,label_dataset = get_data.get_word2vec_dataset()

X_train,X_test, y_train, y_test =

train_test_split(train_dataset,label_dataset,test_size=0.1, random_state=828)  #将数据集划分为训练集和测试集

#获取device

device = "cuda" if torch.cuda.is_available() else "cpu"

model = word2vec_CNN().to(device)

# 定义交叉熵损失函数