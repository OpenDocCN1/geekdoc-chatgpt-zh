```
```python
return x / y
return list(map(f,input_list))

#主函数入口
if __name__ == "__main__":
    try:
        con=pymysql.connect(**config)#连接数据库
        with con.cursor() as cursor:
            query="SELECT * FROM credit_management.distinct_company_
list;"
            cursor.execute(query)
            result=cursor.fetchall()#抓取需要下载财务数据的公司列表
    finally:
        con.close

    distinct_company_list=pd.DataFrame(result)

    balance=pd.DataFrame(np.copy(distinct_company_list),columns=
['CODE','COMP_NAME'])
    cashflow=pd.DataFrame(np.copy(distinct_company_list),columns=
['CODE','COMP_NAME'])
    income=pd.DataFrame(np.copy(distinct_company_list),columns=
['CODE','COMP_NAME'])

    for year in range(2012,2017):
        date=str(year)+"1231"
        print(date)
        try:
            con=pymysql.connect(**config)
            with con.cursor() as cursor:
                query="SELECT * FROM credit_management.balance WHERE
rptdate='"+date+"';"
                cursor.execute(query)
                result=cursor.fetchall()#从数据库中抓取资产负债表数据
                balance_x=pd.DataFrame(result)
                del balance_x["rptdate"]
                balance=pd.merge(balance, balance_x, how='left', on=
['CODE','COMP_NAME'])

                query=" SELECT * FROM credit_management.cashflow
WHERE rptdate='"+date+"';"
                cursor.execute(query)
```
```