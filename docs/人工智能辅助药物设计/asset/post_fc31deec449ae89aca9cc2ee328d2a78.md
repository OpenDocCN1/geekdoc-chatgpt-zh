
size=2048, chiral=False,
6.   bonds=True,features=False, sparse=False,
smiles=False)
7.   feature = engine(mol)
8.   feature_list.extend(feature)
9.   ID_feature_df = pd.DataFrame(feature_list)
10.  ID_feature_df.to_csv('ecfp-drugbank-all.csv',index=0)

运行结果如图15-4所示，指纹总长度为2048，图中未能全部展示。

| | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | ... | 2038 | 2039 | 2040 | 2041 | 2042 | 2043 | 2044 | 2045 | 2046 | 2047 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 |
| 2 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 3 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 4 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |
| 8650 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 8651 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 8652 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 8653 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 |
| 8654 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | ... | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |

8655 rows × 2048 columns

### 图15-4 数据特征化处理运行结果

## 15.3.2 机器学习预测药物分子子性质

我们将使用pandas、numpy、sklearn和scipy中的相关模块，如清单15-6所示。

### 清单15-6 导入相关工具包和模块

```
1. from pandas import read_csv
2. import numpy as np
3. from sklearn.model_selection import train_test_split
4. from sklearn.ensemble import RandomForestRegressor
5. import scipy
6. import matplotlib.pyplot as plt
```

在清单15-7中，使用pandas中的read_csv方法读取文件中的数据，csv文件数据之间以逗号分隔，则需要设置参数delimiter=','，得到的数据对象dataframe为DataFrame格式，进而获得数据集dataset。我们将dataset中的所有数据作为输入值 X，将指定文件夹下drugbank_all_logP.AlogP.value中的数据作为结果值 Y，数据集 Y 为一维数组，如图15-5所示。使用train_test_split方法将数据集以8:2的比例随机划分为训练集和测试集。train_test_split方法中前两个参数分别为输入数据集和结果数据集，test_size参数为数据集划分比例，random_state控制每次划分训练集和测试集的模式。

### 提示

将random_state指定为一个数字，能够使程序在任何时候、任何机型上都能将数据集划分出相同的效果，即划分后的各个训练集和测试集是一致的。但当将random_state设置为不同的数字时，使用train_test_split方法划分数据集所产生的效果一定是不同的。由此可见，计算机中所谓的随机并不是真正的随机。

```
[-0.55 -0.96 -2.67 ... 3.05 3.82 2.61]
len(Y) : 8655
```

### 图15-5 数据集 Y 为一维数组

### 清单15-7 读取数据文件，并划分训练集和测试集

```
1. dataframe = read_csv("./data/drugbank/ecfp-drugbank-all.csv", delimiter=',')
2. dataset = dataframe.values
3. X = dataset[:, :]
4. Y = np.loadtxt("./data/drugbank/drugbank_all_logP.AlogP.value")
5. X_train, X_test, Y_train, Y_test = train_test_split(
6. X, Y, test_size=0.2, random_state=42)
```

定义随机森林预测模型对象estimators，其中设置建立子树数量为90，树的最大深度为24，根据属性划分节点时最少需要6个节点，每个叶子节点的最小样本数为5，单个决策树使用特征的最大数为总特征数的平方根个数，该模型使用oob_score，oob_score是一种非常强大的模型验证技术，专门用于随机森林算法以实现最小方差结果。然后将训练集X_train、Y_train载入模型中，将运行后的模型保存为history。通过history的oob_score属性，我们能够直观地了解该模型用于预测的准确程度。该过程见清单15-8。程序运行结果为training accuracy=0.671。

### 清单15-8 使用随机森林回归训练数据集并评估模型效果

```
1. estimators = RandomForestRegressor(n_estimators=90,
max_depth=24,
min_samples_split=6, min_samples_leaf=5,
max_features='sqrt',
oob_score=True)
2.  history = estimators.fit(X_train, Y_train )
3.  print('training accuracy:', history.oob_score_)
```

