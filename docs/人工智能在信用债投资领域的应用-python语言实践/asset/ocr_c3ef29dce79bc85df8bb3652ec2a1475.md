```
```python
set_start[col_name].iloc[i])/abs(data_set_start[col_name].iloc[i])

    output_set[trend_name]=temp

#计算波动性 (Volitility)
for col_name in col_list:
    volitility_name=col_name + '_V'
    temp=[]

    for i in range(len(data_set_end)):
        Sdv=np.std([data_set_start[col_name].iloc[i],data_set_mid[col_name].iloc[i],data_set_end[col_name].iloc[i]])
        absMean=abs(np.mean([data_set_start[col_name].iloc[i],data_set_mid[col_name].iloc[i],data_set_end[col_name].iloc[i]]))

        if absMean ==0:
            absMean=0.0001

        temp.append(Sdv/absMean)

    output_set[volitility_name]=temp

output_set['isDefault']=isDefault

return output_set

#取分位数
def quantile(data_list,seq_num=4):
    quantile_list=[]
    for i in range(seq_num+1):
        percentage=(i/seq_num)*100
        quantile_list.append(np.percentile(data_list,percentage))
    return quantile_list

#把数据写入 Excel 文件
def write_dataframe_to_excel(sheet,df,start_col=1,start_row=1,index=False,columns=True):
    # sheet=wsl
    column_list=['','A','B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z']
    writing_row=0
    for r in dataframe_to_rows(df, index=index, header=columns):
```
```