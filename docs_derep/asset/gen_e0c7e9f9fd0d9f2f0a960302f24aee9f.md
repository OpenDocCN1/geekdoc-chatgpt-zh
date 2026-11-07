[PRE0]

### 附录4 数据合并脚本

[PRE1]

数据转换处理

For i = 2 To UBound(srcData, 1) ' 跳过标题行

    For m = 2 To UBound(srcData, 2) ' 处理月份列

        If IsNumeric(srcData(i, m)) Then

            rowCnt = rowCnt + 1

            resData(rowCnt, 1) = ws.Name ' 部门 ← 工作表名称（需确保工作表已正确命名）

            resData(rowCnt, 2) = yrFlag ' 年份

            resData(rowCnt, 3) = monthNames(m - 2) ' 月份名称

            resData(rowCnt, 4) = srcData(i, 1) ' 项目

            resData(rowCnt, 5) = srcData(i, m) ' 金额

        End If

    Next m

Next i

End If

Next ws

wb.Close False

File = Dir()

Loop

' 输出结果

With ThisWorkbook.Sheets.Add(After:=Sheets(Sheets.Count))

    .Name = "合并数据"

    .Range("A1:E1") = Array("部门", "年份", "月份", "项目", "金额")

    If rowCnt > 0 Then

        .Range("A2").Resize(rowCnt, 5).Value = resData

        .Columns("E").NumberFormat = "#,##0" ' 金额

        .ListObjects.Add(xlSrcRange, .Range("A1").CurrentRegion).Name = "tblData"

    End If

    .Columns.AutoFit

End With

Application.ScreenUpdating = True

MsgBox "合并完成! 有效记录: " & rowCnt & " 条"

End Sub

## 排版信息

策划编辑：王传芳

责任编辑：杨静华

## DeepSeek

赋能提速

数据分析

DeepSeek带你：以逻辑驾驭数据，以工具解放双手

数据分析的本质并非堆砌工具技巧，而是逻辑思维与业务认知深度融合的产物。本书围绕“赋能”与“增效”两条主线展开，系统性地阐释如何借助 DeepSeek 实现：

- 1. 思维重塑：从指标拆解、归因剖析、业务流程再造到模型搭建，培育面向业务的分析思维，构建企业的精准业务雷达。
- 2. 效率跃迁：借助自然语言指令完成数据清洗、自动关联多源数据生成动态业务画像、一句话生成附带结论的可视化汇报。
- 3. 实战提升：覆盖用户增长、供应链优化、企业内部资源精细化等场景，提供可直接复用的分析范式。让 DeepSeek 充当你的“数据分析外脑”，彻底摆脱无效加班，聚焦价值创造，赢得业务侧的认可！

[PRE2]

微信号:Waterpub-Pro

唯一官方微信服务平台

销售分类：计算机与互联网/人工智能/办公软件

[PRE3]

中国水利水电出版传媒集团有限公司

ISBN 978-7-5226-3486-9

定价：79.80元