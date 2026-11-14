| SQL 关键字 | 描述 |
| :--- | :--- |
| SELECT | 从一个或多个表中检索数据 |
| FROM | 查询中涉及的表，每个 SELECT 都需要 |
| WHERE | 选择标准，用于确定要检索、删除或更新的行。在 SQL 语句中可选 |
| GROUP BY | 对行进行分组的标准，SELECT 查询中的可选项 |
| ORDER BY | 对行进行排序的标准，SELECT 查询中的可选项 |
| INNER JOIN | 合并多个表中的行 |
| INSERT | 将行插入指定的表 |
| UPDATE | 更新指定表中的行 |
| DELETE | 删除指定表中的行 |

# 16. 2. 2 SELECT查询

上一节使用SELECT语句和*通配符来获取表中的所有列。但大多情况下，尤其是在包含成百上千个列的大数据中，只需要列的一部分。要检索特定的列，需要指定以逗号分隔的列名的列表。例如，从authors表中检索first列和last列：

```
In [9]: pd.read_sql('SELECT first, last FROM authors', connection)
Out[9]:
    first     last
0   Paul     Deite1
1  Harvey    Deite1
2   Abbey    Deite1
3     Dan     Quirk
4  Alexander     Wald
```

# 16. 2. 3 WHERE子句

通常会在数据库中选择满足某些选择条件的行，尤其是在数据库可能包含数百万或数十亿行的大数据中，即仅选择满足选择标准的行（称为谓词）。SQL的WHERE子句指定查询的选择条件。例如，需检索title表中copyright年份大于2016的所有图书的title、edition和copyright。SQL查询中的字符串值由单引号分隔，如'2016':