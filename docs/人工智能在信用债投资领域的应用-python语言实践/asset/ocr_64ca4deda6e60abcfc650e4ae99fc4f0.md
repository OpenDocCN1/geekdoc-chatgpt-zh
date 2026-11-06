```
ws6["J3"].value='说明'

ws6["A4"].value='机构定性评分 F'
ws6["B4"].value='产业属性 A1'

ws6["C4"].value='经济政策影响'
ws6["D4"].value='P1'
ws6["E4"].value='A.良好的宏观经济环境,国家有政策支持,行业发展可持续增长'

ws6["E5"].value='B.稳定的宏观经济环境,国家有政策支持,行业发展稳定'
ws6["E6"].value='C.比较稳定的宏观经济环境,基本不受政策影响,行业发展有小幅波动'

ws6["E7"].value='D.较为波动的宏观经济环境,受政策影响较大,行业发展有较大幅波动'

ws6["E8"].value='E.较大波动的宏观经济环境,受政策影响很大,行业发展有剧烈波动'

ws6["F4"].value='B'
ws6["G4"].value='=IF(F4="", "", VLOOKUP(F4,数据!$A$2:$B$6,2,FALSE))'
ws6["H4"].value='=数据!C22'
ws6["I4"].value='=G4 * H4'

ws6["C9"].value='经济周期性'
ws6["D9"].value='P2'
ws6["E9"].value='A.受经济周期的影响很小'
ws6["E10"].value='B.受经济周期的影响较小'
ws6["E11"].value='C.受经济周期的影响一般'
ws6["E12"].value='D.受经济周期的影响较大'
ws6["E13"].value='E.受经济周期的影响很大'

ws6["F9"].value='B'
ws6["G9"].value='=IF(F4="", "", VLOOKUP(F4,数据!$A$2:$B$6,2,FALSE))'
ws6["H9"].value='=数据!C22'
ws6["I9"].value='=G4 * H4'

ws6["B14"].value='产业属性 A1'

ws6["C14"].value='公司性质'
ws6["D14"].value='P3'
ws6["E14"].value='A.中央国有企业'
```