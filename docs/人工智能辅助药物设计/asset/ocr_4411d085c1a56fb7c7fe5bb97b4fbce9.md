# ### 4.3 示例

本书将给出具体的决策树示例，以帮助读者更好地了解其实际应用。

# ### 1.决策树计算信息熵

首先，按照如下步骤构建决策树。

+   (1) 收集数据：一般需要收集包含属性和目标值的数据，数据来源可以为文献、已有数据库、科研中积累的数据。

+   (2) 准备数据：数据一般转变为数值类型，并且尽量进行标准化处理，即去除异常值。

+   (3) 分析数据：对数据进行分析，确定以哪些数据作为属性，以哪些数据作为目标，以及属性与目标之间是否具有相关性。

+   (4) 训练算法：使用已有的训练集对算法进行训练。

+   (5) 测试算法：在测试集上验证算法的准确性。

+   (6) 使用算法：将算法应用（泛化）到新的数据集上。

决策树的算法已包含在Scikit-learn软件包中，在此我们用Python编写构建决策树的代码。这里使用的数据集为表4-1中的17个样本。代码如清单4-1所示。

# ### 清单4-1 准备数据

```
1.  #!/usr/bin/python
2.  #encoding:utf-8
3.  # 将原始数据分为训练数据和测试数据
4.  import numpy as np
5.  from sklearn import tree,metrics
6.  import pydotplus
7.  def gender(s):
8.      it = {'M':0, 'F':1}
9.      return it[s.decode(" utf-8 ")]
10. def BP(s):
11.     it = {'HIGH':0, 'NORMAL':1, 'LOW':2}
12.     return it[s.decode(" utf-8 ")]
13. def Cholesterol(s):
14.     it = {'HIGH':0, 'NORMAL':1, 'LOW':2}
15.     return it[s.decode(" utf-8 ")]
16. def drug_type(s):
17.     it = {'drugA':0, 'drugB':1}
18.     return it[s.decode(" utf-8 ")]
19.     drug_feature_E = 'Age', 'Gender', 'BP', 'Cholesterol',
20.     'Na_to_K'
21.     drug _class = 'drugA', 'drugB'
21. # 读入数据，并将原始数据中的数据转换为数字形式
22. data = np.loadtxt(" drug_decision.csv ", delimiter=",",
dtype=str,
```