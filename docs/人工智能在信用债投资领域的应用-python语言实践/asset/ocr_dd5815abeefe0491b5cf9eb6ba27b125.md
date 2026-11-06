```
```python
test_data=test_data.T
test_data.columns=["2014 年财务数据","2015 年财务数据","2016 年财务数据"]
factor_title=pd.DataFrame({'type':type_list,'factor_names':factor_names},columns=['type','factor_names'])
row_to_write=1
wb=openpyxl.Workbook()
ws1=wb.active
ws1.title='定量评分'
write_dataframe_to_excel(ws1,factor_title,start_col=1,start_row=2,index=False,columns=False)
write_dataframe_to_excel(ws1,test_data,start_col=3,start_row=1,index=False,columns=True)
row_to_write +=len(factor_title)+2
#得分模块#
ws1["A% d" % row_to_write].value='得分'
row_to_write +=2
#同行业
ws1["A% d" % row_to_write].value='同行业'
ws1["F% d" % row_to_write].value='得分'
row_to_write +=1
write_dataframe_to_excel(ws1,factor_title,start_col=1,start_row=row_to_write,index=False,columns=False)
for i in range(len(factor_title)):
    rw=row_to_write+i
    formula="=IF(同行业!$ C"+str(i+3)+"<同行业!$ L"+str(i+3)+", IF(E"+str(i+3)+"<同行业!$ C"+str(i+3)+",0,IF(E"+str(i+3)+">同行业!$ L"+str(i+3)+",100,OFFSET(同行业!$ B$ 2,0,MATCH(E"+str(i+3)+", 同行业!$ C"+str(i+3)+":$ L"+str(i+3)+")))+",IF(E"+str(i+2)+">同行业!$ C"+str(i+3)+",0,IF(E"+str(i+2)+"<同行业!$ L"+str(i+3)+",100,OFFSET(同行业!$ B$ 2,0,MATCH(E"+str(i+2)+",同行业!$ C"+str(i+3)+":$ L"+str(i+3)+",-1))))"
    ws1["F% d" % rw].value=formula
row_stamp_l=row_to_write
row_to_write +=len(factor_title)+1
```
```