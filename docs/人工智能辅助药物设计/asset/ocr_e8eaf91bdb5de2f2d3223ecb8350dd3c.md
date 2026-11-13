在清单15-12中，使用MolecularDescriptorCalculator()创建一个计算描述符对象calc，该对象能够查询分子的所有属性值。header变量中保存calc对象能够查询到的属性标签，它在最终的特征表中作为表头。利用calc对象计算df表中ROMol列，将查询结果保存在列表rdkit_2d_desc中，然后将查询属性标签header和查询结果组合成一个特征表df_final并在表的最后添加一列属性New_pKa。在特征表df_final作为数据集前，还需要检查表中是否有空值存在，如果有，则去除该行数据。程序运行结果如图15-9所示。

# 清单15-12 提取特征

```
1. rdkit_2d_desc = []
2. calc = MolecularDescriptors.MolecularDescriptorCalculator([x[0] for x in Descriptors._descList])
3. header = calc.GetDescriptorNames()
4. for i in range(len(df)):
5. ds = calc.CalcDescriptors(df[ "ROMol "][i])
6. rdkit_2d_desc.append(ds)
7. df_final = pd.DataFrame(rdkit_2d_desc,columns=header)
8. df_final=pd.concat([df_final,df[ "New_pKa " ]],axis=1)
9. is_NaN = df_final.isnull()
10. row_has_NaN = is_NaN.any(axis=1)
11. rows_with_NaN = df_final[row_has_NaN]
12. df_final.drop(index=rows_with_NaN.index,inplace=True)
13. print(df_final.head())
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

# 清单15-13 划分数据集

```
1. X=df_final.values[:,0:-1]
2. y=df_final.values[:,-1]
3. scaler=MinMaxScaler()
4. X=scaler.fit_transform(X)
5. X_train1, X_test1, y_train, y_test = train_test_split(X, y, test_size

=0.2, random_state=101)
```