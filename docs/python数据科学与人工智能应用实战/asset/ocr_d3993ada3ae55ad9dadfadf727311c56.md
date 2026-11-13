上述运行结果是 0，表示已经没有遗漏值。在完成补值后，计算不同性别人数和男女的平均年龄，如下所示：

```
print("性别人数:")
print(titanic["Sex"].groupby(titanic["Sex"]).size())
print(titanic.groupby("Sex")["Age"].mean())
print(titanic.groupby("Sex")["Age"].mean())
```

上述程序代码调用 groupby() 函数来群组 Sex 字段，首先计算各群组的人数，然后计算 Age 字段的平均值。其运行结果如下所示：

![](bbox=[0.09, 0.27, 0.94, 0.45])

上述运行结果显示女性有 462 人，平均年龄是 29.77；男性有 851 人，平均年龄是 30.7。在数据集的 Name 字段值中包含 Miss、Mrs 和 Mr 等信息，可以使用正则表达式取出这些数据来新增 Title 字段，如下所示：

```
import re
patt = re.compile(r"\, \s(\S+\s)")
titles = []
for index, row in titanic.iterrows():
    m = re.search(patt, row["Name"])
    if m is None:
        title = "Mrs" if row["SexCode"] == 1 else "Mr"
    else:
        title = m.group(0)
        title = re.sub(r"", "", title).strip()
        if title[0] != "M":
            title = "Mrs" if row["SexCode"] == 1 else "Mr"
        else:
            if title[0] == "M" and title[1] == "a":
                title = "Mrs" if row["SexCode"] == 1 else "Mr"
    titles.append(title)
titanic["Title"] = titles
```