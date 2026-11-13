
## 15.4 深度学习中的药物分子子性质预测

### 15.4.1 特征化处理和数据集划分

SDF文件中的ROMol属性记录药物分子的拓扑结构，我们将利用数据库中分子的ROMol属性值来预测药物分子的pKa值。首先需要导入相关工具模块，如清单15-10所示。

清单15-10 导入相关工具模块

```python
from rdkit import Chem
import pandas as pd
from rdkit.Chem import Descriptors
from rdkit.ML.Descriptors import MoleculeDescriptors
from rdkit.Chem import PandasTools
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import MinMaxScaler
```

在清单15-11中，载入SDF文件drugbank_approved_logP.sdf，读取标签名为JCHEM_PKA所在列的值，将其转换为float类型后保存在New_pKa标签下。图15-7为df数据表中的部分属性数据。

清单15-11 读取数据文件

```python
df = PandasTools.LoadSDF('drugbank_approved_logP.sdf')
pd.set_option('max_columns', None)
df["New_pKa"]=[float(i) for i in df["JCHEM_PKA"].values]
print(df.head())
```

| DATABASE_ID | SMILES | INCHI_IDENTIFIER | New_pKa |
|---|---|---|---|
| 0 | DB00006 | CC[C@H](C)[C@H](NC(=O)C@H)... | InChI=1S/C9H8H138N24O33/c1-5-52(4)82(96(153))122-... | 3.177101 |
| 1 | DB00014 | CC(C)(C)C@H](NC(=O)C@H]... | InChI=1S/C59H84N18O14-c1-31(2)22-40(49)82-68-3-... | 9.818594 |
| 2 | DB00027 | CC(C)C(C@H](NC(=O)CNC(=O) [C@@H](NC=O)C(C)C(... | InChI=1S/C96H135N19O16/c1-50(2)36-71(105-79(11... | 11.945820 |
| 3 | DB00035 | NC(=O)CC[C@@H]1NC(=O)C@H] (CC2=CC=CC=C2)NC(=O)... | InChI=1S/C46H64N14O12S2/c47-35(62)15-14-29-40(... | 11.344259 |
| 4 | DB00050 | CC(C)C[C@H](NC(=O)C@H] (CCNCNC(N)=O)NC(=O)[C@H... | InChI=1S/C70H92CN17O14/c1-39(2)31-52(61(94)82... | 11.590298 |

图15-7 df数据表中的部分属性数据

调用Descriptors._descList能够查到分子描述符的属性名称，如图15-8所示。

```python
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

图15-8 分子描述符部分属性名称

在清单15-12中，使用MolecularDescriptorCalculator()创建一个计算描述符对象calc，该对象能够查询分子的所有属性值。header变量中保存calc对象能够查询到的属性标签，它在最终的特征表中作为表头。利用calc对象计算df表中ROMol列，将查询结果保存在列表rdkit_2d_desc中，然后将查询属性标签header和查询结果组合成一个特征表df_final并在表的最后添加一列属性New_pKa。在特征表df_final作为数据集前，还需要检查表中是否有空值存在，如果有，则去除该行数据。程序运行结果如图15-9所示。

清单15-12 提取特征

```python
rdkit_2d_desc = []
calc = MolecularDescriptors.MolecularDescriptorCalculator([x[0] for x in Descriptors._descList])
header = calc.GetDescriptorNames()
for i in range(len(df)):
    ds = calc.CalcDescriptors(df["ROMol"][i])
    rdkit_2d_desc.append(ds)
df_final = pd.DataFrame(rdkit_2d_desc,columns=header)
df_final=pd.concat([df_final,df["New_pKa"]],axis=1)
is_NaN = df_final.isnull()
row_has_NaN = is_NaN.any(axis=1)
rows_with_NaN = df_final[row_has_NaN]
df_final.drop(index=rows_with_NaN.index,inplace=True)
print(df_final.head())
```

| fr_thiazole | fr_thiocyan | fr_thiophene | fr_unbrch_alkane | fr_urea | New_pKa |
|---|---|---|---|---|---|
| 0 | 0 | 0 | 1 | 0 | 3.177101 |
| 0 | 0 | 0 | 1 | 1 | 9.818594 |
| 0 | 0 | 0 | 0 | 0 | 11.945820 |
| 0 | 0 | 0 | 1 | 0 | 11.344259 |
| 0 | 0 | 0 | 2 | 1 | 11.590298 |

图15-9 df_final数据表部分截图

数据表df_final中前 n-1列为神经网络的输入数据，第六列为对应的真实结果数据。由于各个评价指标的性质不同，通常具有不同的量纲和数量级。当各指标间的水平相差很大时，如果直接用原始指标值进行分析，就会突出数值较高的指标在综合分析中的作用，相对削弱数值较低的指标的作用。因此，为了保证结果的可靠性，需要对原始指标数据进行归一化处理，此处使用的是Min-Max归一化方法。之后，使用train_test_split划分训练集和测试集，如清单15-13所示。

清单15-13 划分数据集

```python
X=df_final.values[:,0:-1]
y=df_final.values[:,-1]
scaler=MinMaxScaler()
X=scaler.fit_transform(X)
X_train1, X_test1, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=101)
```

### 15.4.2 深度学习预测药物分子子性质

Sequential模型结构是一种网络层的线性堆栈。它是一个简单的线性结构，没有多余分支，是多个网络层的堆叠。从代码中可看出这是一个7层网络结构，4个全连接层和3个dropout层，该模型选择adam优化器和均方差（mse）损失函数。

将数据装载到模型中，前两个参数分别为训练数据的输入和输出，validation_data为测试集的输出，训练一次网络需要的样本数batch_size=128，一共迭代训练400次。具体实现如清单15-14所示。图15-10所示的是模型训练过程中的详细信息。

清单15-14 构建神经网络模型

```python
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense
from tensorflow.keras.layers import Dropout
model=Sequential()
model.add(Dense(200,activation="tanh"))
model.add(Dropout(0.2))
model.add(Dense(100,activation="relu"))
model.add(Dropout(0.2))
model.add(Dense(50,activation="relu"))
model.add(Dropout(0.2))
model.add(Dense(1,activation="linear"))
model.compile(optimizer='adam',loss='mse')
model.fit(x=X_train,y=y_train, validation_data=(X_test,y_test),batch_size=128,epochs=400)
```

```
6/6 [==============================] - 0s 5ms/step - loss: 3.7753 - val_loss: 4.1367
Epoch 393/400
6/6 [==============================] - 0s 5ms/step - loss: 3.3696 - val_loss: 4.4091
Epoch 394/400
6/6 [==============================] - 0s 4ms/step - loss: 3.2914 - val_loss: 4.1826
Epoch 395/400
6/6 [==============================] - 0s 5ms/step - loss: 3.3773 - val_loss: 4.2328
Epoch 396/400
6/6 [==============================] - 0s 4ms/step - loss: 3.3208 - val_loss: 4.4095
Epoch 397/400
6/6 [==============================] - 0s 5ms/step - loss: 3.3318 - val_loss: 4.3793
Epoch 398/400
6/6 [==============================] - 0s 4ms/step - loss: 3.6522 - val_loss: 4.0488
Epoch 399/400
6/6 [==============================] - 0s 5ms/step - loss: 3.3757 - val_loss: 4.0351
Epoch 400/400
6/6 [==============================] - 0s 4ms/step - loss: 3.4886 - val_loss: 4.6354
```

图15-10 模型训练过程中的详细信息

模型的fit函数返回一个history的对象，其history.history属性记录了损失函数和其他指标的数值随epoch变化的情况。将损失函数的变化进行可视化显示，代码如清单15-15所示。通过图15-11所示的损失函数变化图更容易查看损失函数数值变化的趋势。其中loss是训练集的损失值，loss值是神经网络参数更新的重要依据，val_loss是测试集的损失函数，val_loss值不会对神经网络参数产生影响。

清单15-15 损失函数变化可视化显示

```python
loss=pd.DataFrame(model.history.history)
loss.plot()
```

![](img/df1b219b2c6176235f1ea4f385724826_379_0.png)

图15-11 损失函数变化图

在清单15-16中，用训练好的模型对测试集进行预测，得到的结果保存在列表y_predict中。绘制散点图，横坐标和纵坐标分别为预测值和真实值，如图15-12所示，我们能直观地观察到模型对测试集数据的整体预测效果。散点离直线 y = x 越近，表明预测越准确。

清单15-16 评估模型预测效果

```python
import matplotlib.pyplot as plt
import numpy as np
y_predict=model.predict(X_test)
plt.scatter(y_predict,y_test, color='red', s=4)
x = np.linspace(0,20, 10000)
plt.plot(x,x,color="blue")
plt.xlabel("y_predict")
plt.ylabel("Y_true")
plt.show()
```

![](img/df1b219b2c6176235f1ea4f385724826_380_0.png)

图15-12 模型对测试集的整体预测效果的展示

## 15.5 参考资料

- [1] 陈安进, 石杰. 计算机辅助药物设计中用于建模的计算方法研究进展[J].中国海洋大学学报（自然科学版）, 2005, 35(3): 407-411.
- [2] 陈瑶, 葛卫红, 廖俊. 深度学习在医药领域命名实体识别中的研究进展[J].药学进展, 2020, 44(1): 28-34.
- [3] 邓燕红, 蔡滵萱, 张建华, 等. 基于深度学习的微管蛋白秋水仙碱位点抑制剂的预测研究[J].化学研究与应用, 2020, 32(12): 2192-2198.
- [4] 黄芳, 杨红飞, 朱迅. 人工智能在新药发现中的应用进展[J].药学进展, 2021, 45(7): 502-511.
- [5] 兰欣, 卫荣, 蔡宏伟, 等. 机器学习算法在医疗领域中的应用[J].医疗卫生装备, 2019, 40(3): 93-97.
- [6] 李凤雷, 胡乔宇, 熊若凡, 等. 基于深度学习的药物设计方法[J].自然杂志, 2021, 43(5): 383-390.
- [7] 李伟, 杨金才, 黄牛, 等. 深度学习在药物设计与发现中的应用[J].药学学报, 2019, 54(5): 761-767.
- [8] 梁桂兆, 梅虎, 周原, 等. 计算机辅助药物设计中的多维定量构效关系模型化方法[J].化学进展, 2006, 18(1): 120-127.
- [9] 沈泓佑, 何小妮, 鲁素芳, 等. 新型萘醌类拓扑异构酶 II 抑制剂的深度学习设计与合成[J].合成化学, 2021, 29(4): 261-267.
- [10] 谢良旭, 薛亮亮, 李峰. 神经网络的深度与宽度对药物分子pKa预测性能影响的研究[J].江苏理工学院学报, 2021, 27(2): 1-8.
- [11] 徐娟, 王林. 计算机辅助药物设计中的QSPR,QSAR和QSMR研究[J].国外医学（药学分册）, 2003, 30(3): 135-138.
- [12] 颜彩琴, 范睿琦, 宁雨坪, 等. 深度学习模型在中药毒性预警中的应用和前景[J].中国药理学与毒理学杂志, 2022, 36(3): 231-240.
- [13] 于海瀛, 陈伟, 袁泉, 等. 有机小分子化合物解离常数pKa的预测研究进展[J].科学通报, 2015, 60(14): 1261-1271.

## 第16章 分子从头生成

### 16.1 先导化合物的优化

发现的先导化合物不能立刻进入临床是因为先导化合物可能会存在化学结构不理想、与靶点结合性弱或者生物活性弱等问题。需要对先导化合物进行化学修饰，使其能够达到预期的药理作用进而使之优化发展为理想的药物，我们把这一过程称为“先导化合物的优化”。

在进行先导化合物优化之前，需要明确两点：一是要根据预期的药理明确优化目的，确定修饰目标，制定合适的优化路线，同时要保证具体问题具体分析；二是对于要优化的先导化合物的性质、特点及目前存在的问题有充分的了解。

先导化合物优化时要注意以下3点。

- (1) 最小修饰原则，意思是在优化先导化合物的时候，优先选择与先导化合物分子结构接近的类似物，或是只要结构轻微改变就能改变化合物性质的化合物。
- (2) 改善药理活性和化合物的ADMET性质。
- (3) 经济学评估。先导化合物的优化除了要考虑知识产权问题，还要从合成路线是否简便、合成原料或中间体是否价廉和易得等方面进行综合考虑。

随着计算机技术的发展，分子生成技术也有很大的发展，形成了传统组合药物化学方法和基于人工智能的从头生成方法。
