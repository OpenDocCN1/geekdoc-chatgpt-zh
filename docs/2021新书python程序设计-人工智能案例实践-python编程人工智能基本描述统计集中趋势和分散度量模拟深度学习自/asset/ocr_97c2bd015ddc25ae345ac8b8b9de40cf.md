```
In [20]: pd.read_sql('SELECT id, first, last FROM authors',
        ...:              connection, index_col=['id'])
        ...:
Out[20]:
            first   last
id
1    Paul    Duitez
2    Harvey    Deitel
3    Abbey    Deitel
4    Dan    Quirk
5    Alexander    Wald
6    Sue    Red
```

# 关于包含单引号的字符串的说明

SQL使用单引号（'）区分字符串。对于自身包含单引号的字符串（例如O'Malley），必须在单引号出现的位置使用两个单引号来代替（例如，'O"Malley'），相当于第一个单引号充当第二个单引号的转义字符。对SQL语句中的字符串的单引号字符不进行转义会引发SQL语法错误。

# 16. 2. 7 UPDATE语句

UPDATE语句用于修改现有值。下面假设Sue Red的last name在数据库中不正确，需将其更新为'Black'：

```
In [21]: cursor = cursor.execute("""UPDATE authors SET last='Black'
...:                         WHERE last='Red' AND first='Sue""")
```

UPDATE关键字后跟着要更新的表名、关键字SET和以逗号分隔的column_name=value对，指示要更改的列及其新值。如果未指定WHERE子句，则将更改所有行。此查询中的WHERE子句表示只更新last name为“Red”，first name为“Sue”的行。

当然，可能有多个人具有相同的first name和last name。如果仅更改一行，最好在WHERE子句中使用行的唯一主键。在这种情况下，可以指定：

```
WHERE id = 6
```

对于修改数据库的语句，Cursor对象的rowcount属性记录一个整数值，表示已修改的行数。如果此值为0，则表明没有进行任何更改。以下确认UPDATE确实修改了一行：