在清单15-9中，使用训练好的模型对测试集进行预测，prediction以一维数组的形式保存着测试集经过模型预测后的结果。绘制模拟坐标分别为预测值和真实值的散点图，我们能直观地观察到模型对测试集数据的整体预测效果，如图15-6所示。散点离直线 y=x 越近，表明预测越准确。

### 清单15-9 使用模型对测试集进行预测

```
1.  prediction = estimators.predict(X_test)
2.  plt.scatter (prediction,Y_test, color='red',s=2)
3.  x = np.linspace(-2, 5, 10000)
4.  plt.plot(x,x,color=" blue ")
5.  plt.xlabel( " prediction " )
6.  plt.ylabel( " Y_test " )
7.  plt.show()
```

![](img/df1b219b2c6176235f1ea4f385724826_373_0.png)

### 图15-6 模型对测试集整体预测效果的展示

## 15.4 深度学习中的药物分子子性质预测

### 15.4.1 特征化处理和数据集划分

SDF文件中的ROMol属性记录药物分子的拓扑结构，我们将利用数据库中分子的ROMol属性值来预测药物分子的pKa值。首先需要导入相关工具模块，如清单15-10所示。

### 清单15-10 导入相关工具模块

```
1. from rdkit import Chem
2. import pandas as pd
3. from rdkit.Chem import Descriptors
4. from rdkit.ML.Descriptors import MoleculeDescriptors
5. from rdkit.Chem import PandasTools
6. from sklearn.model_selection import train_test_split
7. from sklearn.preprocessing import MinMaxScaler
```

在清单15-11中，载入SDF文件drugbank_approved_logP.sdf，读取标签名为JCHEM_PKA所在列的值，将其转换为float类型后保存在New_pKa标签下。图15-7为df数据表中的部分属性数据。

### 清单15-11 读取数据文件

```
1. df = PandasTools.LoadSDF('drugbank_approved_logP.sdf')
2. pd.set_option('max_columns', None)
3. df[" New_pKa "]=[float(i) for i in df[" JCHEM_PKA "].values]
4. print(df.head())
```

