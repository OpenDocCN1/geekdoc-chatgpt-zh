```
write_dataframe_to_excel(ws7,score_table,start_col=4,start_row
=1,index=False,columns=True)

score_table=pd.DataFrame()
score_table['司法指标选项-1']=["A","B","C"]
score_table['得分']=[100,50,0]
write_dataframe_to_excel(ws7,score_table,start_col=7,start_row
=1,index=False,columns=True)

score_table=pd.DataFrame()
score_table['司法指标选项-2']=["A","B"]
score_table['得分']=[100,0]
write_dataframe_to_excel(ws7,score_table,start_col=10,start_
row=1,index=False,columns=True)

score_table=pd.DataFrame()
score_table['招聘指标选项']=["A","B","C","D"]
score_table['得分']=[100,75,50,25]
write_dataframe_to_excel(ws7,score_table,start_col=13,start_
row=1,index=False,columns=True)

ws7["A9"].value='#####目标层---准则层,权重的确定。#####'
ws7["C10"].value='权重'
weigth_table=pd.DataFrame()
weigth_table["a"]=["A1","A2"]
weigth_table["b"]=["产业属性","公司属性"]
weigth_table["c"]=["20% ","80% "]
write_dataframe_to_excel(ws7,weigth_table,start_col=1,start_
row=11,index=False,columns=False)

ws7["A14"].value='####重要性判别矩阵####'
input_dir=" D:/Temp/风控模型/AHP-"+industry+"-重要性判断矩阵
1.csv"
weigth_table=read_csv(input_dir)
weigth_table=pd.DataFrame(weigth_table,columns=["A1","A2"],
index=["A1","A2"])
write_dataframe_to_excel(ws7,weigth_table,start_col=1,start_
row=15,index=True,columns=True)

ws7["A20"].value='#####准则层 A1---指标层 P,权重的确定#####'
```