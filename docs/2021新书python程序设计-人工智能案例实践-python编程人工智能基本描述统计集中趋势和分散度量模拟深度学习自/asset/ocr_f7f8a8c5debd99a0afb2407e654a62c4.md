泰坦尼克号灾难数据集是最流行的机器学习数据集之一，该数据集提供了包括CSV在内的多种文件格式。

# 通过URL加载泰坦尼克号数据集

如果我们有一个CSV数据集所对应的URL，则可以使用`read_csv`将其加载到`DataFrame`中。在这里，直接从Github加载泰坦尼克号灾难数据集：

```
```python
In [1]: import pandas as pd

In [2]: titanic = pd.read_csv('https://vincentarelbundock.github.io/' +
   ...:                         'Rdatasets/csv/carData/TitanicSurvival.csv')
   ...: 
```
```

# 查看泰坦尼克号数据集中某些行的数据

该数据集包含了1300多行数据，每一行对应一名乘客。根据维基百科，有大约1317名乘客，其中815名乘客死于这场灾难。对于大规模数据集，输出`DataFrame`时，会先显示前30行数据、再显示省略号“...”、最后显示后30行数据。为了节省空间，这里使用`DataFrame`的`head`和`tail`方法查看前5行和后5行数据。`head`和`tail`这两个方法默认都是返回5行数据，我们也可以通过参数指定要显示的行数：

```
```python
In [3]: pd.set_option('precision', 2) # format for floating-point values

In [4]: titanic.head()
Out[4]:
        Unnamed: 0 survived    sex  age passengerClass
0  Allen, Miss. Elisabeth Walton        yes  female  29.00           1st
1   Allison, Master. Hudson Trevor        yes   male   0.92           1st
2  Allison, Miss. Helen Loraine         no  female   2.00           1st
3  Allison, Mr. Hudson Joshua Crei        no   male  30.00           1st
4   Allison, Mrs. Hudson J C (Bessi        no  female  25.00           1st

In [5]: titanic.tail()
Out[5]:
        Unnamed: 0 survived    sex  age passengerClass
1304  Zabour, Miss. Hileni         no  female  14.50           3rd
1305  Zabour, Miss. Thamine         no  female    NaN           3rd
1306  Zakarian, Mr. Mapriededer         no   male  26.50           3rd
1307  Zakarian, Mr. Ortin         no   male  27.00           3rd
1308  Zimmerman, Mr. Leo         no   male  29.00           3rd
```
```

请注意，pandas会根据列中最宽的数据值或列标题（以较宽者为准）调整每列的宽度。另外，请注意，第1305行`age`列的值是`NaN`（即非数值），用于表示缺失值。