+   5. # 把需要预测的目标 y 单独提取出来用于后续的特征选择
+   6. y = supervised_df['y']
+   7. # 从数据中删除目标 y 后，以 SKU 字段为 ID、order_date 字段为顺序参照，自动化提取特征
+   8. supervised_df = supervised_df.drop('y',axis=1).stack().reset_index().rename(columns={'level_1':'sort_val',0:'val'})
+   9. extracted_features = extract_features(supervised_df, column_id= "SKU |order_date", column_sort = 'sort_val')
+   10. # 用 impute 先进行插值
+   11. impute(extracted_features)
+   12. # 用 select_features 选择与 y 相关性最高的特征
+   13. features_filtered = select_features(extracted_features, y)
+   14. print(extracted_features)

# 表 1-10 使用 tsfresh 提取的特征（extracted_features 部分示例）

| 日期 | val_has_duplicatemin | val_abs_energy | val_mean_abs_change | val_mean_change |
|---|---|---|---|---|
| 2019/1/1 | 5911.71 | 3038465 | 131.09 | -7.82 |
| 2019/2/1 | 5842.97 | 2958236 | 128.05 | -9.54 |
| 2019/3/1 | 5849.13 | 2964182 | 113.97 | 14.30 |
| 2019/4/1 | 6078.54 | 3235157 | 117.84 | -21.89 |
| 2019/5/1 | 6501.71 | 3760757 | 189.69 | -27.67 |
| 2019/6/1 | 6789.54 | 4099286 | 141.22 | -44.97 |
| ... | ... | ... | ... | ... |
| 2019/7/1 | 7010.24 | 4431484 | 173.72 | -24.58 |

# 1.2.3 预测实践

在模型预测环节，我们需要基于业务背景知识和前期的探索性分析，选择适合的模型来完成预测任务。在本节的实践案例中，我们将介绍三种较为通用的经典模型，分别是指数平滑模型、ARIMA 和 XGBoost 模型，通过代码快速完成一次时间序列的建模和预测。需要注意的是，在实践中没有哪种模型是普遍适用的，这就要求我们对模型的原理和特点有一定的了解，根据预测任务的实际情况选择最合适的模型。在深入学习模型原理之前，让我们先对如何应用这些模型有一个直观的感受，而有关模型的数学原理将在后面的章节中系统地讲解。

在本次快消品 SKU 预测实践中，我们将使用 2018 年 1 月 1 日至 2021 年 12