```
```python
# 网络的前向计算
def forward(self, inputs):
    x = self.fc(inputs)
    return x
```
```

这里的代码重写了 forward() 函数，来实现线性回归模型的计算过程。

# 步骤 3：数据准备

项目所需要的数据已经预先处理完毕，使用时简单导入即可。

（1）本次加载的数据集是 CSV 格式的文件，因此使用 pandas 的 read_csv() 进行数据的导入，代码如下。

```
```python
data = pd.read_csv('./data/gas_use_clean.csv').drop(['Unnamed: 0'],axis = 1)
```
```

（2）数据概览，此处只展示部分数据，如图 10-1 所示。

| | distance | speed | temp_inside | temp_outside | 92 | 98 | ac | rain | sun | snow | consume |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 0.743220 | 0.887756 | 1.059402 | 0.949970 | 0 | 1 | 0 | 0 | 1 | 0 | 1.360977 |
| 1 | -0.161145 | -0.068230 | 0.069749 | -0.051240 | 0 | 1 | 0 | 0 | 0 | 0 | 1.504077 |
| 2 | 0.668224 | -1.024216 | -1.909557 | -1.052450 | 0 | 1 | 0 | 0 | 0 | 0 | 1.386294 |
| 3 | -0.355253 | -1.244829 | -0.425077 | -0.766390 | 0 | 1 | 0 | 0 | 0 | 0 | 1.609438 |
| 4 | 0.253787 | -1.465441 | -1.909557 | -0.480330 | 0 | 1 | 0 | 0 | 0 | 0 | 1.722767 |
| 5 | 0.712339 | 0.667143 | -0.425077 | -0.766390 | 1 | 0 | 0 | 0 | 0 | 0 | 1.481605 |
| 6 | -0.346430 | 0.740681 | 0.069749 | -0.051240 | 0 | 1 | 0 | 0 | 0 | 0 | 1.667707 |

图 10-1 数据概览

其中，油耗量 “consume” 用作数据集的标签，而其他的特征用作输入的特征。

（3）划分训练集和测试集，通过以下代码定义数据集划分函数 train_test_split()。

```
```python
def train_test_split(data_split,train_size_rate=0.7):
    assert train_size_rate<1 and train_size_rate>0, 'train_size_rate should be greater than 0 and smaller than 1'
    data_split = data_split.sample(frac=1).reset_index(drop=True) # 通过随机采样打乱数据
    train_data = data_split.iloc[:int(len(data_split)*train_size_rate),:] # 划分训练集
    test_data = data_split.iloc[int(len(data_split)*train_size_rate):,:] # 划分测试集
    return train_data,test_data
```
```

这里默认训练集和测试集的数据比例为 7:3，可以通过控制 train_size_rate 参数的大小来调整比例。需要注意的是，传入的参数的值应该为 (0,1) 的值。

以下代码表示调用数据集划分函数 train_test_split()。

```
```python
data_train,data_test = train_test_split(data)
```
```

（4）将数据封装成数据集。

调用前文介绍过的数据封装方法 load_data() 进行封装。对于不同的任务，只需要传入 DataFrame 格式的数据集，即可完成封装以供深度学习模型使用，其中 DataFrame 是 pandas 库用