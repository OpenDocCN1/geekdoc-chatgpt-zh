
# 类不平衡处理

```
import numpy as np
import matplotlib.pyplot as plt
from sklearn.ensemble import ExtraTreesClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report 
```

```
from utilities import visualize_classifier 
```

我们将使用文件`data_imbalance.txt`中的数据进行分析。让我们加载数据。
该文件中的每一行都包含逗号分隔的值。前两个值对应于输入数据，最后一个值对应于目标标签。我们在这个数据集中有两个类。让我们从该文件加载数据：

```
# Load input data
input_file = 'data_imbalance.txt'
data = np.loadtxt(input_file, delimiter=',')
X, y = data[:, :-1], data[:, -1] 
```

将输入数据分为两类：

```
# Separate input data into two classes based on labels 
class_0 = np.array(X[y==0])
class_1 = np.array(X[y==1]) 
```

使用散点图可视化输入数据：

```
# Visualize input data
plt.figure()
plt.scatter(class_0[:, 0], class_0[:, 1], s=75, facecolors='black',
                edgecolors='black', linewidth=1, marker='x')
plt.scatter(class_1[:, 0], class_1[:, 1], s=75, facecolors='white',
                edgecolors='black', linewidth=1, marker='o')
plt.title('Input data') 
```

将数据分为训练和测试数据集：

```
# Split data into training and testing datasets
X_train, X_test, y_train, y_test = train_test_split.train_test_split(
        X, y, test_size=0.25, random_state=5) 
```

接下来，我们为极随机森林分类器定义参数。请注意，有一个称为`balance`的输入参数可控制是否通过算法解决类不平衡问题。如果是这样，则需要添加另一个名为`class_weight`的参数，该参数告诉分类器它应该权衡权重，以便与每个类中的数据点数量成正比：

```
# Extremely Random Forests classifier
params = {'n_estimators': 100, 'max_depth': 4, 'random_state': 0}
if len(sys.argv) > 1:
    if sys.argv[1] == 'balance':
        params = {'n_estimators': 100, 'max_depth': 4, 'random_state': 0, 'class_weight': 'balanced'}
    else:
        raise TypeError("Invalid input argument; should be 'balance'") 
```

使用训练数据构建，训练和可视化分类器：

```
classifier = ExtraTreesClassifier(**params)
classifier.fit(X_train, y_train)
visualize_classifier(classifier, X_train, y_train, 'Training dataset') 
```

预测测试数据集的输出并可视化输出：

```
y_test_pred = classifier.predict(X_test)
visualize_classifier(classifier, X_test, y_test, 'Test dataset') 
```

计算分类器的表现并打印分类报告：

```
# Evaluate classifier performance
class_names = ['Class-0', 'Class-1']
print("\n" + "#"*40)
print("\nClassifier performance on training dataset\n")
print(classification_report(y_train, classifier.predict(X_train), 
target_names=class_names))
print("#"*40 + "\n") 
```

```
print("#"*40)
print("\nClassifier performance on test dataset\n")
print(classification_report(y_test, y_test_pred, target_names=class_names))
print("#"*40 + "\n") 
```

```
plt.show() 
```

完整代码在文件`class_imbalance.py`中给出。如果运行代码，您将看到以下图形。第一个图表示输入数据：

![](Images/127a17bfee45d1ad0c102dcf518eac00.png)

图 11：可视化输入数据

第二个图形显示了测试数据的分类器边界：

![](Images/ec84bac6193ae430fcb465c831d1a483.png)

图 12：测试数据集上的分类器边界

前面的图形指示边界无法捕获两个类之间的实际边界。顶部附近的黑色斑点代表边界。最后，您应该看到以下输出：

![](Images/14661f1bd05e9c0f679d0c0d409d62bf.png)

图 13：测试数据集上分类器的表现

您会看到一条警告，因为第一行的值为`0`，这在我们计算`f1-score`时会导致被零除的错误（`ZeroDivisionError`异常）。使用`ignore`标志运行代码，以免看不到被零除警告：

```
$ python3 --W ignore class_imbalance.py 
```

现在，如果要解决类不平衡的问题，请使用`balance`标志运行它：

```
$ python3 class_imbalance.py balance 
```

分类器输出看起来像：

![](Images/f11294ddfd1179a06fed08fad5e1d8dc.png)

图 14：带有平衡的测试数据集的可视化

您应该看到以下输出：

![](Images/7f40b55278c239b9a2056b3d6fa4e507.png)

图 15：测试数据集上分类器的表现

通过考虑的类别不平衡，我们能够对的类别进行分类，从而使`Class-0`中的数据点的整体精度不为零。

## 使用网格搜索找到最佳训练参数

在使用分类器时，并非总是可能知道要使用什么最佳参数。通过手动检查所有可能的组合来使用暴力效率不高。这是网格搜索变得有用的地方。网格搜索使我们可以指定范围为的值，分类器将自动运行各种配置以找出参数的最佳组合。让我们来看看如何做。

创建一个新的 Python 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt
from sklearn.metrics import classification_report
from sklearn import grid_search
from sklearn.ensemble import ExtraTreesClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report 
```

```
from utilities import visualize_classifier 
```

我们将使用`data_random_forests.txt`中的可用数据进行分析：

```
# Load input data
input_file = 'data_random_forests.txt'
data = np.loadtxt(input_file, delimiter=',')
X, y = data[:, :-1], data[:, -1] 
```

将数据分为三类：

```
# Separate input data into three classes based on labels 
class_0 = np.array(X[y==0])
class_1 = np.array(X[y==1])
class_2 = np.array(X[y==2]) 
```

将数据分为训练和测试数据集：

```
# Split the data into training and testing datasets
X_train, X_test, y_train, y_test = cross_validation.train_test_split(
        X, y, test_size=0.25, random_state=5) 
