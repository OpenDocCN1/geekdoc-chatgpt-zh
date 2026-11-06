# Numpy 数组列表的形式。

```
# (1)Keras 内置数据集
from keras.datasets import boston_housing,mnist,cifar10,imdb
(x_train,y_train),(x_test,y_test)=mnist.load_data()
(x_train2,y_train2),(x_test2,y_test2)=boston_housing.load_data()
(x_train3,y_train3),(x_test3,y_test3)=cifar10.load_data()
(x_train4,y_train4),(x_test4,y_test4)=imdb.load_data(num_words=20000)
num_classes=10
# (2)其他数据集
from urllib.request import urlopen
data=np.loadtxt(urlopen("http://archive.ics.uci.edu/ml/machine-learning-databases/pima-indians-diabetes/pima-indians-diabetes.data"),delimiter=",")
X=data[:,0:8]
y=data[:,8]
```

# 2）数据预处理(Preprocessing)

```
# (1)顺序填充(Sequence Padding)
from keras.preprocessing import sequence
x_train4=sequence.pad_sequences(x_train4,maxlen=80)
x_test4=sequence.pad_sequences(x_test4,maxlen=80)
# (2)One-Hot 编码 (One-Hot Encoding)
from keras.utils import to_categorical
Y_train=to_categorical(y_train, num_classes)
Y_test=to_categorical(y_test, num_classes)
Y_train3=to_categorical(y_train3, num_classes)
Y_test3=to_categorical(y_test3, num_classes)
# (3)标准化/归一化 (Standardization/Normalization)
from sklearn.preprocessing import StandardScaler
scaler=StandardScaler().fit(x_train2)
standardized_X=scaler.transform(x_train2)
standardized_X_test=scaler.transform(x_test2)
# (4)训练集和测试集 (Train and Test Sets)
from sklearn.model_selection import train_test_split
X_train5,X_test5,y_train5,y_test5=train_test_split(X,y,test_size=0.33,random_state=42)
```

# 3）模型架构(Model Architecture)

```
# (1)序列模型(Sequential Model)
from keras.models import Sequential
```