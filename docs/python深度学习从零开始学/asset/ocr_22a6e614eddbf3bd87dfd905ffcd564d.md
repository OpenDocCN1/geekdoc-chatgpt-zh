# Python深度学习从零开始学

```
In [5]: Survived_0 = data_train.embarked[data_train.survived == 0].value_counts()
Survived_1 = data_train.embarked[data_train.survived == 1].value_counts()
df = pd.DataFrame({'获救':Survived_1,'未获救':Survived_0})
df.plot(kind = 'bar', stacked = True)
plt.title(u'各登录港口乘客的获救情况')
plt.xlabel(u'登录港口')
plt.ylabel(u'人数')
plt.show()
```

![](bbox=[0.17, 0.21, 0.81, 0.41])

图 6-9

乘客的兄弟姐妹、孩子、父母的人数，对是否获救的影响并不明显。

本案例是个不太复杂的数据处理题目，预测结果只有两种，但是题目所给因素较多，需要进行无关因素的排除，排除后再将数据进行预处理。预处理时，需要先补全缺失值。

# 6.4.2 数据预处理

在使用多层感知器模型进行训练和预测之前，必须完成数据预处理的工作。如图 6-10 所示，我们将之前数据预处理的命令全部收集在 PreprocessData 函数中，用这个函数对训练数据和测试数据进行预处理。

其中，“numpy.random.seed(10)” 只是用来设置随机生成器的种子，如果使用相同的 seed() 值，则每次生成的随机数都相同；如果不设置这个值，则每次生成的随机数会因时间的差异而有所不同。

```
In [1]: import numpy;import pandas as pd
from sklearn import preprocessing
numpy.random.seed(10)
```

```
In [2]: all_df = pd.read_excel("titanic3.xls")
cols=['survived','name','pclass','sex','age','sibsp','parch','fare','embarked']
all_df=all_df[cols]
```

```
In [3]: msk = numpy.random.rand(len(all_df)) < 0.8
train_df = all_df[msk];test_df = all_df[~msk]
```

```
In [4]: def PreprocessData(raw_df):
df=raw_df.drop(['name'],axis=1)
age_mean = df['age'].mean()
df['age'] = df['age'].fillna(age_mean)
fare_mean = df['fare'].mean()
df['fare'] = df['fare'].fillna(fare_mean)
df['sex'] = df['sex'].map({'female':0,'male':1}).astype(int)
x_OneHot = pd.get_dummies(data=df,columns=['embarked'])
ndarray = x_OneHot.values
Features = ndarray[:,1:]
Label = ndarray[:,0]
minmax_scale = preprocessing.MinMaxScaler(feature_range=(0,1))
scaledFeatures=minmax_scale.fit_transform(Features)
return scaledFeatures,Label
```

```
In [5]: train_Features,train_Label=PreprocessData(train_df)
test_Features,test_Label=PreprocessData(test_df)
```

图 6-10