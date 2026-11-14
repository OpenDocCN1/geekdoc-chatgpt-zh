| 表达式 1 | 表达式 2 | 表达式 1 and 表达式 2 |
|---|---|---|
| False | False | False |
| False | True | False |
| True | False | False |
| True | True | True |

# 布尔运算符or

布尔运算符or可以测试两个条件中的一个或两个是否为True。在下面的代码中，如果其中一个或两个简单条件为True时，整个测试条件为True，当且仅当两个简单条件均为False时，整个测试条件为False：

```
In [4]: semester_average = 83

In [5]: final_exam = 95

In [6]: if semester_average >= 90 or final_exam >= 90:
    ...:     print("Student gets an A")
    ...
Student gets an A
```

代码段[6]同样包含两个简单的条件：

+   - semester_average >= 90确定学生在学期内的平均成绩是否为 A (90或以上) ;
+   - final_exam>=90确定学生的期末考试成绩是否为 A。

运算符and的优先级高于or。下面的真值表总结了布尔运算符or的运算规则。

| 表达式 1 | 表达式 2 | 表达式 1 or 表达式 2 |
|---|---|---|
| False | False | False |
| False | True | True |
| True | False | True |
| True | True | True |

# 利用短路提高性能