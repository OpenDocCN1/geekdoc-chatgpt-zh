![](bbox=[0.1, 0.06, 0.92, 0.33])

图 8.2 导出违约和负面事件列表

```
#标记实质性违约样本
def mark_default(preprocessed_set):
    #读取图 8.2 中导出的三张报表,此处假设这三张报表的存放位置为 D:/Temp/风控模型
    default_list_1=readwb("D:/Temp/风控模型/负面事件报表.xlsx","Wind资讯")
    default_list_1=pd.DataFrame(default_list_1[1:(len(default_list_1)-2)],columns=default_list_1[0])
    default_list_2=readwb("D:/Temp/风控模型/违约兑付报表.xlsx","Wind资讯")
    default_list_2=pd.DataFrame(default_list_2[1:(len(default_list_2)-2)],columns=default_list_2[0])
    default_list_3=readwb("D:/Temp/风控模型/违约债券报表.xlsx","Wind资讯")
    default_list_3=pd.DataFrame(default_list_3[1:(len(default_list_3)-2)],columns=default_list_3[0])
    default_list_sub_1=pd.DataFrame({'COMP_NAME':default_list_1['发行人'],'Date':default_list_1['发生日期']},columns=['COMP_NAME','Date'])
    default_list_sub_2=pd.DataFrame({'COMP_NAME':default_list_2['发行人全称'],'Date':default_list_2['付息日']},columns=['COMP_NAME','Date'])
    default_list_sub_3=pd.DataFrame({'COMP_NAME':default_list_3['发行人'],'Date':default_list_3['发生日期']},columns=['COMP_NAME','Date'])
```