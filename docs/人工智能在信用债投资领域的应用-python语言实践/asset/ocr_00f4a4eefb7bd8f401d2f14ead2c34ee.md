```
```python
else:
    dictionary.类型[i]=factor_type
#生成 Excel 版本的评分卡模型,并保存到计算机
wb = construct _ scorecard (selected _ set, factors _ set, boundarys,
importance,dictionary,final_w,industry)
wb.save(' D:/Temp/风控模型/wbtest.xlsx')
#评估所有样本,并将结果保存到 Excel 中
rankings= company _scoring _and _ranking (factors _set _1,boundary s,
importance,dictionary,final_w,qualitative_data)
rankings_wb=openpyxl.Workbook()
ws=rankings_wb.active
write_dataframe_to_excel (ws,rankings,start_col=1,start_row=1,
index=False,columns=True)
rankings_wb.save(' D:/Temp/风控模型/'+industry+'_rankings.xlsx')
```
```

综上,本章重点介绍的是通过调整模型结构来缓解财务粉饰对评估企业信用风险影响的实用方法。对于深入研究并实操过资本市场财务数据统计分析工作的读者来说,肯定会有异常发现,如盈利率状况跟违约状态成正比,即盈能力越强,违约风险越大。虽然这种正相关的系数很小,但毕竟是大于零的正相关系数,也是严重的异常结果。

面对这种情况普遍存在的现状下,很多科学、专业的模型开发方法基本都是不适用的,作为普通的信用债投资者我们无力改变现状,只能改变信用债投资分析的架构和方法,尽量减少财务粉饰对挖掘企业真实还款能力的影响。此处介绍的方法,是笔者多年的实践经验总结,笔者也相信有更好的方法,欢迎广大读者来信交流。