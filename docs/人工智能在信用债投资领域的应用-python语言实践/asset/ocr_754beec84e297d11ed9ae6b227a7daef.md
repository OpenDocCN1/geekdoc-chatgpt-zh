```
ws6["C38"].value='近 1 年来是否存在被小贷(民间借贷)公司起诉'
ws6["D38"].value='P10'
ws6["E38"].value='A.否'
ws6["E39"].value='B.是'

ws6["F38"].value='B'
ws6["G38"].value='=IF(F38="", "", VLOOKUP(F38, 数据!J2:K3, 2, FALSE))'
ws6["H38"].value='=数据!C40'
ws6["I38"].value='=G38 * H38'

ws6["C40"].value='招聘信息'
ws6["D40"].value='P11'
ws6["E40"].value='A.近 3 年招聘职位数>25'
ws6["E41"].value='B.5<近 3 年招聘职位数<=25'
ws6["E42"].value='C.0<近 3 年招聘职位数<=5'
ws6["E43"].value='D.近 3 年招聘职位数=0'

ws6["F40"].value='B'
ws6["G40"].value='=IF(F40="", "", VLOOKUP(F40, 数据!M2:N5, 2, FALSE))'
ws6["H40"].value='=数据!C41'
ws6["I40"].value='=G40 * H40'

ws6["H44"].value='机构定性得分：'
ws6["I44"].value='=SUM(I4:I43)'
```

# ##数据

```
ws7=wb.create_sheet('数据')

final_w=devide_list(final_w,100)

score_table=pd.DataFrame()
score_table['指标选项']=["A","B","C","D","E"]
score_table['得分']=[100,80,60,40,20]
write_dataframe_to_excel(ws7,score_table,start_col=1,start_row=1,index=False,columns=True)

score_table=pd.DataFrame()
score_table['管理层指标选项']=["A","B"]
score_table['得分']=[100,50]
```