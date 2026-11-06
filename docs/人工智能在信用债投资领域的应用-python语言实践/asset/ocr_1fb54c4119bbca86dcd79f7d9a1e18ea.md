```
if len(drop_list)>0:
    for i in range(len(drop_list)):
        del preselected_set[drop_list[i]]

selected_name_list=list(preselected_set)

for i in range(len(selected_name_list)):
    final_name_list.append(selected_name_list[i])

final_name_list.append('isDefault')
selected_set=complete_set.loc[:,final_name_list]

return selected_set

#返回选中的人模指标
def list_select(input_set,column_name,select_list):
    output_set=pd.DataFrame(columns=list(input_set))
    for i in range(len(input_set)):
        if input_set[column_name].iloc[i] in select_list:
            output_set=output_set.append(input_set.iloc[i,:])
    return output_set

#计算各人模指标的同行业、趋势、波动率评分表
def calculate_factors(selected_set,year_end):
    #year_end=2016
    year_mid=year_end -1
    year_start=year_end -2
    rptDate_start=str(year_start) +'1231'
    rptDate_mid=str(year_mid) +'1231'
    rptDate_end=str(year_end) +'1231'

    data_set_start=selected_set[selected_set['rptDate'] ==rptDate_start]
    data_set_mid=selected_set[selected_set['rptDate'] ==rptDate_mid]
    data_set_end=selected_set[selected_set['rptDate'] ==rptDate_end]

    code_1=list(data_set_start.CODE)
    code_2=list(data_set_mid.CODE)
    code_3=list(data_set_end.CODE)
```