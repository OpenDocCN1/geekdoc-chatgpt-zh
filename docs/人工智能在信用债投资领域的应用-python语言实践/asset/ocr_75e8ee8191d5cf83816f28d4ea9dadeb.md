```
for sign in [-1, 1]:
    wsTest=ws.copy()
    wsTest[jj] +=step * sign
    yTest=xMat * wsTest
    rssE=rssError(yMat.A, yTest.A)
    if rssE <lowestErr:
        lowestErr=rssE
        wsMax=wsTest
    ws=wsMax.copy()
    returnMat[ii, :]=ws.T
return returnMat
```

# ## 数据预处理及入模指标筛选

```
def index_select(industry,percentage):
    #industry="采矿业"
    #percentage=0.2
    #从数据库读取,指定行业的所有数据
    try:
        with con.cursor() as cursor:
            query="SELECT * FROM creditrisk.all_ratio_data WHERE industry_L1='"+industry+"';"
            cursor.execute(query)
            result=cursor.fetchall()
    finally:
        con.close
    #删除定性相关指标
    ratio_data=pd.DataFrame(result)
    del_list=['CITY','CITYINVESTMENTBONDGEO','MUNICIPALBOND',
              'FOUNDDATE','HOLDER_PCT','HOLDER_SHARECATEGORY',
              'LISTINGORNOT','NATURE','PROVINCE','REGCAPITAL',
              'SHAREHOLDERNATURE','industry_L1','industry_L2',
              'industry_L1_wind','industry_L2_wind','industry_L3_wind','industry_L4_wind']
    for col_name in del_list:
        del ratio_data[col_name]

    #df.head
    name_list=list(ratio_data)
    protect_list=['OCFTOQUICKDEBT','OCFTOINTEREST','OCFTOSHORTDEBT']
```