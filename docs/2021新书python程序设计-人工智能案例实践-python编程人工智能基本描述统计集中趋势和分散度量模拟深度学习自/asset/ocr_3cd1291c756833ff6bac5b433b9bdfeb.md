# 3.9 边界值控制的迭代

现在，对求班级平均分问题进行扩展。以下是需求声明：

开发一个计算班级平均分的程序，该程序每次执行时可以处理任意数量的成绩。

需求声明没有说明成绩是什么或者是多少，所以我们会让用户自行输入成绩。该程序要处理任意数量的成绩。用户每次输入一个成绩，直到输入所有成绩，然后输入边界值（也称为信号值、虚拟值或标记值）以表示不再需要输入成绩。

# 应用边界值控制的迭代

下面的脚本使用边界值控制的迭代解决求班级平均分问题。需要注意的是，脚本中应该包含测试是否除以零的语句。如果漏检，可能会出现致命的逻辑错误。在第9章中，我们编写了能够识别此类异常并采取适当措施的程序。

```
# class_average_sentinel.py
"""Class average program with sentinel-controlled iteration."""
# initialization phase
total = 0 # sum of grades
grade_counter = 0 # number of grades entered
# processing phase
grade = int(input('Enter grade, -1 to end: ')) # get one grade
while grade != -1:
    total += grade
    grade_counter += 1
    grade = int(input('Enter grade, -1 to end: '))
# termination phase
if grade_counter != 0:
    average = total / grade_counter
    print(f'Class average is {average:.2f}')
else:
    print('No grades were entered')
```

![](bbox=[0.2, 0.77, 0.8, 0.83])

# 边界值控制的迭代的程序逻辑