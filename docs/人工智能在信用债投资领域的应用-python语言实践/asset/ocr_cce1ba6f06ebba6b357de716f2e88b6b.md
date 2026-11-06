```
ws6["E30"].value='B.近 3 年有发生法人变更'

ws6["F29"].value='B'
ws6["G29"].value=' = IF (F29="", "", VLOOKUP (F29, 数据! D2:E3, 2, FALSE))'
ws6["H29"].value=' =数据!C36'
ws6["I29"].value=' =G29 * H29'

ws6["C31"].value='近 1 年来被执行和失信被执行次数'
ws6["D31"].value='P7'
ws6["E31"].value='A.0 次'
ws6["E32"].value='B.1 次'
ws6["E33"].value='C.1 次以上'

ws6["F31"].value='B'
ws6["G31"].value=' = IF (F31="", "", VLOOKUP (F31, 数据!G2:H4, 2, FALSE))'
ws6["H31"].value=' =数据!C37'
ws6["I31"].value=' =G31 * H31'

ws6["C34"].value='近 1 年来是否存在被银行冻结存款或起诉欠款'
ws6["D34"].value='P8'
ws6["E34"].value='A.否'
ws6["E35"].value='B.是'

ws6["F34"].value='B'
ws6["G34"].value=' = IF (F34="", "", VLOOKUP (F34, 数据!J2:K3, 2, FALSE))'
ws6["H34"].value=' =数据!C38'
ws6["I34"].value=' =G34 * H34'

ws6["C36"].value='近 1 年来是否存在被股东起诉'
ws6["D36"].value='P9'
ws6["E36"].value='A.否'
ws6["E37"].value='B.是'

ws6["F36"].value='B'
ws6["G36"].value=' = IF (F36="", "", VLOOKUP (F36, 数据!J2:K3, 2, FALSE))'
ws6["H36"].value=' =数据!C39'
ws6["I36"].value=' =G36 * H36'
```