```

指定参数的网格供分类器测试。通常，一个参数保持恒定，而另一个参数变化。然后进行反演以找出最佳组合。在这种情况下，我们想找到`n_estimators`和`max_depth`的最佳值。让我们指定参数网格：

```
# Define the parameter grid
parameter_grid = [{'n_estimators':[100], 'max_depth':[2,4,7,12,16]},
                 {'max_depth':[4],'n_estimators':[25,50,100,250]}] 
```

让我们定义分类器用来找到最佳参数组合的指标：

```
metrics = ['precision_weighted', 'recall_weighted'] 
```

对于每个指标，我们需要运行网格搜索，在其中训练分类器以获取参数组合：

```
for metric in metrics:
    print("\n##### Searching optimal parameters for", metric)

    classifier = grid_search.GridSearchCV(
            ExtraTreesClassifier(random_state=0),
            parameter_grid, cv=5, scoring=metric)
    classifier.fit(X_train, y_train) 
```

打印每个参数组合的分数：

```
 print("\nGrid scores for the parameter grid:")
    for params, avg_score, _ in classifier.grid_scores_:
        print(params, '-->', round(avg_score, 3))

    print("\nBest parameters:", classifier.best_params_) 
```

打印效果报告：

```
 y_pred = classifier.predict(X_test)
    print("\nPerformance report:\n")
    print(classification_report(y_test, y_pred)) 
```

完整代码在文件`run_grid_search.py`中给出。如果运行代码，则将使用精度度量输出以下输出：

![](Images/0a88ac28853348a0b994eaa04e1d76db.png)

图 16：最佳参数搜索输出

基于网格搜索中的组合，它将打印出精度度量的最佳组合。为了找到召回的最佳组合，可以检查以下输出：

![](Images/81484edae1ca5914740c49c9efdb4737.png)

图 17：最佳参数搜索输出

这是用于召回的不同组合，使成为一体，因为精度和召回率是对和不同参数组合进行界定的不同指标。

## 计算特征相对重要性

在将与包含 N 维数据点的数据集一起使用时，必须理解，并非所有特征都同样重要。有些比其他更具歧视性。如果我们有此信息，则可以使用它来减少维数。这对于降低复杂度和提高算法速度很有用。有时，一些特征是完全多余的。因此，可以轻松地将它们从数据集中删除。

我们将使用`AdaBoost`回归器计算特征重要性。`AdaBoost`是 Adaptive Boosting 的缩写，是一种经常与其他机器学习算法结合使用以提高其表现的算法。在`AdaBoost`中，从分布中提取训练数据点以训练当前分类器。该分布会进行迭代更新，以便后续的分类器可以专注于更困难的数据点。困难的数据点是那些分类错误的数据点。这是通过在每个步骤更新发行版来完成的。这将使先前被错误分类的数据点更有可能出现在用于训练的下一个样本数据集中。

然后将这些分类器进行级联，并通过加权多数投票做出决定。

创建一个新的 Python 文件并导入以下包：

```
import numpy as np
import matplotlib.pyplot as plt
from sklearn.tree import DecisionTreeRegressor 
from sklearn.ensemble import AdaBoostRegressor 
from sklearn import datasets
from sklearn.metrics import mean_squared_error, explained_variance_score 
from sklearn.model_selection import import train_test_split
from sklearn.utils import shuffle 
```

```
from utilities import visualize_feature_importances 
```

我们将使用 scikit-learn 中提供的内置房屋数据集：

```
# Load housing data
housing_data = datasets.load_boston() 
```

对数据进行混洗，以使不会偏向于分析：

```
# Shuffle the data
X, y = shuffle(housing_data.data, housing_data.target, random_state=7) 
```

将数据集分为训练和测试：

```
# Split data into training and testing datasets
X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=7) 
```

使用决策树回归器作为单独模型来定义和训练`AdaBoostregressor`：

```
# AdaBoost Regressor model
regressor = AdaBoostRegressor(DecisionTreeRegressor(max_depth=4),
        n_estimators=400, random_state=7)
regressor.fit(X_train, y_train) 
```

估计回归器的表现：

```
# Evaluate performance of AdaBoost regressor
y_pred = regressor.predict(X_test)
mse = mean_squared_error(y_test, y_pred)
evs = explained_variance_score(y_test, y_pred )
print("\nADABOOST REGRESSOR")
print("Mean squared error =", round(mse, 2))
print("Explained variance score =", round(evs, 2)) 
```

此回归器具有一个内置方法，可以调用该方法来计算相对特征的重要性：

```
# Extract feature importances
feature_importances = regressor.feature_importances_
feature_names = housing_data.feature_names 
```

归一化相对特征重要性的值：

```
# Normalize the importance values 
feature_importances = 100.0 * (feature_importances / max(feature_importances)) 
```

对它们进行排序，以便可以进行绘制：

```
# Sort the values and flip them
index_sorted = np.flipud(np.argsort(feature_importances)) 
```

排列条形图在 x 轴上的刻度：

```
# Arrange the X ticks
pos = np.arange(index_sorted.shape[0]) + 0.5 
```

绘制条形图：
