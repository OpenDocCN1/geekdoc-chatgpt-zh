```
sklearn.model_selection.StratifiedKFold(n_splits=3, shuffle=False, random_state=None)
```

其参数与 KFold 函数相同。

# 【例2-2】比较 KFold 和 StratifiedKFold 的区别

```
from sklearn.model_selection import KFold, StratifiedKFold
import numpy as np
X = np.random.randint(50,size=(8, 4))
y=np.array([1,1,0,0,1,1,0,0])
folder=KFold(n_splits=3, random_state=0, shuffle= False)
for train_index, test_index in folder.split(X,y) :
print('Train_index:',train_index)
print('Test_index:',test_index)
print('Y_train:',y[train_index])
print('Y_test:',y[test_index])
folder=StratifiedKFold(n_splits=3, random_state=0, shuffle= False)
for train_index, test_index in folder.split(X,y) :
print('Stratify_train_index:',train_index)
print('Stratify_test_index:',test_index)
print('Stratify_Y_train:',y[train_index])
print('Stratify_Y_test:',y[test_index])
```

运行结果如下：

```
Train_index: [3 4 5 6 7]
Test_index: [0 1 2]
Y_train: [0 1 1 0 0]
Y_test: [1 1 0]
Train_index: [0 1 2 6 7]
Test_index: [3 4 5]
Y_train: [1 1 0 0 0]
Y_test: [0 1 1]
Train_index: [0 1 2 3 4 5]
Test_index: [6 7]
Y_train: [1 1 0 0 1 1]
Y_test: [0 0]
Stratify_train_index: [4 5 6 7]
Stratify_test_index: [0 1 2 3]
Stratify_Y_train: [1 1 0 0]
Stratify_Y_test: [1 1 0 0]
Stratify_train_index: [0 1 2 3 5 7]
Stratify_test_index: [4 6]
Stratify_Y_train: [1 1 0 0 1 0]
Stratify_Y_test: [1 0]
Stratify_train_index: [0 1 2 3 4 6]
Stratify_test_index: [5 7]
Stratify_Y_train: [1 1 0 0 1 0]
Stratify_Y_test: [1 0]
```

Scikit-learn 还提供了函数 cross_val_score，它可以在分割数据集后，进行训练和测试并计算评估指标结果。其函数原型如下：