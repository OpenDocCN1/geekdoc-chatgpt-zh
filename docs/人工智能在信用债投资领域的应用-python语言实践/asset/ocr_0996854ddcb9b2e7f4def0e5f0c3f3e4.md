指标尤其是比率型的指标趋势变化同质化。因此，此处我们采取的缺失值填充策略是根据企业的营业收入,分为四分位数。即根据企业的营业收入,分为大型公司、中大型公司、中型公司、小型公司,如果一个存在缺失值的样本属于大型公司,则用大型公司中所有非缺失值样本的平均数来填充该缺失指标,以此类推。

```
#填充缺失值
try:
    with con.cursor() as cursor:
        query="SELECT COMP_NAME, TOT_OPER_REV AS oper_rev, rptDate FROM creditrisk.all_ratio_data;"
        cursor.execute(query)
        result=cursor.fetchall()
finally:
    con.close
#读取企业营业收入
oper_rev=pd.DataFrame(result)
oper_rev=oper_rev.drop_duplicates()
comp_list=pd.DataFrame()
comp_list['CODE']=ratio_data.CODE
comp_list['COMP_NAME']=ratio_data.COMP_NAME
comp_list=comp_list.drop_duplicates()
oper_rev_list=pd.merge(comp_list,oper_rev,how='left',on='COMP_NAME')
rptDate_list=['20161231','20151231','20141231','20131231','20121231']

complete_set=pd.DataFrame()
count_nas=[0 for i in range(0,ratio_data.columns.size)]

for rptdate in rptDate_list:
    # rptdate='20161231'
    data_set = oper_rev_list[oper_rev_list['rptDate'] == str(rptdate)]
    data_set=data_set.fillna(-1)
    data_set=data_set[data_set['oper_rev'] != (-1)]
    #用分位数,按照公司的营业收入将公司分为大型、中大型、中型和小型公司
    boundarys=[data_set['oper_rev'].quantile(0),data_set['oper_rev'].quantile(0.25), data_set['oper_rev'].quantile(0.5),data_set['oper_rev'].quantile(0.75), data_set['oper_rev'].quantile(1)]
    #获取不同类型公司的边界
    part_1_list=data_set[data_set['oper_rev'] <=boundarys[1]]
    del part_1_list['oper_rev']
    part_2_list=data_set[data_set['oper_rev']>boundarys[1]]
```