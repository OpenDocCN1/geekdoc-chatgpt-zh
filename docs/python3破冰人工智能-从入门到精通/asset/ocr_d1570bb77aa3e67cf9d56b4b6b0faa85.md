输入：

```
gender_workclass = round(pd.crosstab(adult.workclass, [adult.income, adult.gender]).div
(pd.crosstab(adult.workclass, [adult.income, adult.gender]).
    apply(sum,1),0),2)

gender_workclass[[('>50k','Male'), ('>50k','Female')]].plot(
    kind = 'bar', title = 'Proportion distribution across gender for each workclass',
    figsize = (10,8), rot = 30)
ax.set_xlabel('Gender level') /
ax.set_ylabel('Proportion of population')
plt.show()
```

输出：

![](bbox=[0.18, 0.32, 0.78, 0.6])

▲ 图 7-8 例 7-7.12 程序运行结果

通过绘制这 3 个类别的条形图，如图 7-8 所示，年收入超过 50k 美元的人口比例中，女性有增加的趋势。

# 7.2.2 小结

本章的内容相对简单，但这些知识足以让你大致了解数据分析的过程，以及利用 Python 开始数据分析任务。只有懂得获取数据以及将数据进行提取与分析，才能完成更为复杂、精妙的分析。希望通过本章节所介绍的技术，你能够立马开展自己的分析工作。同时，本章中选取的数据集都是机器学习中标准的测试数据集，对你后期的机器学习任务同样适用。