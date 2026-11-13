上述程序代码建立正则表达式的模板字符串后，使用 for 循环访问 DataFrame 对象的每一行，以便使用模板字符串取出 Title 字段值，因为有不完整的数据，所以用 if/else/if 条件判断这些异常情况，删除多余字符并依据 SexCode 字段判断男性是 Mr，女性是 Mrs，最后新增 Title 字段。
在成功新增 Title 字段后，需要再次确认 Title 字段取出的类别种类，如下所示：

```
print("Title 类别:")
print(np.unique(titles).shape[0], np.unique(titles))
```

上述程序代码显示 Title 字段的类别有几种，其运行结果如下所示：

```
Title 类别:
5 ['Miss' 'Mlle' 'Mr' 'Mrs' 'Ms']
```

上述执行结果显示多出 Mlle 和 Ms 两类，所以准备修正这两个错误，如下所示：

```
titanic["Title"] = titanic["Title"].replace("Mlle", "Miss")
titanic["Title"] = titanic["Title"].replace("Ms", "Miss")
titanic.to_csv("titanic_pre.csv", encoding="utf-8")
```

上述程序代码调用 replace() 函数取代 Mlle 和 Ms 成为 Miss，数据集的数据预处理完成，最后将其导出为 titanic_pre.csv 的 CSV 文件。

现在，乘客已经可以区分为先生、女士或小姐，下面计算各类别的人数和生存率，如下所示：

```
print("Title 人数:")
print(titanic["Title"].groupby(titanic["Title"]).size())
print("平均生存率:")
print(titanic[["Title", "Survived"]].groupby(titanic["Title"]).mean())
```

上述程序代码使用 Title 字段群组数据，可以计算各类别的人数和生存率，即 Survived 字段的平均值。其运行结果如下所示：

```
Title 人数:
Title
Miss    250
Mr      851
Mrs     212
Name: Title, dtype: int64
平均生存率:
             Survived
Title
Miss   0.604000
Mr     0.166863
Mrs    0.740566
```