| DATABASE_ID | SMILES | INCHI_IDENTIFIER | New_pKa |
|---|---|---|---|
| 0 | DB00006 | CC(C@H)(C)[C@H](NC(=O)C@H)(CCC(O)=O)(NC(=O)[C@H... | InChI=1S/C99H138N24O33/c1-52(4)82(96(153)122... | 3.177101 |
| 1 | DB00014 | CC(C)[C@H](NC(=O)[C@H](CCC(O)=O)(C)(C@H]... | InChI=1S/C59H84N18O14/c1-31(2)22-40(49(82)68-3... | 9.818594 |
| 2 | DB00027 | CC(C)[C@H](NC(=O)CNC(=O)[C@H](NC(=O)C(C)C)(... | InChI=1S/C99H135N19O16/c1-50(2)36-71(105-79(11... | 11.945820 |
| 3 | DB00035 | NC(=O)CC[C@H]1NC(=O)[C@H](CC2=CC=CC=C2)NC(=O)... | InChI=1S/C46H64N14O12S2/c47-35(62)15-14-29-40(... | 11.344259 |
| 4 | DB00050 | CC(C)[C@H](NC(=O)[C@H](CCCNC(N)=O)NC(=O)[C@H... | InChI=1S/C70H92CN17O14/c1-39(2)31-52(61(94)82... | 11.590298 |

### 图15-7 df数据表中的部分属性数据

调用Descriptors_descList能够查到分子描述符的属性名称，如图15-8所示。

```
['MaxEStateIndex', 'MinEStateIndex', 'MaxAbsEStateIndex', 'MinAbsEStateIndex', 'qed',
'MolWt', 'HeavyAtomMolWt', 'ExactMolWt', 'NumValenceElectrons', 'NumRadicalElectrons',
'MaxPartialCharge', 'MinPartialCharge', 'MaxAbsPartialCharge', 'MinAbsPartialCharge',
'FpDensityMorgan1', 'FpDensityMorgan2', 'FpDensityMorgan3', 'BCUT2D_MWHI', 'BCUT2D_MWLOW',
'BCUT2D_CHGHI', 'BCUT2D_CHGLO', 'BCUT2D_LOGPHI', 'BCUT2D_LOGPLOW', 'BCUT2D_MRHI',
'BCUT2D_MRLOW', 'BalabanJ', 'BertzCT', 'Chi0', 'Chi0n', 'Chi0v', 'Chi1', 'Chi1n', 'Chi1v',
'Chi2n', 'Chi2v', 'Chi3n', 'Chi3v', 'Chi4n', 'Chi4v', 'HallKierAlpha', 'Ipc', 'Kappa1', 'Kappa2',
'Kappa3', 'LabuteASA', 'PEOE_VSA1', 'PEOE_VSA10', 'PEOE_VSA11', 'PEOE_VSA12',
'PEOE_VSA13', 'PEOE_VSA14', 'PEOE_VSA2', 'PEOE_VSA3', 'PEOE_VSA4', 'PEOE_VSA5',
```

### 图15-8 分子描述符部分属性名称

在清单15-12中，使用MolecularDescriptorCalculator()创建一个计算描述符对象calc，该对象能够查询分子的所有属性值。header变量中保存calc对象能够查询到的属性标签，它在最终的特征表中作为表头。利用calc对象计算df表中ROMol列，将查询结果保存在列表rdkit_2d_desc中，然后将查询属性标签header和查询结果组合成一个特征表df_final并在表的最后添加一列属性New_pKa。在特征表df_final作为数据集前，还需要检查表中是否有空值存在，如果有，则去除该行数据。程序运行结果如图15-9所示。

### 清单15-12 提取特征

```
1.  rdkit_2d_desc = []
2.  calc = MoleculeDescriptors.MolecularDescriptorCalculator([x[0] for x in Descriptors._descList])
3.  header = calc.GetDescriptorNames()
4.  for i in range(len(df)):
5.      ds = calc.CalcDescriptors(df["ROMol"][i])
6.      rdkit_2d_desc.append(ds)
7.  df_final = pd.DataFrame(rdkit_2d_desc,columns=header)
8.  df_final=pd.concat([df_final,df["New_pKa"]],axis=1)
9.  is_NaN = df_final.isnull()
10. row_has_NaN = is_NaN.any(axis=1)
11. rows_with_NaN = df_final[row_has_NaN]
12. df_final.drop(index=rows_with_NaN.index,inplace=True)
13. print(df_final.head())
```

| fr_thiazole | fr_thiocyan | fr_thiophene | fr_unbrch_alkane | fr_urea | New_pKa |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | 0 | 0 | 1 | 0 | 3.177101 |
| 0 | 0 | 0 | 1 | 1 | 9.818594 |
| 0 | 0 | 0 | 0 | 0 | 11.945820 |
| 0 | 0 | 0 | 1 | 0 | 11.344259 |
| 0 | 0 | 0 | 2 | 1 | 11.590298 |

### 图15-9 df_final数据表部分截图

数据表df_final中前 n-1列为神经网络的输入数据，第六列为对应的真实结果数据。由于各个评价指标的性质不同，通常具有不同的量纲和数量级。当各指标间的水平相差很大时，如果直接用原始指标值进行分析，就会突出数值较高的指标在综合分析中的作用，相对削弱数值较低的指标的作用。因此，为了保证结果的可靠性，需要对原始指标数据进行归一化处理，此处使用的是Min-Max归一化方法。之后，使用train_test_split划分训练集和测试集，如清单15-13所示。

### 清单15-13 划分数据集

```
1.  X=df_final.values[:,0:-1]
2.  y=df_final.values[:,-1]
3.  scaler=MinMaxScaler()
4.  X=scaler.fit_transform(X)
5.  X_train1, X_test1, y_train, y_test = train_test_split(X, y, test_size
    =0.2, random_state=101)
