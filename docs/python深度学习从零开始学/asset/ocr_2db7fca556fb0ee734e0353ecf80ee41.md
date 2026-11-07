```
data = np.array([[3,-1.7,3.5,-6],
                [0,4,-0.3,2.5],
                [1,3.5,-1.8,-4.5]])
print('原始数据: ')
print(data)
from sklearn.preprocessing import StandardScaler
data_standardscaler=StandardScaler().fit_transform(data)
print('原始数据使用 StandardScaler 进行数据标准化处理后:')
print(data_standardscaler)
from sklearn.preprocessing import MinMaxScaler
data_minmaxscaler=MinMaxScaler(feature_range=(0,1)).fit_transform(data)
print('原始数据使用 MinMaxScaler 进行归一化处理（范围缩放至[0-1]）后:')
print(data_minmaxscaler)
from sklearn.preprocessing import Binarizer
data_binarizer=Binarizer().fit_transform(data)
print('原始数据使用 binarizer 进行二值化处理后:')
print(data_binarizer)
#####################################################
```

运行代码，结果如图 7-6 所示。

![](bbox=[0.27, 0.42, 0.8, 0.6])

图 7-6

StandardScaler 标准化原理是将特征数据的分布调整成标准正态分布（也叫高斯分布），也就是使得数据的均值为 0，方差为 1，这样就可以确保数据的“大小”都是一致的，更有利于模型的训练。而 MinMaxScaler 把所有的数据缩放到 0 和 1 之间。除了对数据进行缩放之外，我们还可以使用 Binarizer 对数据进行二值化处理，将不同的数据全部处理为 0 或 1 这两个数值。归一化其实就是标准化的一种方式，只不过归一化是将数据映射到了 [0,1] 这个区间中。

# 7.2.2 数据预处理案例——缺失值补全、标签化

很多情况下，真实的数据集中会存在缺失值，此时需要对缺失值进行处理。一种方法是将存在缺失值的整条记录直接删除，但是这样做可能会丢失一部分有价值的信息。另一种也是更好的一种方法是推定缺失数据，例如根据已知数据推算缺失的数据。SKImputer 类能够提供一些处理缺失值的基本方法，例如使用缺失值所处的一行或者一列的均值、中位数或者出现频率最高的值作为缺失数据的取值。