```
codes=code_list[0]

for code in code_list[1:]:
    codes=codes+','+code
#使用获取的违约债券代码,抓取公司名称
w_data=w.wss(codes, "comp_name")

comp_name_data=pd.DataFrame()
comp_name_data['CODE']=w_data.Codes
comp_name_data['COMP_NAME']=w_data.Data[0]
comp_name_data['TARGET']=1 #将违约公司标记为 1
#因存在一个公司发行多只债券的情况,为了避免重复获取数据,先删除重复的公司
unique_comp=comp_name_data.drop_duplicates(['COMP_NAME'])#删除重名的公司,只保留第一个
unique_comp.index=range(len(unique_comp))
#抓取所有民营企业债券和公司
bond_data=w.wset("sectorconstituent","date=2018-04-01;sectorid=1000004555000000")#获取所有民营企业信用债
#bond_code_dup=pd.DataFrame()
#bond_code_dup['CODE']=bond_data.Data[1]
#bond_code_no_dup=bond_code_dup.drop_duplicates()

bond_list=bond_data.Data[1]
bonds=bond_list[0]
#获取非违约民营企业债券的代码
for bond in bond_list[1:]:
    bonds=bonds+','+bond
#使用民营企业债券的代码,抓取公司名称
w_data=w.wss(bonds, "comp_name")

bond_name_data=pd.DataFrame()
bond_name_data['CODE']=w_data.Codes
bond_name_data['COMP_NAME']=w_data.Data[0]
bond_name_data['TARGET']=0 #将正常公司标记为 0

#因存在一个公司发行多只债券的情况,为了避免重复获取数据,先删除重复的公司
unique_bond=bond_name_data.drop_duplicates(['COMP_NAME'])#删除重名的公司,只保留第一个
unique_bond.index=range(len(unique_bond))
```