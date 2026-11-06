```
_inc,tot_compreh_inc,tot_compreh_inc_min_shrhldr,tot_compreh_inc_parent_comp',wind_args)
    print(codes)
    if w_wss_data.ErrorCode !=0:
        print(w_wss_data)
        break;
    mm=np.mat(w_wss_data.Data)
    df=pd.DataFrame(mm.T, columns=w_wss_data.Fields)
    df['CODE']=w_wss_data.Codes
    df['rptDate']=[rptdate for j in range(len(code_list))]
    df.to_sql(name='income', con=engine, if_exists='append',
index=False, index_label=False)
    # index_set=index_set.append(df)
    # index_set.index=range(len(index_set))
    index=0
```

```
#抓取所有财务数据(资产负债表、现金流量表、利润表)并存到数据库中
w.start()
engine= create_engine ("mysql+pymysql://test:admin@ 10.8.16.210:
3306/creditrisk?charset=gbk")
distinct_company_list=pd.read_sql_table('distinct_company_list',
con=engine)
company_codes=list(distinct_company_list.CODE)#获取在第一节中抓取的
债券代码
get_balance(company_codes)#抓取这些债券的资产负债表会计科目中的所有数
据,并存到数据库中,数据库表名为 balance
get_cashflow(company_codes)#抓取这些债券的现金流量表会计科目中的所有数
据,并存到数据库中,数据库表名为 cashflow
get_income(company_codes)#抓取这些债券的利润表会计科目中的所有数据,并
存到数据库中,数据库表名为 income
w.stop()#关闭 WindPy 插件
```

第二部分,本福特法则的统计计算,Python 代码如下所示。

```
#加载本福特法则统计计算所需的 Python 包
import pandas as pd
import numpy as np
import time
import pymysql
#设置时间的起点,以便于计算程序运行的时间
time_start=time.time()
#数据库配置,以抓取存储在数据库中的财务数据
config={
```